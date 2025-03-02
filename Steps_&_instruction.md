# **README: GCP Virtual Machine Setup with Auto-Scaling and Security**

## **Table of Contents**
- [Introduction](#introduction)
- [Prerequisites](#prerequisites)
- [Step 1: Create a Virtual Machine (VM)](#step-1-create-a-virtual-machine-vm)
- [Step 2: Configure Auto-Scaling](#step-2-configure-auto-scaling)
- [Step 3: Implement Security Measures](#step-3-implement-security-measures)
- [Step 4: Validate Auto-Scaling and Security](#step-4-validate-auto-scaling-and-security)
- [Troubleshooting](#troubleshooting)
- [Conclusion](#conclusion)

---

## **Introduction**
This guide provides a step-by-step process to set up a Virtual Machine (VM) in **Google Cloud Platform (GCP)**, configure **Auto-Scaling**, and implement security measures including **IAM roles and firewall rules**. Additionally, it includes procedures to validate the setup.

## **Prerequisites**
- A **Google Cloud Platform (GCP) account** with billing enabled.
- Permissions to create and manage **Compute Engine**, **IAM roles**, and **Firewall rules**.
- Basic knowledge of Linux commands (for testing Auto-Scaling).

---

## **Step 1: Create a Virtual Machine (VM)**

1. **Sign in to GCP Console**: [Google Cloud Console](https://console.cloud.google.com/)
2. **Go to Compute Engine**:
   - Navigate to **Compute Engine** > **VM Instances**.
   - Click **Create Instance**.
3. **Configure the VM**:
   - **Name**: Assign a unique name.
   - **Region & Zone**: Choose a location.
   - **Machine Type**: Select the appropriate machine type.
   - **Boot Disk**: Choose an OS (e.g., Ubuntu, Debian, Windows).
   - **Firewall Settings**: Check **Allow HTTP and HTTPS traffic** (if needed).
4. **Click Create** and wait for the VM to deploy.

---

## **Step 2: Configure Auto-Scaling**

1. **Go to Compute Engine** > **Instance Groups**.
2. Click **Create Instance Group** > **Managed Instance Group**.
3. Configure:
   - **Name**: Provide a name for the group.
   - **Instance Template**: Create a new instance template.
   - **Auto-Scaling**: Enable and set **CPU utilization threshold (e.g., 60%)**.
   - **Min & Max Instances**: Set a range (e.g., **1 to 5**).
4. Click **Create**.
5. **Test Auto-Scaling**:
   - SSH into an instance.
   - Install `stress`:
     ```bash
     sudo apt-get update && sudo apt-get install stress -y
     ```
   - Simulate high CPU load:
     ```bash
     stress --cpu 4 --timeout 300
     ```
   - Monitor **Compute Engine > Instance Groups** for instance scaling.
   - Stop stress test:
     ```bash
     pkill stress
     ```
   - Check if instances scale down.

---

## **Step 3: Implement Security Measures**
### **IAM Role Configuration**
1. **Go to IAM & Admin** > **IAM**.
2. Click **Grant Access**.
3. Enter user email and select a role:
   - **Compute Viewer**: Read-only access.
   - **Compute Admin**: Full access.
4. Click **Save**.
5. **Validate IAM Permissions**:
   - Log in with the restricted user.
   - Try stopping an instance (should be denied).
   - Check **IAM & Admin > Audit Logs** for permission errors.

### **Firewall Rules Configuration**
1. **Go to VPC Network** > **Firewall**.
2. Click **Create Firewall Rule**.
3. Configure:
   - **Name**: Define a unique rule name.
   - **Network**: Select your VPC.
   - **Priority**: Lower numbers = higher priority.
   - **Direction**: Select **Ingress**.
   - **Action**: Choose **Allow** or **Deny**.
   - **Source IP Ranges**: Define allowed source IPs.
   - **Protocols & Ports**: Specify open ports (e.g., TCP: 22, 80, 443).
4. Click **Create**.
5. **Validate Firewall Rules**:
   - Try SSH from a blocked IP (should fail).
   - Access HTTP/HTTPS from allowed sources.
   - Check logs: **Operations > Logging > Logs Explorer**.

---

## **Step 4: Validate Auto-Scaling and Security**
### **Auto-Scaling Validation**
1. **Monitor Scaling in Compute Engine** > **Instance Groups**.
2. Use `stress` to generate load.
3. Confirm instances are added/removed based on CPU usage.
4. Check logs under **Operations > Logs Explorer** (`autoscaler`).

### **IAM Validation**
1. Log in with the test user.
2. Attempt restricted actions (should fail).
3. Verify logs in **IAM & Admin > Audit Logs**.

### **Firewall Rules Validation**
1. **Check Open Ports**:
   ```bash
   nmap -p 22,80,443 <VM_EXTERNAL_IP>
   ```
   - Ensure only allowed ports are open.
2. **Attempt Denied SSH Access**:
   ```bash
   ssh user@your-vm-ip
   ```
   - Should fail if blocked.
3. **Monitor Firewall Logs**:
   - **Operations > Logs Explorer**.
   - Filter logs for **firewall** to see allowed/denied traffic.

---

## **Troubleshooting**
### **Auto-Scaling Issues**
- **Instances Not Scaling Up?**
  - Ensure CPU threshold is set correctly.
  - Check **Logging > Logs Explorer** for `autoscaler` events.

### **IAM Permission Errors**
- Use **IAM & Admin > Audit Logs** to check access attempts.

### **Firewall Access Issues**
- **Can’t connect to SSH?**
  - Ensure port **22** is allowed for your IP.
  - Use GCP **IAP Tunneling** if SSH is blocked.

---

## **Conclusion**
By following this guide, you have:
✅ Set up a **GCP Virtual Machine**.
✅ Configured **Auto-Scaling**.
✅ Implemented **IAM roles and Firewall Rules**.
✅ Validated security and scaling configurations.

Your GCP environment is now **secure and optimized for workload scaling**! 🚀
