# Hands-on Wordpress on RDS: How to Install Wordpress on EC2 Instance and connect to RDS

## Outline

- Part 1 Creating Security Groups

- Part 2 Creating an RDS Instance

- Part 3 Creating an EC2 Instance with bootstrap script that installs Wordpress

- Part 4 Creating Target Group and registering EC2 Instance

- Part 5 Creating Load Balancer

- Part 6 Creating Image from Instance 

- Part 7 Creating Launch Template

- Part 8 Creating Autoscaling Group



## Part 1 - Creating Security Groups

1. Create security group for RDS

Name: RDS-WP-Sec-Gr
Inbound: Type: Mysql/Aurora Port: 3306 Source: Instance-WP-Sec-Gr

2. Create security group for Instances

Name: Instance-WP-Sec-Gr
Inbound: Type: HTTP Port: 80 Source: Anywhere IPv4
         Type: SSH Port: 22 Source: Anywhere IPv4


## Part 2 - Creating RDS Instance

### Create RDS instance with the following parameters:

- Select Standard create as method,
- Engine: MySQL
- Version: 8.5.22
- Templates: Free Tier (For high availability we may choose production, when selected options viewed also change)
Settings
- DB instance identifier: wordpress1
- Master username: admin
- Master password: MyBatch85
- Confirm password: MyBatch85
Instance Configuration
- DB Instance class: Burstable classes db.t2.micro
switch include previous generation classes button to see t2.micro
Storage
- general purpose SSD (gp2)
- 20 GB
- Deselect Enable storage autoscaling
Connectivity
VPC
- Default VPC
Subnet Group
- default
- Public access: No
VPC security group
Choose existing
- RDS-WP-Sec-Gr
Availability Zone
- No preference
Additional configuration
- Database port: 3306
Database authentication
- Password authentication
Additional configuration
- Initial database name: wp
Backup
- Uncheck Enable automated backups
Encryption
- Uncheck
Monitoring
- Uncheck
Maintenance
- Uncheck Enable auto minor version upgrade
Maintenance window
- No preference

Click Create database


## Part 3 - Creating an EC2 Instance with bootstrap script that installs Wordpress

### Launch an instance with the following properties:

- Name and tag: wordpress-server
- Security Group: Instance-WP-Sec-Gr
- Amazon Linux 2
- type: t2.micro
- include keypair
- user data:

#!/bin/bash
yum install httpd php php-mysql -y
amazon-linux-extras install -y php7.2
yum install mariadb-server -y
cd /var/www/html
wget https://wordpress.org/wordpress-5.4.1.tar.gz
tar -xzf wordpress-5.4.1.tar.gz
cp -r wordpress/* /var/www/html/
rm -rf wordpress
rm -rf wordpress-5.4.1.tar.gz
chmod -R 755 wp-content
chown -R apache:apache wp-content
service httpd start
chkconfig httpd on


### After the wordpress instance launches change wordpress configuration:

- In the initial screen of wordpress enter
database name: wp
username: admin
password: MyBatch85
database host: RDS instance endpoint
- Submit and then leave the next screen open
- Then connect to wordpress instance using ssh
- go to /var/www/html
- create wp-config.php file using nano editor

```bash
sudo nano wp-config.php
```
- copy the content of the wordpress screen and paste into the nano editor, wp-config.php
- save and return to wordpress screen
- run the installation and then at the start-up, create wordpress user name, password, e-mail.
- you are ready to start your blog page

## Part 4 Creating Target Group and registering EC2 Instance

1. Go to EC2 dashboard and click Target Groups
2. Create a target group with those settings:
Target type: Instances
Target Group Name: WP-TG
Protocol: HTTP Port: 80
VPC: default
Protocol version: HTTP1
Health checks: default settings
Tags:
Key: Name Value: WP-TG
Register wordpress instance in the second page
Create target group

## Part 5 Creating Load Balancer

### Create an Application Load Balancer
Name: WP-ALB
Scheme: Internet-facing
IP address type: IPv4
Network mapping
default VPC
Mappings: select all AZs
Security Groups: select Instance-WP-Sec-Gr
Choose Listener HTTP:80 with action target group name

Create Load Balancer

As the ALB becomes active, get DNS and paste it in the Wordpress Address (URL) section in the settings of WP. Do not forget to keep http:// at the beginning of the line!!

DNS name can be used to reach wordpress instead of instance IP.


## Part 6 Creating Image from Instance

- Go to Instances and select the wordpress instance and from Actions select image and templates, create image.
- Name it as wordpress-server
- Click create image
- After the image was created in AMIs panel, you can terminate the wordpress instance.

## Part 7 Creating Launch Template

1. Give name as WP-LT and description as wordpress launch template
2. Select Auto Scaling guidance
3. Choose My AMIs, then select wordpress-server AMI
4. Select instance type as t2.micro
5. Select key pair
6. Select existing security group as Instance-WP-Sec-Gr
7. Advanced details, enter user data as:

```bash
#!/bin/bash
yum update -y
```
8. Click create Launch Template

## Part 8 Creating Autoscaling Group

### On EC2 side panel go down to Auto Scaling

1. Click Auto Scaling Groups
2. Create an Auto Scaling Group
3. Name: WP-ASG
4. Choose Launch Template: WP-LT
5. Click Next
6. Choose default VPC
7. Choose all AZs and subnets on the list
8. Click Next
9. For Load balancing select Attach to and existing load balancer
10. Health check type, select ELB
11. Click Next
12. For Group Size, desired 2, minimum 2 and maximum 3
13. For scaling policies, select target tracking scaling policy
14. Metric type: Average CPU Utilization
15. Target value 70
16. Click Next
17. Add tag, Key: Name Value: Wordpress-server
18. Click Next, review and create.

Auto scaling may take some time, check instances provisioning.

### Now, you have built a wordpress website with high availability and a secure database. ALB's DNS can be used to reach the wordpress server. If Route 53 is used, then an FQDN can be used to reach the web site.






**********************

## TASK TO DO
# Create the same structure without RDS, use another instance with MariaDB or MySql insalled.



## EXTRA BONUS TASK TO DO
### This part is to create a wordpress and its database locally. MySql configuration is made manually.


set-up the database locally

install mariadb-server
start mariadb service
mysql_secure_installation (set root password then y to the rest of the questions)
mysql -u root -p (login using root password)
SELECT User, Host, Password FROM mysql.user; (show users)
show databases;
create database wp;
create user 'wp-user'@'localhost' identified by 'wp1234'; (create user at localhost with password)
SELECT User, Host, Password FROM mysql.user; (show newly created user)
GRANT ALL PRIVILEGES ON wp.* to 'wp-user'@'localhost'; (give permission to user on the database)
flush privileges; (make privileges active)
show grants for 'wp-user'@'localhost'; (show privileges)

do wordpress database settings locally

install wordpress
connect to wordpress by localhost
enter the database name, user name, password and host namee
