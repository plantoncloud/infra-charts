# AWS ECS Quick Start Bundle

## Overview

The AWS ECS Quick Start Bundle provides a streamlined way to rapidly provision all necessary cloud resources to deploy
your ECS-based service on AWS. This bundle specifically focuses on creating infrastructure components essential for
deploying and managing ECS services, ensuring ease of use and quick onboarding for development teams.

---

## Included Cloud Resources

This ECS Quick Start Bundle creates the following AWS resources:

1. **AWS VPC**:
    - Public subnets across two availability zones
    - DNS hostname and DNS support enabled
    - NAT Gateway enabled for internet access from private subnets

2. **AWS Security Group**:
    - Ingress allowed for HTTP (80), HTTPS (443), and custom port (8080)
    - Egress allowed for all outbound traffic

3. **AWS Route 53 Hosted Zone**:
    - DNS management for specified custom domain

4. **AWS Certificate Manager (ACM) Certificate**:
    - DNS validated SSL certificate for secure ALB communication

5. **AWS Application Load Balancer (ALB)**:
    - Configured with SSL termination and integrated with Route 53 DNS
    - Associated with specified security groups and public subnets

6. **AWS Elastic Container Registry (ECR)**:
    - Container image repository secured with AES256 encryption

7. **AWS ECS Cluster**:
    - Configured for use with AWS Fargate and Fargate Spot

8. **AWS IAM Role (Task Execution Role)**:
    - Grants ECS tasks permissions to interact with ECR and CloudWatch

---

## Bundle Customization

Resources created by this ECS Quick Start Bundle can be customized post-deployment to fit specific requirements.
Individual configurations may be modified directly in your AWS account or through Planton Cloud.

---

## Important Notes

- The ECS Quick Start Bundle is intended for initial provisioning only. Subsequent changes or management of resources
  must be handled individually.
- Verify DNS configurations within Route 53 to ensure seamless ALB and SSL certificate operations.

---

© 2025 Planton Cloud. All rights reserved.
