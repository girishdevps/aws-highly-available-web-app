# aws-highly-available-web-app
AWS project: highly available web application using VPC, EC2, ALB, Auto Scaling, IAM, and CloudWatch.

## AWS VPC Network Architecture
Designed and configured a custom AWS Virtual Private Cloud (VPC) in the Mumbai region (`ap-south-1`) to establish the networking foundation for a highly available web application.
Network Configuration
- **VPC CIDR:** `10.0.0.0/16`
- **Availability Zones:** `ap-south-1a`, `ap-south-1b`
- **Subnets:** 2 public and 2 private subnets
- **Internet Gateway:** Created and attached to the VPC
- **Public Route Table:** Configured with a default route to the Internet Gateway

