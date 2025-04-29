# Week-9 Assignment: AWS Web Application Deployment

## Overview

This project involves deploying the Clarusway Bootcamp website using AWS services. The solution employs Amazon S3 for static asset hosting, an Auto Scaling Group (ASG) for NGINX web servers, and an Application Load Balancer (ALB) for traffic distribution. This architecture ensures high availability, scalability, and efficient resource management.

## Deployment Steps

### Part 1: S3 Setup

1. **Create S3 Bucket**
   - **Bucket Name**: `yourname-clarusway-assets`
   - **Region**: `eu-north-1`
   - **Details**: Use a unique bucket name (replace `yourname` with your actual name) to ensure it does not conflict with existing buckets.

2. **Upload Files**
   - Upload the following files to the S3 bucket:
     - `index.html` (provided)
     - Logo files: `logo.png`, `sda.png`
   - **Details**: These files are essential for rendering the website correctly.

3. **Configure S3**
   - **Static Website Hosting**: Enable this feature to serve the `index.html` file directly.
   - **Bucket Policy**: Set the following policy to allow public read access:

   ```json
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Principal": "*",
         "Action": "s3:GetObject",
         "Resource": "arn:aws:s3:::yourname-clarusway-assets/*"
       }
     ]
   }
   ```
   - **Details**: This policy ensures that anyone can access the static assets hosted in S3.

### Part 2: Auto Scaling Group

1. **Create Launch Template**
   - **User Data Script**:
     ```bash
     #!/bin/bash
     yum update -y
     yum install nginx -y
     systemctl start nginx
     systemctl enable nginx
     aws s3 cp s3://yourname-clarusway-assets/index.html /usr/share/nginx/html/
     ```
   - **Details**: This script installs NGINX, starts the service, and retrieves the `index.html` file from S3 to serve it.

2. **Configure Auto Scaling Group**
   - **Minimum Instances**: 1 (ensures at least one instance is always running).
   - **Maximum Instances**: 3 (limits scaling to three instances to manage costs).
   - **Desired Instances**: 2 (the target number of instances for normal operation).
   - **Health Checks**: Enable EC2 + ELB health checks to maintain availability.
   - **Details**: This setup allows the application to scale based on demand while ensuring reliability.

### Part 3: Application Load Balancer

1. **Create ALB**
   - **Internet-facing**: Configure the ALB to be accessible from the internet.
   - **HTTP Listener**: Set up a listener on port 80 to handle incoming web traffic.
   - **Target Group**: Create a target group for the ASG with health checks on `/` to monitor instance health.
   - **Details**: The ALB efficiently distributes traffic across multiple instances, enhancing performance.

2. **Verification**
   - Access the website via the ALB DNS name to ensure it is reachable.
   - Verify round-robin traffic distribution by making multiple requests to the ALB.
   - **Details**: This ensures that traffic is balanced across the available instances.

## Configuration Files

- **Launch Template**: Include any relevant configuration details or JSON files used in the setup.


1. **S3 for Static Assets**:
   - **Reason**: S3 is a cost-effective solution for hosting static content. It provides high availability and durability for assets like HTML files and images. By using S3, we can easily manage static assets without the overhead of server management.

2. **Auto Scaling Group (ASG)**:
   - **Reason**: ASG allows the application to scale dynamically based on traffic. By setting a minimum and maximum number of instances, we ensure that the application can handle varying loads efficiently. If traffic spikes, additional instances can be launched automatically, and if demand decreases, instances can be terminated to reduce costs.

3. **Application Load Balancer (ALB)**:
   - **Reason**: ALB is designed for distributing incoming application traffic across multiple targets, ensuring no single instance becomes a bottleneck. It also provides features like health checks and SSL termination, enhancing the application’s resilience and security.

4. **Health Checks**:
   - **Reason**: Implementing health checks ensures that if an instance fails, it is automatically replaced, maintaining the application's availability and reliability.

### Conclusion

This architecture combines the strengths of AWS services to create a highly available and scalable web application. It balances cost efficiency and performance, making it suitable for production environments. By leveraging these services, we can ensure that the application remains responsive and resilient under varying loads.
