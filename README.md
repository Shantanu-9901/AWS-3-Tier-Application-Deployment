# AWS-3-Tier-Application-Deployment

## Prerequisite:
    VPC
    Subnets
    Route Table
    Nat Gateway
    Internet Gateway
    RDS
    
## Create VPC 
    Name: VPC-3-tier
    CIDR: 192.168.0.0/16

## Create Subnets
1. Public Subnet 1:
   * Name: Public-Subnet-Nginx
   * CIDR: 192.168.1.0/24

3. Public Subnet 2:
   * Name: Private-Subnet-Tomcat
   * CIDR: 192.168.2.0/24

5. Private Subnet 1:
   * Name:Private-Subnet-Database
   * CIDR: 192.168.3.0/24

7. Private Subnet 2:
   * Name:Public-Subnet-LB
   * CIDR: 192.168.4.0/24
        
## Create Internet Gateway 
- Name: IGW-3-tier
- attach igw to vpc

## Create Nat Gateway 
- Name: NAT-3-tier
- create in public subnet

## Create Route Table 

- RT-Public-Subnet
- add public subnet
- add igw

- RT-Private-Subnet
- add private subnets
- add nat

<img width="610" alt="image" src="https://github.com/user-attachments/assets/81dced12-5009-4fb5-a15f-fc9dc6aaf4c8" />

## Create EC2 Instances 
1. Nginx-Server-Public
   - create in public subnet
   - allow port = 80,22
     
3. Tomcat-Server-Private
   - create in private subnet
   - allow port = 8080,22
     
5. Database-Server-Private
   - create in private subnet
   - allow port = 3306,22
  
## Create Database In Aurora & RDS 
- Go To Aurora & RDS
- Created Database
- Standard create
- Free tier
- DB name – database-1
- Username – admin
- Password – Passwd123$
- VPC – VPC-3-tier
- Connect to Instance -> choose database instance
- Public access – no
- A.Z. – no preference
- Create database
- Edit security group -> Add 3306 port

## Connect To Nginx-Server-Public 
- connect to instance
- change hostname
- create file with name 3-tier-key.pem

      vim 3-tier-key.pem
- copy private key and paste it here

## Now SSH into Database Server
1. Switch to root user
   
        sudo -i
   
3. Install Mariadb

        yum install mariadb105-server -y

4. Start Mariadb

        systemctl start mariadb
  
5. Enable Mariadb
    
        systemctl enable mariadb

## Log in into database
    mysql -h rds-endpoint   -u admin -pPasswd123$
    
Note: replace rds-endpoint with actual endpoint value

1. List down the databases

        show databases;
   
2. Create studentapp database

        create database  studentapp;
   
3. Use studentapp database

        use studentapp;

4. Create a table
 
       CREATE TABLE if not exists students(student_id INT NOT NULL AUTO_INCREMENT,  
    	student_name VARCHAR(100) NOT NULL,  
    	student_addr VARCHAR(100) NOT NULL,   
    	student_age VARCHAR(3) NOT NULL,      
    	student_qual VARCHAR(20) NOT NULL,     
    	student_percent VARCHAR(10) NOT NULL,   
    	student_year_passed VARCHAR(10) NOT NULL,  
    	PRIMARY KEY (student_id)  
        );

5. View the tables

        show tables;

6. Exit Mariadb

        exit
