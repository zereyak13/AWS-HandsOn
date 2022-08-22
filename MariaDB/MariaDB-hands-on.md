## Part 1 - Creating EC2 Instance and Installing MariaDB Server

In this lab, we will complete the following tasks to query a running MariaDB instance. We will need to run create and configure  queries.

1. Launch EC2 Instance.

AMI: Amazon Linux 2
Instance Type: t2.micro
Security Group
  - SSH           -----> 22    -----> Anywhere
  - MYSQL/Aurora  -----> 3306  -----> Anywhere

2. Connect to EC2 instance with SSH.

3. Update yum package management and install MariaDB server.

```bash
sudo yum update -y
sudo yum install mariadb-server -y
```
4. Start MariaDB service.

```bash
sudo systemctl start mariadb
```
5. Check status of MariaDB service.

```bash
sudo systemctl status mariadb
```
6. Enable MariaDB service, so that MariaDB service will be activated on restarts.

```bash
sudo systemctl enable mariadb
```

## Part 2 - Connecting and Configuring MariaDB Database

7.  Connect to the MariaDB Server and open MySQL CLI with root user, no password set as default.

```bash
mysql -u root
```
8. Show default databases in the MariaDB Server.
```bash
SHOW DATABASES;
```
9.  Choose a database (mysql db) to work with. **⚠️ Caution: We have chosen mysql db as demo purposes, normally database mysql is used by server itself, it shouldn't be changed or altered by the user.**

```bash
USE mysql;
```
10. Show tables within the mysql db.
```bash
SHOW TABLES;
```
11. Show users defined in the db server currently.

```bash
describe user;
SELECT Host, User, Password FROM user;
```
12. Close the mysql terminal.

```bash
EXIT;
```
13. Setup secure installation of MariaDB.
    - No root password for root so 'Enter' for first question,
    - Then set root password: `root1234` and yes `y` to all remaining ones.

```bash
sudo mysql_secure_installation
```
14.  Show that you can not log into mysql terminal without password anymore.

```bash
mysql -u root
```
15. Connect to the MariaDB Server and open MySQL CLI with root user and password (pw:root1234).
```bash
mysql -u root -p
```
16.  Show that test db is gone.
```bash
SHOW DATABASES;
```
17. List the users defined in the server and show that it has now password and its encrypted.

```bash
USE mysql;
show tables;
describe user;
SELECT Host, User, Password FROM user;
```
18. Create new database named 'techprodb'.

```bash
CREATE DATABASE techprodb;
```
19. Show newly created database.
```bash
SHOW DATABASES;
```
20. Create a user named 'techprouser'.
```bash
CREATE USER techprouser IDENTIFIED BY 'techpro1234';

```
21. Grant permissions to the user techprouser for database techprodb.

```bash
GRANT ALL ON techprodb.* TO techprouser IDENTIFIED BY 'techpro1234' WITH GRANT OPTION;
```
22. Update privileges.
```bash
FLUSH PRIVILEGES;
# see that the new user is in mysql database user table
SELECT Host, User, Password FROM user;
```
23. Close the mysql terminal.
```bash
EXIT;
```

## Part 3 - Manipulating MariaDB Database


24. Login back as techprouser using the password defined.
mysql -u techprouser -p

25. Show databases for techprouser.
SHOW DATABASES;

26. Select the database techprodb.
USE techprodb;

27. Create a table named offices.

```bash
CREATE TABLE `offices` (
  `office_id` int(11) NOT NULL,
  `address` varchar(50) NOT NULL,
  `city` varchar(50) NOT NULL,
  `state` varchar(50) NOT NULL,
  PRIMARY KEY (`office_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```
28. Insert some data into the table named offices.
```
INSERT INTO `offices` VALUES (1,'03 Reinke Trail','Cincinnati','OH');
INSERT INTO `offices` VALUES (2,'5507 Becker Terrace','New York City','NY');
INSERT INTO `offices` VALUES (3,'54 Northland Court','Richmond','VA');
INSERT INTO `offices` VALUES (4,'08 South Crossing','Cincinnati','OH');
INSERT INTO `offices` VALUES (5,'553 Maple Drive','Minneapolis','MN');
INSERT INTO `offices` VALUES (6,'23 North Plaza','Aurora','CO');
INSERT INTO `offices` VALUES (7,'9658 Wayridge Court','Boise','ID');
INSERT INTO `offices` VALUES (8,'9 Grayhawk Trail','New York City','NY');
INSERT INTO `offices` VALUES (9,'16862 Westend Hill','Knoxville','TN');
INSERT INTO `offices` VALUES (10,'4 Bluestem Parkway','Savannah','GA');
```

29. Create a table named employees.
```bash
CREATE TABLE `employees` (
  `employee_id` int(11) NOT NULL,
  `first_name` varchar(50) NOT NULL,
  `last_name` varchar(50) NOT NULL,
  `job_title` varchar(50) NOT NULL,
  `salary` int(11) NOT NULL,
  `reports_to` int(11) DEFAULT NULL,
  `office_id` int(11) NOT NULL,
  PRIMARY KEY (`employee_id`),
  KEY `fk_employees_offices_idx` (`office_id`),
  CONSTRAINT `fk_employees_offices` FOREIGN KEY (`office_id`) REFERENCES `offices` (`office_id`) ON UPDATE CASCADE) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```
30. Insert some data into the table named employees.
```
INSERT INTO `employees` VALUES (37270,'Yovonnda','Magrannell','Executive Secretary',63996,NULL,10);
INSERT INTO `employees` VALUES (33391,'Darcy','Nortunen','Account Executive',62871,37270,1);
INSERT INTO `employees` VALUES (37851,'Sayer','Matterson','Statistician III',98926,37270,1);
INSERT INTO `employees` VALUES (40448,'Mindy','Crissil','Staff Scientist',94860,37270,1);
INSERT INTO `employees` VALUES (56274,'Keriann','Alloisi','VP Marketing',110150,37270,1);
INSERT INTO `employees` VALUES (63196,'Alaster','Scutchin','Assistant Professor',32179,37270,2);
INSERT INTO `employees` VALUES (67009,'North','de Clerc','VP Product Management',114257,37270,2);
INSERT INTO `employees` VALUES (67370,'Elladine','Rising','Social Worker',96767,37270,2);
INSERT INTO `employees` VALUES (68249,'Nisse','Voysey','Financial Advisor',52832,37270,2);
INSERT INTO `employees` VALUES (72540,'Guthrey','Iacopetti','Office Assistant I',117690,37270,3);
INSERT INTO `employees` VALUES (72913,'Kass','Hefferan','Computer Systems Analyst IV',96401,37270,3);
INSERT INTO `employees` VALUES (75900,'Virge','Goodrum','Information Systems Manager',54578,37270,3);
INSERT INTO `employees` VALUES (76196,'Mirilla','Janowski','Cost Accountant',119241,37270,3);
INSERT INTO `employees` VALUES (80529,'Lynde','Aronson','Junior Executive',77182,37270,4);
INSERT INTO `employees` VALUES (80679,'Mildrid','Sokale','Geologist II',67987,37270,4);
INSERT INTO `employees` VALUES (84791,'Hazel','Tarbert','General Manager',93760,37270,4);
INSERT INTO `employees` VALUES (95213,'Cole','Kesterton','Pharmacist',86119,37270,4);
INSERT INTO `employees` VALUES (96513,'Theresa','Binney','Food Chemist',47354,37270,5);
INSERT INTO `employees` VALUES (98374,'Estrellita','Daleman','Staff Accountant IV',70187,37270,5);
INSERT INTO `employees` VALUES (115357,'Ivy','Fearey','Structural Engineer',92710,37270,5);
```
31. Show newly created tables.

```bash
SHOW TABLES;
```
32. List all records within employees table.

```bash
SELECT * FROM employees;
```
33. List all records within offices table.

```bash
SELECT * FROM offices;
```
34. Filter the first_name, last_name, salary, city, state information of employees having salary more than $100000.

```bash
SELECT first_name, last_name, salary, city, state FROM employees INNER JOIN offices ON employees.office_id=offices.office_id WHERE employees.salary > 100000;
```
35. Close mysql terminal.

```bash
EXIT;
```

## Part 4 - Creating a Client Instance and Connecting to MariaDB Server Instance Remotely

36. Launch EC2 Instance (Ubuntu 20.04) and name it as MariaDB-Client on Ubuntu.

  AMI: Ubuntu 20.04
  Instance Type: t2.micro
  Security Group
    - SSH           -----> 22    -----> Anywhere

37. Connect to EC2 instance with SSH.

38. Update instance.

```bash
sudo apt update && sudo apt upgrade -y
```
39. Install the mariadb-client.

```bash
sudo apt-get install mariadb-client -y
```
40. Connect the techprodb on MariaDB Server on the other EC2 instance (pw:techpro1234).
```bash
mysql -h ec2-3-94-163-77.compute-1.amazonaws.com -u techprouser -p
```
41. Show that techprouser can do same db operations on MariaDB Server instance.

```bash
USE techprodb;
SHOW TABLES;
SELECT * FROM employees;
SELECT * FROM offices;
SELECT first_name, last_name, salary, city, state FROM employees INNER JOIN offices ON employees.office_id=offices.office_id WHERE employees.salary > 100000;
```
42.  Close mysql terminal.
````bash
EXIT;
```
# DO NOT FORGET TO TERMINATE THE INSTANCES YOU CREATED!!!!!!!!!!

Ref: https://mariadb.org/documentation/