# Dynamic Café Website on AWS ☕️

## 📌 Overview
This project is based on the **AWS Academy Cloud Foundations Challenge Lab: Creating a Dynamic Website for the Café**.  
The goal was to extend the café’s existing static website by enabling **online ordering** and creating a **multi-region deployment strategy**.

---

## 🎯 Objectives
By completing this lab, I practiced how to:

- Connect to **AWS Cloud9 IDE** on an EC2 instance.  
- Analyze the EC2 instance environment and confirm **web server accessibility**.  
- Deploy a **dynamic PHP web application** backed by **MariaDB**.  
- Use **AWS Secrets Manager** for securely storing application parameters.  
- Create and test a fully functional café ordering system.  
- Build an **Amazon Machine Image (AMI)** from the instance.  
- Deploy the same application in another AWS Region for a **production environment**.  

---

## 🛠️ AWS Services Used
- **Amazon EC2** – hosting the café web application.  
- **AWS Cloud9** – coding and managing files.  
- **Amazon RDS/MariaDB** – relational database backend.  
- **AWS Secrets Manager** – secure storage of application secrets.  
- **Amazon Machine Image (AMI)** – creating reusable server images.  
- **Amazon VPC & Security Groups** – networking and security.  

---

## 🏗️ Final Architecture
- **Development environment** in **us-east-1 (N. Virginia)**.  
- **Production environment** in **us-west-2 (Oregon)**.  
- Each environment runs independently, with its own web server, database, and Secrets Manager configuration.  
- Secrets and app parameters replicated across regions.  

---

## ⚙️ Steps Completed
1. **Configured EC2 & Cloud9** to install Apache, PHP, and MariaDB.  
2. **Deployed café app** and set up test web pages.  
3. **Integrated Secrets Manager** with PHP app to manage DB credentials.  
4. **Tested online ordering system** (menu browsing, order placement, order history).  
5. **Created AMI** of the working instance.  
6. **Launched a new instance in another region** (Oregon) for production.  
7. **Verified application functionality** across both regions.  

---

## 🚀 Key Takeaways
- Learned hands-on how **multi-region deployments** improve resilience and disaster recovery.  
- Gained experience working with **Secrets Manager + PHP SDK integration**.  
- Practiced scaling from **dev → prod** environments on AWS.  

---

  

---

## 🧑‍💻 Author
- **Name:** Shiru  
- **Role:** Student, AWS Academy Cloud Foundations Learner  
- **Focus:** Cloud Computing | Backend Development | AWS Architecture  

---