# Café Database Migration to Amazon RDS

## Project Overview
This project demonstrates the migration of a **MariaDB database** from a single **Amazon EC2 instance** to a **managed Amazon RDS database**. The café application initially hosted its web server, application code, and database on one EC2 instance. As the business grew, maintaining the database became cumbersome due to patching, backups, and administration requirements.

The goal of this migration was to:
- Improve database durability and reliability.
- Reduce operational overhead and technical learning requirements.
- Enable better scalability and performance for the café web application.

---

## Lab Objectives
1. Create an Amazon RDS instance with MariaDB engine.
2. Connect to the existing EC2-hosted MariaDB database.
3. Export data using `mysqldump`.
4. Import data into the RDS instance.
5. Connect the café web application to the new database and verify.

---

## Technologies and Tools Used
- **AWS Services:** Amazon EC2, Amazon RDS, AWS Secrets Manager, VPC & Subnets, Security Groups, Systems Manager Session Manager  
- **Database:** MariaDB  
- **Web Application:** PHP-based café ordering app  
- **Tools & Commands:** `mysqldump`, MySQL CLI (`mysql -u ...`), Bash commands on EC2

---

## Steps Performed

### 1. Create RDS Instance
Created an RDS MariaDB instance in AWS with the following configuration:  
- DB Identifier: `CafeDatabase`  
- Master Username: `admin`  
- Password: `Caf3DbPassw0rd!`  
- Instance Class: `db.t3.micro`  
- Storage: 20 GiB GP2  
- Public Access: Disabled  
- Security Group: `dbSG`  

---

### 2. Analyze Existing EC2 Deployment
Verified the café application was running on the EC2 public IP, then connected to the instance using Systems Manager Session Manager:
```bash
sudo su
su ec2-user
cd /home/ec2-user/
service mariadb status
mysql --version
Checked the local MariaDB database:

bash
Copy code
mysql -u root -p
show databases;
use cafe_db;
show tables;
select * from `order`;
select * from `order_item`;
exit;
3. Export Data from EC2 MariaDB
Exported the cafe_db database using mysqldump:

bash
Copy code
mysqldump --databases cafe_db -u root -p > CafeDbDump.sql
ls
cat CafeDbDump.sql
4. Import Data into RDS
Imported the backup into the new RDS instance:

bash
Copy code
mysql -u admin -p --host <rds-endpoint> < CafeDbDump.sql
Verified the import:

bash
Copy code
mysql -u admin -p --host <rds-endpoint>
show databases;
use cafe_db;
show tables;
select * from `order`;
exit;
5. Configure Café Application for RDS
Updated Secrets Manager values and environment variables so the application points to the RDS instance:

env
Copy code
DB_HOST=<RDS_ENDPOINT>
DB_USER=admin
DB_PASSWORD=Caf3DbPassw0rd!
DB_NAME=cafe_db
Stopped the local MariaDB service on EC2 to avoid conflicts:

bash
Copy code
sudo service mariadb stop
Finally, tested the café application by placing a new order and checking the Order History to confirm that both previous and new orders were intact.

Lessons Learned
Cloud migration improves availability, scalability, and maintainability.

Amazon RDS reduces operational overhead compared to self-managed MariaDB on EC2.

Using AWS Secrets Manager secures database credentials.

Always verify data integrity after migration.

Systems Manager provides safe, SSH-less access to EC2 instances.

Conclusion
The migration was successful.

An RDS MariaDB instance was created.

Data was exported from EC2 and imported into RDS.

The café application was reconfigured to use RDS.

All functionality and data remained intact.

Local MariaDB on EC2 was shut down.

The café now benefits from a more reliable, scalable, and maintainable backend sy