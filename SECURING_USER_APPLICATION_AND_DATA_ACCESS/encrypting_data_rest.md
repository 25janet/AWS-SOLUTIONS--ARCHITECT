
# Guided Lab: Encrypting Data at Rest by Using AWS Encryption Options

## Lab Overview
This lab explores **data encryption at rest** in AWS. You review the default encryption in **Amazon S3**, then create and use a **customer managed AWS KMS key** to encrypt **Amazon EBS** volumes. You also test disabling and re-enabling the KMS key, monitor key usage through **AWS CloudTrail**, and review **automatic key rotation*

---

## Objectives Achieved
By completing this lab, I successfully:
- Reviewed the **default server-side encryption (SSE-S3)** for S3 objects.  
- Uploaded and accessed an encrypted S3 object.  
- Created a **KMS customer managed key (CMK)**.  
- Created and attached an **encrypted EBS volume** to an EC2 instance.  
- Disabled and re-enabled the KMS key to observe access impact.  
- Monitored **KMS activity in CloudTrail**.  
- Enabled **automatic key rotation** for the CMK.  

---

## Scenario
At the start, the lab environment included:
- An **S3 bucket** (`ImageBucket`)  
- An **EC2 instance** (`LabInstance`)  
- AWS KMS available for creating a CMK  

By the end of the lab:
- The S3 bucket stored an **object encrypted with SSE-S3**.  
- The EC2 instance had both:  
  - An unencrypted root volume  
  - A **KMS-encrypted EBS data volume**  
- KMS key usage was logged by **CloudTrail**.  

---

## Tasks and Steps

### **Task 1: Reviewing Default Encryption for S3**
- Verified bucket properties → default encryption enabled (**SSE-S3**).  
- Uploaded `clock.png` to S3 with public-read access.  
- Confirmed object encryption = SSE-S3.  
- Accessed image via **Object URL** → decryption handled transparently.  

✅ Objective: S3 default encryption observed and tested.  

---

### **Task 2: Creating an AWS KMS Key**
- Created a **symmetric CMK** with alias `MyKMSKey`.  
- Assigned **voclabs role** as both administrator and user.  
- Verified key available in **Customer managed keys**.  

✅ Objective: CMK created for encrypting/decrypting EBS volumes.  

---

### **Task 3: Creating and Attaching Encrypted EBS Volume**
- Checked **LabInstance root volume** → not encrypted.  
- Created **1 GiB encrypted EBS volume** with `MyKMSKey`.  
- Attached volume to **LabInstance**.  
- Verified:  
  - Root volume = unencrypted.  
  - Data volume = encrypted with KMS key ID.  

✅ Objective: Encrypted EBS volume attached to instance.  

---

### **Task 4: Disabling the KMS Key**
- Disabled `MyKMSKey` in **KMS console**.  
- Detached and attempted to re-attach encrypted volume.  
- Got error:  
  ```
  Volume vol-xxxx cannot be attached. 
  The encrypted volume was unable to access the KMS key.
  ```
- Checked **CloudTrail**:  
  - `DisableKey` event logged.  
  - `AttachVolume` event failed due to disabled KMS key.  
- Re-enabled key → successfully re-attached volume.  

✅ Objective: Confirmed that disabling KMS key blocks encrypted volume access.  

---

### **Task 5: Analyzing AWS KMS Activity with CloudTrail**
- Opened **CloudTrail Event History**.  
- Filtered by `kms.amazonaws.com`.  
- Reviewed key events:  
  - **CreateGrant** → EC2 instance requested permission to decrypt.  
  - **Decrypt** → plaintext key retrieved for volume use.  
  - **GenerateDataKeyWithoutPlaintext** → key generated for EBS.  
  - **RetireGrant** → grant expired.  

✅ Objective: KMS API activity successfully logged and reviewed in CloudTrail.  

---

### **Task 6: Reviewing Key Rotation**
- Opened `MyKMSKey` → enabled **automatic yearly rotation**.  
- Saved configuration.  

✅ Objective: Automatic KMS key rotation scheduled.  

---

## ✅ Conclusion
The lab demonstrated how AWS services provide **secure encryption at rest**:  
- S3 applies **SSE-S3 by default**.  
- KMS-managed encryption secures **EBS volumes**.  
- **CloudTrail** logs every KMS event for auditing.  
- **Key state (enabled/disabled)** directly affects data access.  
- **Key rotation** helps with compliance and long-term security.  

This ensures that sensitive data stored in AWS remains secure, controlled, and auditable.  
