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



2. Step 4: Update the System

```bash
sudo yum update -y
```

## Step 4: Install Docker and Dependencies

```bash
sudo yum install docker -y
sudo systemctl start docker
sudo systemctl enable docker
```
2. Create and Configure the Dockerfile

Create a `Dockerfile` with the following content:

```Dockerfile
FROM amazonlinux:2023
RUN yum install -y php php-mysqlnd php-gd php-curl php-zip php-mbstring
RUN yum install -y httpd
RUN yum clean all
COPY . /var/www/html
EXPOSE 80
CMD ["httpd", "-D", "FOREGROUND"]
```

Build the Docker image:

```bash
sudo docker build -t prestashop-app .
```

Run the Docker container:

```bash
sudo docker run -d -p 80:80 --name prestashop-container prestashop-app
```

## Step 5: Create an RDS Database

1. Navigate to the AWS RDS Console and click **Create Database**.
2. Select **MySQL** as the database engine.
3. Configure the database settings:
   - **DB Instance Identifier:** `prestashop-db`
   - **Master Username:** `admin`
   - **Master Password:** `securepassword`
   - **DB Instance Class:** `db.t2.micro` (free tier eligible)
   - **Storage:** `20GB` General Purpose SSD
4. Configure connectivity:
   - Connect the RDS instance to the same VPC as the EC2 instance.
   - Make the RDS instance publicly accessible.
   - Create a security group allowing inbound traffic on port `3306` from the EC2 instance’s security group.
5. Create the database.

## Step 6: Connect PrestaShop to the RDS Database

1. Access the PrestaShop installer via `http://<public-ip-of-instance>`.
2. Follow the installation wizard:
   - Select language and agree to terms.
   - Enter the RDS database details:
     - **Database server:** `<RDS-endpoint>`
     - **Database name:** `prestashop`
     - **Database user:** `admin`
     - **Database password:** `securepassword`
   - Configure store details (admin email, password, etc.).
3. Complete the installation and delete the install folder for security:

```bash
sudo docker exec -it prestashop-container rm -rf /var/www/html/install
```
- Link to live installed Prestashop on my instance:
[http://13.48.25.108/]

## Step 7: Monitor and Maintain

- Enable **CloudWatch Logs** to monitor the EC2 instance and RDS database.
- Set up **AWS Backup** for regular RDS database backups.

## Configuration Decisions

- **Instance Type:** Used `t2.micro` to stay within the Free Tier.
- **Security Group:** Opened only necessary ports (`22`, `80`, `443`) for security.
- **Database:** Used **Amazon RDS (MySQL)** for managed database services.
- **Docker:** Containerized the application for easier deployment and scalability.

## Screenshots


1. **EC2 Instance Dashboard** – Showing the running instance.

<img src= "./Screenshot from 2025-03-01 19-27-21.png">


2. **RDS Database Configuration** – Highlighting the database endpoint and security group.
   
<img src= "./Screenshot from 2025-03-01 19-37-29.png">


3. **Dockerfile Content** – Showing the configuration for the PrestaShop application.

<img src= "./Screenshot from 2025-03-01 19-38-01.png">

4. **PrestaShop Installation Wizard** – Showing the RDS database configuration step.

<img src= "./Screenshot from 2025-03-01 19-52-06.png">


<img src="./Screenshot from 2025-03-01 19-52-48.png" size="20%">


## Conclusion

This deployment successfully set up **PrestaShop on AWS** using **Docker** and **RDS**. The use of **Free Tier** resources ensured cost-effectiveness, while Docker simplified the deployment process.

For any questions or further assistance, feel free to reach out!
