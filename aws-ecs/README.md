# AWS ECS Quick Start Bundle

The AWS ECS Quick Start Bundle provides a streamlined way to rapidly provision all necessary cloud resources to deploy
your ECS-based service on AWS. This bundle specifically focuses on creating infrastructure components essential for
deploying and managing ECS services, ensuring ease of use and quick onboarding for development teams.

The resources defined by this bundle are available in the [template.yaml](template.yaml). Configuration parameters are
managed through [values.yaml](values.yaml).

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

## Bundle Input Values

The following values must be provided or will default as specified in [values.yaml](values.yaml):

| Input Parameter             | Description                                          | Example         | Required/Default |
|-----------------------------|------------------------------------------------------|-----------------|------------------|
| `org`                       | Organization ID on PlantonCloud                      | acmecorp        | Required         |
| `env`                       | Name of your target deployment environment           | dev, staging    | Required         |
| `availability_zone_1`       | AWS Availability Zone for the first subnet           | us-east-1a      | `us-east-1a`     |
| `availability_zone_2`       | AWS Availability Zone for the second subnet          | us-east-1b      | `us-east-1b`     |
| `domain_name`               | Custom domain name managed by Route 53               | example.com     | Required         |
| `load_balancer_domain_name` | Domain associated with the Application Load Balancer | app.example.com | Required         |
| `service_image_repo_name`   | Repository name in ECR for ECS service images        | my-service-repo | Required         |

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

