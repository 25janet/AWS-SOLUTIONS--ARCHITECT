# Lab Writeup — Introducing Amazon EFS

**Lab title:** Introducing Amazon Elastic File System (Amazon EFS)  
**File name:** My_First_EFS_File_System.md  
**Author:** *Replace with your name*  
**Date:** *Replace with date of your lab run*  

---

## Overview

This lab demonstrates how to create an Amazon EFS file system, mount it to an Amazon EC2 instance, and measure performance using `fio` and Amazon CloudWatch. The writeup below breaks each task into **what to do**, **how to do it (commands/console actions)**, and **why** each step matters.

---

## Objectives

* Log in to the AWS Management Console  
* Create an EFS file system and mount targets in the Lab VPC  
* Create a security group permitting NFS (TCP 2049) from EFS clients  
* Connect to the EC2 instance via Session Manager  
* Mount the file system on the EC2 instance using the NFS client  
* Run a `fio` write benchmark and collect CloudWatch metrics  

---

## Preconditions

* You are using the lab environment (Lab VPC, Lab EC2 instance) provided by the Guided Lab.  
* Browser pop-ups are allowed (Session Manager opens in a new tab via an InstanceSessionURL).  

---

## Task 1 — Create a security group for EFS mount targets

**Steps**

1. Open **EC2 → Security Groups**.  
2. Copy the existing `EFSClient` security group **Group ID**.  
3. Create a new security group:  
   - Name: `EFS Mount Target`  
   - Description: `Inbound NFS access from EFS clients`  
   - VPC: `Lab VPC`  
4. Add inbound rule:  
   - Type = `NFS (TCP 2049)`  
   - Source = `Custom` → paste the `EFSClient` security group ID.  
5. Create the security group.  

**Why?**  
EFS uses NFS over TCP port 2049. By referencing the `EFSClient` SG as the source, only lab EC2 instances can mount EFS.

**Evidence**  
Screenshot of the new SG with inbound rule TCP 2049 referencing `EFSClient`.

---

## Task 2 — Create the EFS file system

**Steps**

1. Open **EFS console → Create file system → Customize**.  
2. Disable automatic backups (per lab).  
3. Lifecycle management: None.  
4. Add tag: `Name = My First EFS File System`.  
5. Select **Lab VPC**.  
6. For each AZ mount target:  
   - Detach default SG  
   - Attach the `EFS Mount Target` SG  
7. Create file system.  
8. Wait for **File system state = Available** and **Mount targets = Available**.  

**Why?**  
Mount targets must exist in each AZ. Attaching a dedicated SG ensures only allowed clients can mount.

**Evidence**  
- Screenshot of EFS overview with ID + Name tag + Available  
- Screenshot of mount targets showing Available + SG  

---

## Task 3 — Connect to EC2 via Session Manager

**Steps**

1. Copy `InstanceSessionURL` from lab panel.  
2. Paste into a new browser tab → opens Session Manager.  

**Why?**  
Session Manager uses IAM permissions and avoids SSH keys — more secure.  

**Evidence**  
Screenshot of terminal prompt inside Session Manager.  

---

## Task 4 — Install utilities, create mount dir, and mount EFS

**Commands**

```bash
sudo su -l ec2-user
sudo yum install -y amazon-efs-utils
sudo mkdir -p /home/ec2-user/efs
# Copy this mount command from the console:
sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport fs-XXXXXXXX.efs.<region>.amazonaws.com:/ /home/ec2-user/efs
sudo df -hT
