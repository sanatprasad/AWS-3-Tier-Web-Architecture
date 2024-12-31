# AWS-3-Tier-Web-Architecture
In this project I have set up a three-tier web architecture in AWS.  I have manually created and configured the network, security, application, and database components necessary to run this architecture in a scalable and highly available manner.
AWS Three-Tier Web Architecture Project

Description

This project provides a hands-on walkthrough of setting up a three-tier web architecture in AWS. Participants will manually create and configure the network, security, application, and database components necessary to run this architecture in a scalable and highly available manner.

Architecture Overview

AWS Architecture

In this architecture:

A public-facing Application Load Balancer forwards client traffic to the web tier EC2 instances.

The web tier runs Nginx web servers serving a React.js website and redirects API calls to the application tier’s internal load balancer.

The internal load balancer forwards traffic to the application tier (Node.js), which processes data in an Aurora MySQL Multi-AZ Database and returns responses to the web tier.

Features

Load Balancing: Ensures traffic is distributed evenly across instances.

Health Checks: Monitors the health of instances.

Autoscaling Groups: Maintains availability by scaling up/down based on traffic.

Project Steps

Step 1: Download Code from GitHub

Clone the repository to your local system.

Review the provided codebase for the web-server and app-server.

Step 2: Create S3 Buckets

Create two S3 buckets:

Bucket 1: Store web-server and app-server code.

Bucket 2: Store VPC flow logs.

Upload the web-server and app-server code from your local system to Bucket 1.

Step 3: Create IAM Role with Policies

Create an IAM role with the following policies:

AmazonS3ReadOnlyAccess

AmazonSSMManagedInstanceCore

Step 4: Set Up Networking Components

Create VPC:

Define subnets (public and private).

Create an Internet Gateway (IGW) and a NAT Gateway (NAT-GW).

Configure route tables to direct traffic appropriately.

Enable auto-assign public IP for public subnets.

Enable VPC Flow Logs:

Send logs to the second S3 bucket created earlier.

Step 5: Configure Security Groups

External Load Balancer SG: Allow HTTP (80) traffic from 0.0.0.0/0.

Web Tier SG: Allow HTTP traffic from the External Load Balancer SG.

Internal Load Balancer SG: Allow HTTP traffic from the Web Tier SG.

App Tier SG: Allow traffic on port 4000 from the Internal Load Balancer SG.

DB Tier SG: Allow MySQL (3306) traffic from the App Tier SG.

Step 6: Set Up Database

Create a DB Subnet Group.

Launch an RDS Aurora MySQL Multi-AZ Instance in the DB subnet group.

Step 7: Configure Application Tier

Launch a test app server and install required packages.

Test connections to other tiers.

Create an AMI of the app server.

Use the AMI to create a launch template.

Configure:

Target group for the application tier.

Internal load balancer.

Autoscaling group.

Edit nginx.conf locally to add the Internal Load Balancer DNS and upload it to the S3 bucket.

Step 8: Configure Web Tier

Launch a test web server and install required packages:

Nginx

Node.js (React.js)

Test connections to other tiers.

Create an AMI of the web server.

Use the AMI to create a launch template.

Configure:

Target group for the web tier.

External load balancer.

Autoscaling group.

Step 9: Set Up DNS with Route 53

Add the External Load Balancer DNS as a record in Route 53.

Step 10: Create Monitoring and Alerts

Set up CloudWatch Alarms to monitor system metrics.

Integrate alarms with SNS for notifications.

Step 11: Enable Logging

Enable CloudTrail to track API calls and resource changes.

Step 12: Clean Up Infrastructure

To delete the entire infrastructure:

Remove CloudFront distributions.

Delete CloudWatch alarms.

Remove Route 53 records.

Delete load balancers, target groups, autoscaling groups, and launch templates.

Delete security groups.

Delete the NAT gateway (wait for 5 minutes).

Release Elastic IPs.

Delete the VPC.

Delete the RDS subnet group and RDS instance.

Services Used and Their Purpose

S3 (Simple Storage Service)

Purpose: Store web-server and app-server code, and VPC flow logs.

Features:

Durable and highly available storage.

Easy integration with other AWS services.

Versioning and lifecycle policies for efficient storage management.

IAM (Identity and Access Management)

Purpose: Create roles with restricted access for resources.

Features:

Fine-grained access control.

Temporary security credentials.

Integration with AWS services.

VPC (Virtual Private Cloud)

Purpose: Isolate network infrastructure and manage routing, subnets, and internet access.

Features:

Subnet creation (public and private).

Customizable route tables.

Secure communication within the cloud.

EC2 (Elastic Compute Cloud)

Purpose: Host web and application servers.

Features:

Scalable virtual servers.

Custom AMIs for consistent environments.

Autoscaling capabilities.

ALB (Application Load Balancer)

Purpose: Distribute traffic to web and application servers.

Features:

Layer 7 load balancing.

Health checks for target groups.

SSL termination support.

RDS (Relational Database Service)

Purpose: Provide a managed, multi-AZ database for persistent storage.

Features:

Automated backups.

High availability with Multi-AZ deployments.

Performance insights for optimization.

CloudWatch

Purpose: Monitor resources and set up alarms.

Features:

Metrics collection and visualization.

Alarms with SNS integration for notifications.

Logs analysis.

CloudTrail

Purpose: Track API calls and resource changes.

Features:

Governance and compliance.

Security auditing.

Event history and filtering.

Route 53

Purpose: Manage DNS records for the architecture.

Features:

Highly available and scalable DNS service.

Integration with AWS services.

Traffic routing policies (e.g., latency-based).

NAT Gateway

Purpose: Enable internet access for resources in private subnets.

Features:

Scalable outbound internet access.

High availability within a single AZ.

Simplified management.

SNS (Simple Notification Service)

Purpose: Send notifications triggered by CloudWatch alarms.

Features:

Push-based messaging.

Multiple delivery protocols (e.g., email, SMS).

Integration with monitoring and event-driven workflows.

Additional Notes

Ensure that you follow best practices for IAM policies and resource tagging.

Use AWS documentation as needed to understand specific services and configurations.

Enjoy building and learning about scalable, reliable AWS architectures!

