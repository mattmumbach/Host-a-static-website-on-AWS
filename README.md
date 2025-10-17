Host a Static Website on AWS

A minimal DevOps project that provisions a highly‑available static web site on Amazon Web Services using core networking, compute, and security services. All infrastructure is defined in the accompanying Terraform/CloudFormation scripts (or manual steps) and the web files live in the linked GitHub repository.

Table of Contents

Project Overview

Architecture Diagram

AWS Resources Deployed

Deployment Steps (EC2 User‑Data)

How to Run the Project

Cleanup

Project Overview

The solution creates a VPC with public and private subnets spread across two Availability Zones (AZs). An Application Load Balancer (ALB) distributes traffic to an Auto Scaling Group (ASG) of EC2 instances that host the static site. The instances reside in private subnets and reach the internet through a NAT Gateway. DNS is managed with Route 53, TLS termination is handled by AWS Certificate Manager, and SNS alerts notify you of ASG activity.

Architecture Diagram
![Alt text](/Host_a_Static_Website_on_AWS_github.png)

AWS Resources Deployed
Component	Purpose
VPC	Isolated network with public & private subnets in two AZs
Internet Gateway	Provides outbound/inbound connectivity for public subnets
NAT Gateway	Allows private‑subnet instances to reach the internet
Security Groups	Network‑level firewall for ALB, EC2, and other services
Application Load Balancer	Terminates TLS, balances traffic across the ASG
Target Group	Registers EC2 instances for the ALB
Auto Scaling Group	Manages EC2 instance count for availability & scaling
EC2 Instance Connect Endpoint	Secure SSH access to instances in both subnets
EC2 Instances (private)	Host the Apache web server serving the static site
Certificate Manager	Provides TLS certificate for the domain
Simple Notification Service (SNS)	Sends alerts on ASG lifecycle events
Route 53	DNS record pointing the custom domain to the ALB
Deployment Steps (EC2 User‑Data)
#!/bin/bash
# Switch to the root user
sudo su

# Update packages
yum update -y

# Install Apache HTTP Server
yum install -y httpd   # Note: the original script had a typo ("htpd")

# Change to the web root
cd /var/www/html

# Install Git
yum install -y git

# Clone the project repository
git clone https://github.com/aosnotes77/host-a-static-website-on-aws.git

# Copy all files (including hidden) to the web root
cp -R host-a-static-website-on-aws/. /var/www/html/

# Clean up the cloned repo
rm -rf host-a-static-website-on-aws

# Enable Apache to start on boot
systemctl enable httpd

# Start Apache
systemctl start httpd
Note: The script assumes the repository URL is reachable and contains the static HTML files. Adjust the URL if necessary.

How to Run the Project
Clone the repository (if you haven’t already)

git clone https://github.com/aosnotes77/host-a-static-website-on-aws.git
cd host-a-static-website-on-aws
Deploy the infrastructure

Update DNS
In Route 53, create an A‑record (alias) that points your domain to the ALB.

Verify
Open your browser and navigate to the domain (or the ALB DNS name). You should see the static site served by Apache.

Cleanup
To avoid ongoing charges, destroy the stack when you’re done:

