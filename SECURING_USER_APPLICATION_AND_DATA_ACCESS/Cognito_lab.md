
## Lab Overview
This lab demonstrates how to secure a web application by using **Amazon Cognito** for authentication and authorization.  
The **Birds application** is enhanced with:
- A Cognito **user pool** to manage users and authentication.
- A Cognito **identity pool** to provide temporary AWS credentials for authorized users to access DynamoDB.



## Objectives Achieved
By completing this lab, I successfully:
- Created an **Amazon Cognito user pool**.  
- Added **users** and **groups** (standard user + administrator).  
- Updated the **Birds application** to use the user pool for authentication.  
- Configured an **Amazon Cognito identity pool** for AWS service authorization.  
- Updated the application to use the identity pool for **temporary AWS credentials**.  
- Verified access control with **role-based pages** and DynamoDB access.  

---

## Tasks and Steps

### **Task 1: Preparing the Lab Environment**
- Connected to **AWS Cloud9 IDE** via the lab-provided URL.  
- Downloaded application files (`wget … code.zip`).  
- Installed and set up the **Birds application** with `setup.sh`.  
- Recorded **S3 bucket name** and **CloudFront domain** for later use.  
- Started the **Node.js server** (`npm start`).  

✅ Objective: Birds application deployed and running.  

---

### **Task 2: Reviewing the Birds Website**
- Opened the Birds app via CloudFront.  
- Verified unprotected pages (Home, Birds info).  
- Tested protected pages (Sightings, Report, SiteAdmin).  
- Confirmed that login was blocked since Cognito was not configured yet.  

✅ Objective: Understood baseline (no authentication).  

---

### **Task 3: Configuring the Cognito User Pool**
- Created **user pool** `bird_app`.  
- Added **app client** `bird_app_client`.  
- Configured **login pages** with Authorization Code + Implicit grant.  
- Set up **Cognito domain prefix**.  
- Created **users** (`testuser`, `admin`).  
- Created **Administrators group** and added `admin`.  

✅ Objective: Cognito User Pool configured with users and roles.  

---

### **Task 4: Updating Application to Use the User Pool**
- Edited `config.js` to add:
  - Cognito domain  
  - User pool ID  
  - App client ID  
  - CloudFront distribution  
- Pushed updated code to S3.  
- Updated `package.json` to include Cognito config.  
- Restarted the **Node.js server**.  

✅ Objective: Application integrated with Cognito authentication.  

---

### **Task 5: Testing User Pool Integration**
- Logged in as `testuser` → gained access to **Sightings** page.  
- Logged in as `admin` → gained access to **SiteAdmin** page.  

✅ Objective: Authentication and role-based access verified.  

---

### **Task 6: Configuring the Cognito Identity Pool**
- Opened **Identity Pool** `bird_app_id_pool`.  
- Linked Cognito user pool + app client.  
- Confirmed **authenticated role** was assigned.  

✅ Objective: Cognito Identity Pool configured for AWS access.  

---

### **Task 7: Updating Application to Use the Identity Pool**
- Edited `config.js` to add **Identity Pool ID**.  
- Edited `auth.js` to pass **User Pool ID** and tokens to Cognito Identity Pool.  
- Pushed updated code to S3.  
- Restarted **Node.js server**.  

✅ Objective: Application updated to request AWS credentials via identity pool.  

---

### **Task 8: Testing Identity Pool Integration**
- Logged in as `admin`.  
- Navigated to **Report** page.  
- Clicked **Validate Temporary AWS Credentials**.  
- Verified message:
  ```
  Your temporary AWS credentials have been configured..
  Connecting to DynamoDB Table..BirdSightings
  Your DynamoDB Table has 0 rows..
  ```  

✅ Objective: Temporary AWS credentials successfully issued and used to query DynamoDB.  

---

## ✅ Conclusion
The **Birds application** is now fully secured with **Amazon Cognito**:
- **User Pool** → Authentication & role-based access.  
- **Identity Pool** → Temporary AWS credentials for DynamoDB access.  

This demonstrates how Cognito simplifies building secure, scalable applications with both authentication and authorization.  
