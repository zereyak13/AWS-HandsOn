# Hands-on VPC-01 : Configuring of VPC

Purpose of the this hands-on training is to create VPC and configure VPC with components.

## Learning Outcomes

At the end of the this hands-on training, students will be able to;

- learn how to create VPC,

- learn how to create subnet,

- learn how to set route tables as public and private,

- learn how to create Bastion host and connect to private subnet from internet

- learn how to create NAT Gateway and NAT Instance

- learn how to create VPC peering between two VPCs

- learn how to create VPC endpoint 

- learn how to use NACL (Network Access List)

## Outline

- Part 1 - Creating VPC, Subnet and Subnet associations

- Part 2 - Creating Bashion Host and connect to the private subnet from internet

- Part 3 - Creating NAT Gateway

- Part 4 - Creating NAT Instance

- Part 5 - Creating VPC peering between two VPCs

- Part 6 - Creating VPC Endpoint

- Part 7 - Configuring NACL 

## Part 1 - Creating VPC, Subnet and Subnet associations

### STEP 1: Create VPC

- First, go to the VPC and select Your VPC section from the left-hand menu, click create VPC.

- create a vpc named 'myVPC' with 172.16.0.0/16 CIDR

```
    - no ipv6 CIDR block
    - tenancy: default
```
- click create

- explain the vpc descriptions

- enable DNS hostnames for the vpc 'myVPC'

  - Select 'myVPC' on VPC console ----> Actions ----> Edit DNS hostnames
  - Click enable flag
  - Click save 

### STEP 2: Create an internet gateway named 'myIGW'

- Go to the Internet Gateways from left hand menu

- Create Internet Gateway
   - Name Tag "myIGW" 
   - Click create button

-  Attach the internet gateway 'myIGW' to the vpc 'myVPC'
```   
    - Actions ---> attach to VPC
    - Select VPC named "myVPC"
    - Push "Attach Internet gateway"
```

### STEP 3 : Configuring Route Table

- Go to the Route Tables from left hand menu

- rename the route table of the vpc 'myVPC' as 'default-rt'

- select Routes on the sub-section

- click edit routes

- click add route

- add a route
    - destination ------> 0.0.0.0/0 (any network, any host)
    - As target;
      - Select Internet Gateway
      - Select 'myIGW'
      - save routes

- explain routes in the default-rt

### STEP 4: Create Subnets
- Go to the Subnets from left hand menu
- Push create subnet button

1. 
Name tag          :az1a-public-subnet
VPC               :myVPC
Availability Zone :us-east-1a
IPv4 CIDR block   :172.16.1.0/24

2. 
Name tag          :az1a-private-subnet
VPC               :myVPC
Availability Zone :us-east-1a
IPv4 CIDR block   :172.16.2.0/24

3. 
Name tag          :az1b-public-subnet
VPC               :myVPC
Availability Zone :us-east-1b
IPv4 CIDR block   :172.16.4.0/24

4. 
Name tag          :az1b-private-subnet
VPC               :myVPC
Availability Zone :us-east-1b
IPv4 CIDR block   :172.16.5.0/24

5. 
Name tag          :az1c-public-subnet
VPC               :myVPC
Availability Zone :us-east-1c
IPv4 CIDR block   :172.16.7.0/24

6. 
Name tag          :az1c-private-subnet
VPC               :myVPC
Availability Zone :us-east-1c
IPv4 CIDR block   :172.16.8.0/24

- explain the subnet descriptions and reserved ips (why 251 instead of 256)

### STEP 5: Route Tables

- Go to the Route Tables from left hand menu

- Select 'default-rt' and click the Subnet Association from sub-section

- show the default subnet associations on the route table 
default-rt (internet access is available even on private subnets)
- push the create route table button

- create a private route table (not allowing access to the internet) 
  - name: 'private-rt'
  - VPC : 'myVPC'
  - click create button

- show the routes in the route table private-rt

- click Subnet association button and show the route table `private-rt` with private subnets

- Click Edit subnet association
- select private subnets;
  - az1a-private-subnet
  - az1b-private-subnet
  - az1c-private-subnet
  - and click save

- create a private route table (not allowing access to the internet) 

- push the create route table button
  - name: 'public-rt'
  - VPC : 'myVPC'
  - click create button

- show the routes in the route table `public-rt`

- click Subnet association button and show the route table 

-Show the default route table subnet association . There are only 3 subnet implicitly.

- public-rt with public subnets

- Click Edit subnet association

- select public subnets;
  - az1a-public-subnet
  - az1b-public-subnet
  - az1c-public-subnet
  - and click save

- select Routes on the sub-section of public-rt

- click edit routes

- click add route

- add a route
    - destination ------> 0.0.0.0/0 (any network, any host)
    - As target;
      - Select Internet Gateway
      - Select 'myIGW'
      - save routes    
      
### STEP 6: enable Auto-Assign Public IPv4 Address for public subnets

- Go to the Subnets from left hand menu

  - Select 'az1a-public-subnet' subnet ---> Action ---> Modify auto-assign IP settings  ---> select 'Enable auto-assign public IPv4 address' ---> Save

  - Select 'az1b-public-subnet' subnet ---> Action ---> Modify auto-assign IP settings  ---> select 'Enable auto-assign public IPv4 address' ---> Save

  - Select 'az1c-public-subnet' subnet ---> Action ---> Modify auto-assign IP settings  ---> select 'Enable auto-assign public IPv4 address' ---> Save

- Create two instances . One is in the Private and the other one is in Public subnet. Show the public and private IPs of instances.


