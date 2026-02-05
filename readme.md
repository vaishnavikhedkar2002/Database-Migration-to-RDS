# AWS DATABASE MIGRATION FROM EC2 TO AMAZON RDS USING AWS

## **Project Description**

This project demonstrates the complete migration of a MySQL database named myntra from an Amazon EC2 instance(Iaas) to Amazon RDS(Paas) using AWS Database Migration Service.

- To migrate the myntra database from Amazon EC2 to Amazon RDS.

- To use AWS Database Migration Service (DMS) for secure and reliable migration.

- To achieve minimal downtime and zero data loss during migration.

- To improve database performance, scalability, and availability.

- To enable automatic backups, monitoring, and enhanced security.

## **Architecture**

![alt text](<rds/WhatsApp Image 2026-02-04 at 11.17.44 AM.jpeg>)

## **AWS Services Used**

- Amazon EC2 – Used to host the source MySQL database (myntra).

- Amazon RDS (MYSQL/MariaDB)– Used as the target managed MySQL database.

- AWS Identity and Access Management (IAM) – Used to create roles and permissions for AWS DMS.
- Amazon VPC – Used to provide secure network environment for EC2, RDS, and DMS.
- Security Groups – Used to control inbound and outbound traffic (ports 22 & 3306).

## **Step-by-Step Implementation**

### **STEP 1: Launch EC2 Instance (Source DB)**

EC2 → Launch Instance

Select Amazon Linux 2

Instance type: t3.micro

Allow Port 22 (SSH) and 3306 (MySQL)

Instance Name: Traditional-DB

![alt text](<rds/Screenshot (49).png>)

### **STEP 2: Create Amazon RDS Instance (Target Database)**

RDS → Create Database

Engine: MySQL

DB name: myntra

Enable Public Access 

![alt text](<rds/Screenshot (50).png>)

### **STEP 3: Permission for Port 3306**


Edit Inbound Rules.

Add Rule:

Type: MySQL/Aurora (TCP)

Port: 3306

Source: The Public IP of your EC2 instance (or 0.0.0.0/0 for testing, not recommended for production). 


### **STEP 4: Install MySQL on EC2**

This EC2-hosted MySQL database was used to create the project database (myntra) and required tables, enabling data preparation and validation before migrating the database to Amazon RDS (PaaS).


```
sudo yum update -y
sudo yum install mariadb -y
sudo systemctl start mariadb
sudo systemctl enable mariadb
```
![alt text](<rds/Screenshot (51).png>)

### **STEP 5: Create Database (myntra)**

```
mysql -u root -p
CREATE DATABASE myntra
USE myntra;
CREATE TABLE user(id INT,name VARCHAR(10),city VARCHAR(10),age INT);
```

![alt text](<rds/Screenshot (53).png>)

### **STEP 6: Take Database Backup**

A full logical backup of the myntra database was taken from the EC2-hosted MySQL instance to preserve the schema and data before migration. This backup ensured data safety and was later used to restore the database into Amazon RDS.

```
mysqldump -u root -p myntra > myntra_backup.sql
```
![alt text](<rds/Screenshot (54).png>)

### **STEP 7: Connect EC2 to RDS**

The EC2 instance established a connection to the Amazon RDS database using the RDS endpoint, username, and password. Successful connectivity confirmed that network configuration and security group rules on port 3306 were correctly set.

```
sudo mysql -h <RDS-ENDPOINT> -u <RDS-USERNAME> -p
```
![alt text](<rds/Screenshot (54).png>)

### **STEP 8: Restore Data into RDS**

The previously created database backup was restored into the Amazon RDS MariaDB instance to transfer the schema and data. This step completed the migration by populating the RDS database with data from the EC2 source.
```
sudo mysql -h <RDS-ENDPOINT> -u <RDS-USERNAME> -p myntra < myntra_backup.sql
```
![alt text](<rds/Screenshot (55).png>)

### **STEP 9: Verification & Validation**

After restoring, the database and tables on RDS were checked to ensure all data and schema from EC2 were migrated correctly. This verification confirmed the migration was successful and the myntra database was fully operational.
Check Database Presence
```
SHOW DATABASES;
```
Check Tables & Data
```
USE myntra;
SHOW TABLES;
SELECT * FROM User;
```
![alt text](<rds/Screenshot (56).png>)

## **Outcome**

- Database successfully migrated from EC2 (IaaS) to Amazon RDS (PaaS)

- Application data preserved without loss

- Reduced operational overhead

- Improved reliability and availability

## **Conclusion**

The project successfully demonstrated the migration of a MariaDB database from an EC2 instance (IaaS) to Amazon RDS (PaaS). By following structured steps—installation, database creation, backup, connectivity, restoration, and verification—we ensured secure and complete data transfer. Migrating to RDS improved database management, scalability, availability, and reduced operational overhead, providing a reliable and fully managed environment for the myntra database.

