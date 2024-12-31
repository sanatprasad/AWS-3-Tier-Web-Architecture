# AWS Three-Tier Web Architecture Project

A comprehensive guide to setting up a scalable and highly available three-tier web architecture in AWS.

## Architecture Overview
![image](https://github.com/user-attachments/assets/1547a1e0-6783-4170-a0d7-4a4d871a1973)


This project implements a three-tier architecture where:
- A public-facing Application Load Balancer forwards client traffic to web tier EC2 instances
- The web tier runs Nginx web servers serving a React.js website and redirects API calls to the application tier's internal load balancer
- The internal load balancer forwards traffic to the application tier (Node.js), which processes data in an Aurora MySQL Multi-AZ Database

# AWS Three-Tier Architecture with Network Flow

## Entry Point Flow
- **User Request** → **Route 53** → **CloudFront** → **External ALB**
  - **Route 53** handles DNS resolution.
  - **CloudFront** delivers static content and forwards dynamic requests.
  - **External ALB** (Application Load Balancer) distributes traffic to the web servers.

## Web Tier Flow (Public Subnets)
- **External ALB** → **Web Servers** → **Internal ALB**
  - Web servers host **Nginx** and **React.js** application.
  - Located in **public subnets** of each Availability Zone (AZ).
  - Web servers use **NAT Gateway** for outbound internet access.
  - Web servers forward **API requests** to the **Internal ALB**.

## Application Tier Flow (Private Subnets)
- **Internal ALB** → **Application Servers** → **Database**
  - Application servers run **Node.js** on port **4000**.
  - Located in **private subnets**.
  - Process **business logic** and **database operations**.
  - Application tier auto-scales based on demand.

## Database Tier Flow (Private Subnets)
- **Primary DB (AZ-1a)** ⟷ **Replica DB (AZ-1b)**
  - **Aurora MySQL** in **Multi-AZ** configuration.
  - **Synchronous replication** between Availability Zones (AZs).
  - **Automatic failover** capability.
  - **No direct internet access** for the database.

## Monitoring Flow
- **Resources** → **CloudWatch** → **SNS** → **Users**
  - **VPC Flow Logs** are stored in **S3 Bucket**.
  - **API Activities** are monitored through **CloudTrail**.

## High Availability
- Resources deployed across **two AZs (1a and 1b)**.
- **Auto-scaling groups** are configured in each tier.
- **Multi-AZ database** with automatic failover.
- **Redundant NAT Gateways** per AZ for fault tolerance.
  
# Implementation Steps

### 1. Code Setup
1. Clone the repository
2. Review the web-server and app-server codebases

### 2. S3 Bucket Creation
1. Create two S3 buckets:
   - First bucket for web-server and app-server code storage
   - Second bucket for VPC flow logs
2. Upload server code to the first bucket

### 3. IAM Configuration
Create an IAM role with the following policies:
- AmazonS3ReadOnlyAccess
- AmazonSSMManagedInstanceCore

### 4. Network Setup
1. Create VPC with:
   - Public and private subnets
   - Internet Gateway (IGW)
   - NAT Gateway
   - Configured route tables
   - Auto-assign public IP for public subnets
2. Enable VPC Flow Logs pointing to the second S3 bucket

### 5. Security Group Configuration
Configure the following security groups:
- External Load Balancer SG: Allow HTTP (80) from 0.0.0.0/0
- Web Tier SG: Allow HTTP from External Load Balancer SG
- Internal Load Balancer SG: Allow HTTP from Web Tier SG
- App Tier SG: Allow port 4000 from Internal Load Balancer SG
- DB Tier SG: Allow MySQL (3306) from App Tier SG

### 6. Database Setup
1. Create DB Subnet Group
2. Launch RDS Aurora MySQL in Multi-AZ configuration

### 7. Application Tier Configuration
1. Launch and configure test app server
2. Create AMI from configured server
3. Set up:
   - Launch template
   - Target group
   - Internal load balancer
   - Autoscaling group
4. Update nginx.conf with Internal Load Balancer DNS

### 8. Web Tier Configuration
1. Launch and configure test web server with:
   - Nginx
   - Node.js (React.js)
2. Create AMI from configured server
3. Set up:
   - Launch template
   - Target group
   - External load balancer
   - Autoscaling group

### 9. DNS Configuration
Configure Route 53 with External Load Balancer DNS

### 10. Monitoring Setup
1. Configure CloudWatch Alarms
2. Set up SNS notifications

### 11. Logging Configuration
Enable CloudTrail for API and resource tracking

### 12. Cleanup Instructions
1. Remove CloudFront distributions
2. Delete CloudWatch alarms
3. Remove Route 53 records
4. Delete load balancers, target groups, autoscaling groups, and launch templates
5. Delete security groups
6. Delete NAT gateway (wait 5 minutes)
7. Release Elastic IPs
8. Delete VPC
9. Delete RDS subnet group and instance

## AWS Services Used

### Storage
- **S3 (Simple Storage Service)**
  - Stores code and logs
  - Provides versioning and lifecycle management
  - Ensures high durability and availability

### Security
- **IAM (Identity and Access Management)**
  - Manages access control
  - Provides temporary security credentials
  - Integrates with AWS services

### Networking
- **VPC (Virtual Private Cloud)**
  - Isolates network infrastructure
  - Manages routing and subnets
  - Enables secure cloud communication

### Compute
- **EC2 (Elastic Compute Cloud)**
  - Hosts web and application servers
  - Enables custom AMIs
  - Provides autoscaling capabilities

### Load Balancing
- **ALB (Application Load Balancer)**
  - Distributes traffic
  - Performs health checks
  - Supports SSL termination

### Database
- **RDS (Relational Database Service)**
  - Provides managed database service
  - Supports Multi-AZ deployment
  - Includes automated backups

### Monitoring and Logging
- **CloudWatch**
  - Monitors resources
  - Triggers alarms
  - Analyzes logs

- **CloudTrail**
  - Tracks API calls
  - Ensures compliance
  - Maintains event history

### DNS and Routing
- **Route 53**
  - Manages DNS records
  - Provides high availability
  - Supports various routing policies

### Additional Services
- **NAT Gateway**
  - Enables private subnet internet access
  - Provides high availability
  - Simplifies management

- **SNS (Simple Notification Service)**
  - Sends notifications
  - Supports multiple protocols
  - Integrates with monitoring

