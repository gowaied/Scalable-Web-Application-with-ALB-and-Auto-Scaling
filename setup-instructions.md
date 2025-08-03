# 🚀 Setup Instructions

This document guides you through the step-by-step deployment process of the Scalable Web Application on AWS using EC2, ALB, Auto Scaling, and optionally RDS.

---

## 📋 Prerequisites

Before you begin, make sure you have the following:

- ✅ An active [AWS account](https://aws.amazon.com/)
- ✅ Basic knowledge of AWS EC2, ALB, and VPC
- ✅ AWS CLI configured with appropriate IAM permissions
- ✅ SSH key pair for accessing EC2 instances
- ✅ A basic web app code (HTML/PHP/Flask/etc.)

---

## 🧱 Step 1: VPC and Subnet Setup

1. Create a new **VPC** (e.g., `10.0.0.0/16`)
2. Create **2 Public Subnets** (e.g., `10.0.1.0/24`, `10.0.2.0/24`) in different Availability Zones
3. Create **2 Private Subnets** (e.g., `10.0.3.0/24`, `10.0.4.0/24`) for database or future use
4. Add an **Internet Gateway** and attach it to your VPC
5. Update the route table for public subnets to route `0.0.0.0/0` to the Internet Gateway

---

## 🧩 Step 2: Create Security Groups

- **ALB Security Group**
  - Inbound: TCP 80 (HTTP), 443 (HTTPS)
  - Outbound: Allow all

- **EC2 Security Group**
  - Inbound: Allow traffic from ALB Security Group (TCP 80)
  - Outbound: Allow all

- **RDS Security Group (optional)**
  - Inbound: TCP 3306 from EC2 Security Group
  - Outbound: Allow all

---

## 💻 Step 3: Launch EC2 Instances

> These will host your web application.

1. Create a **Launch Template** (Amazon Linux 2 or Ubuntu)
2. In the “User Data” section, include commands to:
   - Install web server (Apache/Nginx)
   - Deploy your app (e.g., from GitHub or S3)
   - Example:
     ```bash
     #!/bin/bash
     yum update -y
     yum install -y httpd
     systemctl start httpd
     systemctl enable httpd
     echo "Hello from $(hostname -f)" > /var/www/html/index.html
     ```

---

## ⚖️ Step 4: Create Auto Scaling Group (ASG)

1. Use the launch template you just created
2. Attach it to **2 public subnets** (in different AZs)
3. Set minimum, desired, and maximum instance count (e.g., 2/2/4)
4. Add **scaling policies** (based on CPU usage or request count)
5. Attach the ASG to the ALB (Target Group)

---

## 🌐 Step 5: Create an Application Load Balancer

1. Internet-facing ALB
2. Choose at least 2 public subnets
3. Add **Listener** on port 80 (HTTP)
4. Create a **Target Group** (Instance type, port 80)
5. Register your ASG with the target group

---

## 🛢️ Step 6: (Optional) Deploy Amazon RDS

1. Launch an RDS instance (MySQL or PostgreSQL)
2. Choose **Multi-AZ** deployment
3. Place it inside **private subnets**
4. Attach the **RDS security group**
5. Configure your web app to connect to the database using endpoint, username, and password

---

## 🔒 Step 7: Create IAM Role for EC2 (Optional but Recommended)

1. Go to IAM → Roles → Create Role
2. Choose “EC2” as the trusted entity
3. Attach policies like:
   - `AmazonSSMManagedInstanceCore`
   - `CloudWatchAgentServerPolicy`
4. Attach this role to your EC2 instances or launch template

---

## 📈 Step 8: Enable Monitoring and Alerts

1. Go to **CloudWatch**
2. Create **Alarms** for:
   - CPU Utilization
   - Instance health check failures
3. Create an **SNS Topic**
4. Subscribe your email address
5. Set alarm actions to notify via SNS

---

## 🧪 Step 9: Test Your Application

1. Visit the **DNS name of the ALB**
2. Verify:
   - The load balancer distributes traffic evenly
   - Auto Scaling works (test with load or manually)
   - Alerts are triggered on thresholds
   - Database connectivity (if using RDS)

---

## 🧹 Optional Cleanup

To avoid extra charges, remember to delete:
- EC2 instances or ASG
- ALB
- RDS instance
- VPC and related components

---

## ✅ Summary

You’ve successfully deployed a scalable, highly available web application on AWS using EC2, ALB, and Auto Scaling, with optional monitoring and RDS integration. This architecture follows best practices for security, fault tolerance, and cost efficiency.
