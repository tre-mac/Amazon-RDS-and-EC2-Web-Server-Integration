# Amazon-RDS-and-EC2-Web-Server-Integration

This project demonstrates how to deploy a full-stack cloud-based web application using Amazon Web Services (AWS). The lab focuses on launching an EC2 instance, creating an Amazon RDS MariaDB database, and enabling secure connectivity between the web server and the database within a Virtual Private Cloud (VPC).

Objectives
- Launch an Amazon EC2 instance running Amazon Linux 2023.
- Create and configure an Amazon RDS MariaDB database.
- Install and configure an Apache web server with PHP.
- Establish secure connectivity between EC2 and RDS within the same VPC.
- Verify database connectivity through a dynamic web application.
- Connect to the database using the MySQL client and validate stored data.

Technologies Used
- AWS EC2 – Virtual server hosting the web application.
- Amazon RDS (MariaDB) – Managed relational database service.
- Amazon VPC – Network isolation and secure connectivity.
- Apache HTTP Server – Web server.
- PHP – Server-side scripting for database interaction.
- MySQL Client – Database verification and management.
- Amazon Linux 2023 – Operating system for the EC2 instance.


# 1. Launching the Amazon EC2 Instance
The first step involved deploying a virtual server to host the web application.

Key Actions:

- Navigated to the AWS Management Console and selected the us-east-1 region.
- Launched an Amazon EC2 instance with the following configuration:
- Name: web-server
- AMI: Amazon Linux 2023
- Instance Type: t3.micro (Free Tier eligible)
- Key Pair: Existing key pair for secure SSH access
- Security Group Rules:
- SSH (Port 22) – Allowed from Anywhere
- HTTP (Port 80) – Allowed from the Internet
- HTTPS (Port 443) – Allowed from the Internet
- Attached a user data script to automatically install and configure the Apache web server with PHP during instance initialization.
- After the instance entered the Running state, the Public IPv4 address was used to verify successful deployment. Navigating to http://<EC2-Public-IP> displayed the default “It works!” page, confirming that the web server was operational


# 2. Creating and Configuring the Amazon RDS MariaDB Instance
The next phase involved deploying a managed relational database and enabling secure connectivity with the EC2 instance.

- Accessed the Amazon RDS console and selected Create Database using the Full Configuration option.
- Configured the database with the following settings:
- Engine: MariaDB
- Engine Version: 11.8.5
- Template: Free Tier
- DB Instance Identifier: IT442-db
- Master Username: IT442_user
- Instance Class: db.t3.micro
- Initial Database Name: sample
- Authentication: Password-based
- Created a custom DB Parameter Group named lab6-DB-PG and modified the parameter:
require_secure_transport set to 0 to allow non-SSL connections for the lab environment.
- In the Connectivity section, selected “Connect to an EC2 compute resource” and chose the previously created web-server instance. This automatically configured the necessary security group rules to allow traffic between EC2 and RDS.
- Disabled automated backups to remain within Free Tier limits.
- After the database status changed to Available, the RDS endpoint and port (3306) were recorded for later use.


# 3. Configuring the Apache Web Server and Database Connectivity
With both compute and database resources in place, the EC2 instance was configured to communicate with the RDS database.

Key Actions:

- Connected to the EC2 instance using EC2 Instance Connect.

- Navigated to the application directory:

cd /var/www/inc

- Created a configuration file named dbinfo.inc to store database connection parameters:

sudo nano dbinfo.inc

- Added the following PHP configuration, replacing placeholders with the actual RDS endpoint and password:

<?php
define('DB_SERVER', 'your-rds-endpoint');
define('DB_USERNAME', 'IT442_user');
define('DB_PASSWORD', 'your-password');
define('DB_DATABASE', 'sample');
?>
- Saved the file and ensured proper permissions so that the Apache web server could access it.

- Verified successful connectivity by navigating to:

http://<EC2-Public-IP>/SamplePage.php

- The page confirmed the database connection and allowed the insertion of employee records into the EMPLOYEES table.

Outcome:
A dynamic web application capable of interacting with a managed MariaDB database.


# 4. Verifying Database Connectivity Using the MySQL Client
To ensure that the application successfully stored data, the database was accessed directly from the EC2 instance.

Key Actions:

- Connected to the MariaDB instance using the MySQL client:

mysql -h <rds-endpoint> -P 3306 -u IT442_user -p

- After entering the password, switched to the target database:

USE sample;

- Verified that records inserted through the web application were successfully stored:

SELECT * FROM EMPLOYEES;
- The returned dataset matched the entries submitted via SamplePage.php, confirming end-to-end connectivity between the web server and the database.
