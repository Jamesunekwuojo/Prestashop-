# PrestaShop Deployment on AWS

This document provides a step-by-step guide for deploying PrestaShop on AWS using Amazon Linux 2023, Docker, and Amazon RDS. The deployment was done using the AWS Free Tier.

---

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Steps](#steps)
   - [Step 1: Sign In as Root User](#step-1-sign-in-as-root-user)
   - [Step 2: Launch an EC2 Instance](#step-2-launch-an-ec2-instance)
   - [Step 3: Connect to the EC2 Instance](#step-3-connect-to-the-ec2-instance)
   - [Step 4: Install Docker and Dependencies](#step-4-install-docker-and-dependencies)
   - [Step 5: Create an RDS Database](#step-5-create-an-rds-database)
   - [Step 6: Connect PrestaShop to the RDS Database](#step-6-connect-prestashop-to-the-rds-database)
   - [Step 7: Monitor and Maintain](#step-7-monitor-and-maintain)
3. [Configuration Decisions](#configuration-decisions)
4. [Screenshots](#screenshots)
5. [Conclusion](#conclusion)

---

## Prerequisites
- An AWS account with root user access.
- AWS Free Tier eligibility to avoid unnecessary charges.
- Basic knowledge of AWS services (EC2, RDS, Docker).

---

## Steps

### Step 1: Sign In as Root User
1. Logged into the AWS Management Console as the **root user**.
2. Verified that all resources created were within the AWS Free Tier limits.

---

### Step 2: Launch an EC2 Instance
1. Navigated to the **EC2 Console** and clicked **Launch Instance**.
2. Selected the **Amazon Linux 2023** AMI.
3. Chose the **t2.micro** instance type (free tier eligible).
4. Configured instance details, enabling **Auto-assign Public IP**.
5. Added the default 8GB SSD storage.
6. Created a new security group with the following rules:
   - **SSH (Port 22)**: Allow access from my IP address.
   - **HTTP (Port 80)**: Allow access from anywhere (`0.0.0.0/0`).
   - **HTTPS (Port 443)**: Allow access from anywhere (`0.0.0.0/0`).
7. Launched the instance and downloaded the key pair (`prestashop-key.pem`).

---

### Step 3: Connect to the EC2 Instance
1. Connected to the instance via SSH:
   ```bash
   ssh -i /path/to/prestashop-key.pem ec2-user@<public-ip-of-instance>
