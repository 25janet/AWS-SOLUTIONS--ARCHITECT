
This lab demonstrates how to build and secure a VPC environment with **public/private subnets, bastion host, NAT gateway, and NACLs**.

---

## 🎯 Objectives
- Create a custom VPC with public and private subnets  
- Attach Internet Gateway for public access  
- Deploy Bastion Host for secure administration  
- Configure NAT Gateway for private outbound internet  
- Enforce security with NACLs and Security Groups  
- Test connectivity using ICMP  

---
## 🏗️ Architecture

```text
                         ┌─────────────────────────┐
                         │        Internet         │
                         └────────────┬────────────┘
                                      │
                         ┌────────────▼────────────┐
                         │   Internet Gateway (IGW)│
                         └────────────┬────────────┘
                                      │
        ┌─────────────────────────────┼─────────────────────────────┐
        │                             │                             │
        │               Public Subnet │ 10.0.0.0/24                  │
        │                             │                             │
        │  ┌───────────────────────┐  │  ┌───────────────────────┐  │
        │  │ Bastion Host (EC2)    │  │  │  NAT Gateway          │  │
        │  │ SG: SSH from MyIP     │  │  │ EIP attached          │  │
        │  └───────────────────────┘  │  └───────────────────────┘  │
        │                             │                             │
        └─────────────────────────────┼─────────────────────────────┘
                                      │
                         ┌────────────▼────────────┐
                         │     Private Subnet      │ 10.0.1.0/24
                         │ (Associated with NACL)  │
                         └────────────┬────────────┘
                                      │
                         ┌────────────▼────────────┐
                         │ Private Instance (EC2) │
                         │ SG: SSH from Bastion   │
                         └─────────────────────────┘

## 📝 Tasks and Steps

---

### ✅ Task 1: Create VPC and Subnets
**Objective:** Build the base network for public and private workloads.  

**Steps Taken:**
1. Create **Lab VPC** with CIDR `10.0.0.0/16`.  
2. Create two subnets:  
   - Public Subnet: `10.0.0.0/24`  
   - Private Subnet: `10.0.1.0/24`  

---

### ✅ Task 2: Configure Internet Gateway
**Objective:** Allow resources in the public subnet to connect to the internet.  

**Steps Taken:**
1. Create an **Internet Gateway (IGW)**.  
2. Attach IGW to **Lab VPC**.  
3. Update Public Route Table:  
```

Destination: 0.0.0.0/0
Target: Internet Gateway

````

---

### ✅ Task 3: Launch Bastion Host
**Objective:** Provide secure SSH access to private instances.  

**Steps Taken:**
1. Launch EC2 instance with:  
- AMI: Amazon Linux 2023  
- Type: t2.micro  
- Subnet: Public Subnet  
- Key Pair: vockey.pem  
- Security Group: Allow SSH (22) from My IP  

**Command to connect:**  
```bash
ssh -i vockey.pem ec2-user@<bastion-public-ip>
````

---

### ✅ Task 4: Create NAT Gateway

**Objective:** Enable outbound internet access from private subnet resources.

**Steps Taken:**

1. Allocate an Elastic IP.
2. Create a NAT Gateway in the **Public Subnet**.
3. Update **Private Route Table**:

   ```
   Destination: 0.0.0.0/0
   Target: NAT Gateway
   ```

---

### ✅ Task 5: Launch Private Instance

**Objective:** Deploy a workload in the private subnet.

**Steps Taken:**

1. Launch EC2 instance with:

   * AMI: Amazon Linux 2023
   * Type: t2.micro
   * Subnet: Private Subnet
   * Key Pair: vockey2.pem
   * Security Group: Allow SSH (22) from BastionHost-SG

---

### ✅ Task 6: SSH Passthrough to Private Instance

**Objective:** Connect to the private instance using the Bastion Host.

**Steps Taken:**

1. Add both key pairs to SSH agent.

   ```bash
   ssh-add vockey.pem
   ssh-add vockey2.pem
   ```
2. Connect to Bastion Host with agent forwarding:

   ```bash
   ssh -A ec2-user@<bastion-public-ip>
   ```
3. From Bastion, connect to Private Instance:

   ```bash
   ssh ec2-user@<private-instance-ip>
   ```

---

### ✅ Task 7: Test Internet Access

**Objective:** Verify that private subnet instances access the internet via NAT Gateway.

**Command:**

```bash
ping 8.8.8.8
```

---

### ✅ Task 8: Configure Network ACL

**Objective:** Add another layer of stateless filtering on the private subnet.

**Steps Taken:**

1. Create a custom NACL.
2. Associate NACL with the **Private Subnet**.
3. Rules:

   * Inbound: Allow all from `10.0.0.0/16`
   * Outbound: Allow all to `10.0.0.0/16`

---

### ✅ Task 9: Test ACL with ICMP

**Objective:** Prove that NACLs can block specific traffic (stateless filtering).

**Steps Taken:**

1. Launch a **Test Instance** in the Public Subnet with SG allowing All ICMP IPv4.
2. From Private Instance:

   ```bash
   ping <test-instance-private-ip>
   ```

   ✅ Ping works.
3. Modify NACL to **deny ICMP** from `10.0.1.0/24` → `<test-instance-ip>/32`.
4. Test again:

   ```bash
   ping <test-instance-private-ip>
   ```

   🚫 Ping blocked (ACL enforcement proven).

---

## 📊 Key Learnings

* IGW = inbound/outbound internet for public subnet.
* NAT Gateway = outbound-only internet for private subnet.
* Bastion Host = secure bridge into private resources.
* Security Groups = stateful; NACLs = stateless.
* Layered defenses (SG + NACL) improve security posture.

```

---

