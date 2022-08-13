# Hands-on Nginx Load Balancing with Apache

Purpose of the this hands-on training is to give the students basic knowledge of how to install Nginx as a Load Balancer for Apache Web Servers  on Amazon Linux 2 EC2 instance.

## Learning Outcomes

At the end of the this hands-on training, students will be able to;

- demonstrate their knowledge of how to launch AWS EC2 Instance.

- establish a connection with AWS EC2 Instance with SSH.

- install the Nginx Server on Amazon Linux 2 Instance.

- configure the Nginx Server to do load balancing.

- write a simple bash script to run the Apache Web Server


## Outline

- Part 1 - Create a Security group for Nginx Load Balancer and Apache Servers

- Part 2 - Launching an Amazon Linux 2 EC2 instance and Connect with SSH

- Part 3 - Installing and Configuring Nginx Web Server to do Load Balancing

- Part 4 - Launching Apache Servers with automated script in user data 


## Part 1 - Create Security Group

In the EC2 dashboard, go to Security Group section and create a security group named Nginx-ALB-Sec-Gr with Inbound rules as:
SSH 22, 0.0.0.0/0 anywhere
HTTP 80, 0.0.0.0/0 anywhere

Create another Security Group, named Apache-Server-Sec-Gr
SSH 22, 0.0.0.0/0 anywhere
HTTP 80, source:Nginx-ALB-Sec-Gr


## Part 2 - Launching an Amazon Linux 2 EC2 instance and Connect with SSH

-1.  Launch an Amazon 2 (one for spare)EC2 instance with AMI as `Amazon Linux 2`, instance type as `t2.micro` and assign Nginx-ALB-Sec-Gr security group.

0. Connect to your instance with SSH.


ssh -i [Your Key pair] ec2-user@[Your EC2 IP / DNS name]


## Part 3 - Installing and Configuring Nginx Web Server to Run a Simple Web Page

1. Update the installed packages and package cache on your instance.


sudo yum update -y


2. Install the Nginx Web Server and Git.

```bash
sudo amazon-linux-extras install nginx1.12 -y
sudo yum install git -y
```

3. Start the Nginx Web Server.

```bash
sudo systemctl status nginx
sudo systemctl start nginx
```

4. Check from browser with public IP/DNS

5. Browse to /etc/nginx/conf.d/
	

6.	Create a configuration file using 

```bash
sudo nano load-balancer.conf
```

Copy and paste the code and save it

upstream samplecluster {
  # The upstream elements lists all
  # the backend servers that take part in 
  # the Nginx load balancer example

  server 44.206.235.36;
  server 100.24.42.176;

}
  ### Nginx load balancer example runs on port 80
server {
  listen 80 default_server;
  listen [::]:80 default_server;
  # root /var/www/html;
  server_name _;
  # The proxy_pass setting will also make the
  # Nginx load balancer a reverse proxy
  location / {
    proxy_pass http://samplecluster/;
    try_files $uri $uri/ =404;
  }

} 
  # End of Nginx load balancer and reverse proxy config file

7. Modify nginx.conf file to avoid conflicts.


# For more information on configuration, see:
#   * Official English Documentation: http://nginx.org/en/docs/
#   * Official Russian Documentation: http://nginx.org/ru/docs/

user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log;
pid /run/nginx.pid;

# Load dynamic modules. See /usr/share/doc/nginx/README.dynamic.
include /usr/share/nginx/modules/*.conf;

events {
    worker_connections 1024;
}

http {
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile            on;
    tcp_nopush          on;
    tcp_nodelay         on;
    keepalive_timeout   65;
    types_hash_max_size 4096;

    include             /etc/nginx/mime.types;
    default_type        application/octet-stream;

    # Load modular configuration files from the /etc/nginx/conf.d directory.
    # See http://nginx.org/en/docs/ngx_core_module.html#include
    # for more information.
    include /etc/nginx/conf.d/*.conf;

#    server {
#        listen       80;
#        listen       [::]:80;
#        server_name  _;
#        root         /usr/share/nginx/html;

        # Load configuration files for the default server block.
#        include /etc/nginx/default.d/*.conf;

#        error_page 404 /404.html;
#        location = /404.html {
#        }

#        error_page 500 502 503 504 /50x.html;
#        location = /50x.html {
#        }
#    }

# Settings for a TLS enabled server.
#
#    server {
#        listen       443 ssl http2;
#        listen       [::]:443 ssl http2;
#        server_name  _;
#        root         /usr/share/nginx/html;
#
#        ssl_certificate "/etc/pki/nginx/server.crt";
#        ssl_certificate_key "/etc/pki/nginx/private/server.key";
#        ssl_session_cache shared:SSL:1m;
#        ssl_session_timeout  10m;
#        ssl_ciphers HIGH:!aNULL:!MD5;
#        ssl_prefer_server_ciphers on;
#
#        # Load configuration files for the default server block.
#        include /etc/nginx/default.d/*.conf;
#
#        error_page 404 /404.html;
#            location = /40x.html {
#        }
#
#        error_page 500 502 503 504 /50x.html;
#            location = /50x.html {
#        }
#	}

}

# end of nginx.conf file


8. test if everything is ok

```bash
sudo nginx -t
```


9. restart nginx

```bash
sudo systemctl restart nginx
```


5. configure to start while launching
```bash
sudo systemctl enable nginx
```

## Part 4 - Automation of Apache Web Servers Installation through Bash Script (User data)

11. Configure an Amazon EC2 instance with AMI as `Amazon Linux 2`, instance type as `t2.micro`, Apache-Server-Sec-Gr security group which allows connections from Nginx-ALB.

12. Configure to launch 2 instances to automate web server installation with `user data` script.


#####
#!/bin/bash
#update os
yum update -y
#install apache server
yum install -y httpd
# get private ip address of ec2 instance using instance metadata
TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` \
&& PRIVATE_IP=`curl -H "X-aws-ec2-metadata-token: $TOKEN" http://169.254.169.254/latest/meta-data/local-ipv4`
# get public ip address of ec2 instance using instance metadata
TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` \
&& PUBLIC_IP=`curl -H "X-aws-ec2-metadata-token: $TOKEN" http://169.254.169.254/latest/meta-data/public-ipv4` 
# get date and time of server
DATE_TIME=`date`
# set all permissions
chmod -R 777 /var/www/html
# create a custom index.html file
echo "<html>
<head>
    <title> Congratulations! You have created an instance from Launch Template</title>
</head>
<body>
    <h1>This web server is launched from launch template by Batch85 Devops Team</h1>
    <p>This instance is created at <b>$DATE_TIME</b></p>
    <p>Private IP address of this instance is <b>$PRIVATE_IP</b></p>
    <p>Public IP address of this instance is <b>$PUBLIC_IP</b></p>
</body>
</html>" > /var/www/html/index.html
# start apache server
systemctl start httpd
systemctl enable httpd

####


13. Review and launch the EC2 Instances.

14. Once Instances are on, get the IP of Nginx Load Balancer and check if the Nginx Web Server is working from the web browser.

15. Refresh the page and see how the ip changes from Apache1 to Apache2.


