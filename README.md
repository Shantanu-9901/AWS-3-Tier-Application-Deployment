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

<img width="846" alt="image" src="https://github.com/user-attachments/assets/eb7a244c-870b-46f9-91cc-6e5dbea3417d" />

  
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

<img width="842" alt="image" src="https://github.com/user-attachments/assets/645313c9-7982-4f84-9e33-8ea32d958ff3" />


## Connect To Nginx-Server-Public
<img width="728" alt="image" src="https://github.com/user-attachments/assets/e14b315b-b154-478c-9c65-c4ea0ad168ca" />

- connect to instance
- change hostname
- create file with name 3-tier-key.pem

      vim 3-tier-key.pem
- copy private key and paste it here

## Now SSH into Database Server
<img width="730" alt="image" src="https://github.com/user-attachments/assets/804934ab-c86e-4988-95e3-4da7aca4f29d" />

1. Switch to root user
   
        sudo -i
   
3. Install Mariadb

        yum install mariadb105-server -y

4. Start Mariadb

        systemctl start mariadb
  
5. Enable Mariadb
    
        systemctl enable mariadb

## Log in into database
<img width="834" alt="image" src="https://github.com/user-attachments/assets/8c4b14d7-405c-46ec-bc0c-8ca2d8383da0" />

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
### Back to nginx-server

## Now SSH into Tomcat Server
<img width="718" alt="image" src="https://github.com/user-attachments/assets/585a9057-55c4-4b3d-8a5c-b8e9864c5e7a" />

1. Connect to Tomcat Server

        ssh -i 3-tier-key.pem ec2-user@ip-of-tomcat-vm

2. Switch to root user

        sudo -i
   
3. Install Java

        yum install java -y
   
4. Create a directory

        mkdir /opt/tomcat
   
5. Download Tomcat tar file

        curl -O https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.90/bin/apache-tomcat-9.0.90.tar.gz
   
7. Extract the Tomcat tar file at /opt/tomcat location

        tar -xzvf apache-tomcat-9.0.90.tar.gz -C /opt/tomcat

8. Move to the following location

        cd /opt/tomcat/apache-tomcat-9.0.90.tar.gz/webapps
   
9. Download the student.war file (Application)

        curl -O https://s3-us-west-2.amazonaws.com/studentapi-cit/student.war

10. Move to ../lib location

        cd ../lib

11. Download the MySQL Connector

        curl -O https://s3-us-west-2.amazonaws.com/studentapi-cit/mysql-connector.jar

##  MODIFY context.xml
1. Move to the respective location

        cd apache-tomcat-9.0.90.tar.gz/conf

2. Open Context.xml in vim editor

        vim context.xml

3. Add below line [connection string] at line 21

         <Resource name="jdbc/TestDB" auth="Container" type="javax.sql.DataSource"
                       maxTotal="100" maxIdle="30" maxWaitMillis="10000"
                       username="USERNAME" password="PASSWORD" driverClassName="com.mysql.jdbc.Driver"
                       url="jdbc:mysql://DB-ENDPOINT:3306/DATABASE-NAME"/>
    <img width="858" alt="image" src="https://github.com/user-attachments/assets/51d70c13-5c2d-4667-b58c-78e897808d97" />


4. Move to bin

        cd ../bin

5. Modifying Permissions

        chmod +x catalina.sh

6. Start Catalina.sh

        ./catalina.sh start

7. Exit the Tomcat Server

       exit

## Back to Nginx - Server
1. Install Nginx Server

        sudo yum install nginx -y

2. Open nginx.conf file

        sudo vim /etc/nginx/nginx.conf

3. :set nu (enter below data in line 47 in between error and location)

        location / {
        proxy_pass http://private-IP-tomcat:8080/student/;
        }
   <img width="741" alt="image" src="https://github.com/user-attachments/assets/e6f2d07b-7fa1-40d7-adb0-7476a84a471e" />


5. Start Nginx Server

        sudo systemctl start nginx

# Go To Browser Hit Public-IP of Nginx Server
<img width="465" alt="image" src="https://github.com/user-attachments/assets/4b736c5b-9d0c-4e3e-8b4b-dee4d508c51e" />

