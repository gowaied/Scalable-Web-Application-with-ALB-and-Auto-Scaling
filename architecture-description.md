# 🧱 Architecture Description

## 📌 Project Title
**Scalable Web Application on AWS using EC2, ALB, and Auto Scaling**

---

## 🧠 Overview

This document provides a detailed explanation of the architecture design and components used to build a highly available, scalable, and fault-tolerant web application on Amazon Web Services (AWS). The application is hosted on EC2 instances behind an Application Load Balancer (ALB), automatically scaled by an Auto Scaling Group (ASG), monitored by Amazon CloudWatch, and optionally backed by Amazon RDS for persistent data storage.

---

## 🖼️ Solution Architecture Diagram

> See the [architecture diagram](./Architecture_Diagram.png) for a visual representation of the components described here.

---

## 🏗️ Architecture Components and Design Rationale

### 1. **Virtual Private Cloud (VPC)**
- A logically isolated section of the AWS cloud.
- Divided into multiple **subnets** across different **Availability Zones (AZs)** for fault tolerance.
- Includes **public subnets** (for load balancer and EC2s) and **private subnets** (for database).

---

### 2. **Internet Gateway**
- Allows inbound HTTP/HTTPS traffic from the internet to the **Application Load Balancer (ALB)**.
- Connected to the public subnets of the VPC.

---

### 3. **Application Load Balancer (ALB)**
- Distributes incoming HTTP/HTTPS requests across EC2 instances.
- Improves fault tolerance and performance by routing traffic to healthy targets in different AZs.
- Operates at Layer 7 (Application Layer) and supports features like URL-based routing and SSL termination.

---

### 4. **Auto Scaling Group (ASG)**
- Automatically adjusts the number of EC2 instances based on demand (e.g., CPU utilization or request count).
- Ensures high availability and cost optimization by scaling in/out dynamically.
- Spread across multiple **Availability Zones** for redundancy.

---

### 5. **Amazon EC2 Instances**
- Host the actual web application (e.g., Flask, Node.js, static website, etc.).
- Placed in **public subnets** to allow direct traffic from ALB.
- Bootstrapped during launch using a custom AMI or user data scripts.

---

### 6. **Amazon RDS**
- Serves as the backend relational database.
- **Multi-AZ deployment** ensures high availability and automatic failover.
- Located in **private subnets** to enhance security (not exposed to internet).
- Integrated with EC2 instances over the VPC’s internal network.

---

### 7. **Security Groups**
- Act as virtual firewalls:
  - ALB SG allows inbound traffic from the internet (e.g., port 80/443).
  - EC2 SG allows traffic only from the ALB.
  - RDS SG allows traffic only from EC2 instances.

---

### 8. **IAM Roles**
- EC2 instances and other services use IAM roles to securely access AWS resources (e.g., CloudWatch Logs, S3, SNS).
- Least privilege principle applied for security.

---

### 9. **Amazon CloudWatch & SNS**
- **CloudWatch** monitors performance metrics (CPU, memory, etc.) and collects logs.
- **Alarms** trigger **SNS notifications** for critical events like instance failures or high CPU usage.
- Enables proactive monitoring and alerting.

---

## 🔐 Security Considerations

- **Least privilege** access with IAM roles.
- EC2s and RDS are placed in isolated subnets.
- **No direct SSH access** recommended (use Systems Manager Session Manager or bastion host if needed).
- HTTPS termination at the ALB (with optional SSL certificate via ACM).

---

## 🧩 Design Benefits

| Benefit             | Description |
|--------------------|-------------|
| **High Availability** | Multiple AZs, ALB, ASG, and RDS Multi-AZ ensure service continuity. |
| **Scalability**         | Auto Scaling adjusts capacity based on real-time demand. |
| **Security**            | VPC isolation, security groups, IAM roles, and private subnets protect data and services. |
| **Cost Optimization**   | EC2 scaling based on actual load avoids over-provisioning. |
| **Observability**       | CloudWatch + SNS provide visibility and alerts for proactive maintenance. |

---

## 🧪 Testing and Validation

- **Load Testing**: Simulated traffic tested Auto Scaling triggers and ALB routing.
- **Failover Test**: EC2 failure triggered ASG replacement; RDS Multi-AZ failover tested.
- **Monitoring**: Custom metrics and alarms configured in CloudWatch.

---

## ✅ Conclusion

This architecture represents a foundational pattern for deploying scalable, resilient web applications on AWS using core services. It can serve as a base for more advanced deployments, such as integrating CI/CD pipelines, WAF, or containerization in the future.

