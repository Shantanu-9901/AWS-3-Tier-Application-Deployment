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

