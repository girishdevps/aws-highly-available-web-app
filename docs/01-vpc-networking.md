# AWS VPC Networking — Highly Available Web Application

## 1. Project Overview

This documentation covers the Virtual Private Cloud (VPC) networking foundation for my AWS Highly Available Web Application project.

The objective is to build a logically isolated AWS network with public and private subnets across multiple Availability Zones, establishing the foundation for a highly available and secure application.

**Services covered:**

- Amazon VPC
- IPv4 CIDR addressing
- Public and private subnets
- Internet Gateway (IGW)
- Route tables and subnet associations

## 2. VPC Configuration

| Property                         | Configuration                |
| -------------------------------- | ---------------------------- |
| VPC Name                         | `aws-highavailable-vpc`      |
| IPv4 CIDR                        | `10.0.0.0/16`                |
| AWS Region                       | `ap-south-1` (Mumbai)        |
| Total IPv4 addresses in VPC CIDR | 65,536                       |
| Subnets created                  | 4                            |
| Availability Zones               | `ap-south-1a`, `ap-south-1b` |
| Internet Gateway                 | `aws-highavailable-igw`      |

The `/16` CIDR provides 65,536 total IPv4 addresses for the VPC address range. Each subnet has its own smaller CIDR block.

## 3. Subnet Design

The VPC is divided into four subnets across two Availability Zones.

| Subnet           | CIDR Block     | Availability Zone | Purpose                 |
| ---------------- | -------------- | ----------------- | ----------------------- |
| Public Subnet A  | `10.0.1.0/24`  | `ap-south-1a`     | Public-facing resources |
| Public Subnet B  | `10.0.2.0/24`  | `ap-south-1b`     | Public-facing resources |
| Private Subnet A | `10.0.11.0/24` | `ap-south-1a`     | Backend resources       |
| Private Subnet B | `10.0.12.0/24` | `ap-south-1b`     | Backend resources       |

### Why use multiple subnets?

- Separate public-facing and backend resources.
- Distribute application resources across Availability Zones.
- Establish network boundaries for security.
- Prepare for a multi-AZ architecture using an Application Load Balancer and Auto Scaling Group.

**Important:** A subnet belongs to one Availability Zone. A subnet is considered public when its associated route table provides a route to an Internet Gateway. Actual internet connectivity also depends on IP addressing and security configuration.

## 4. Internet Gateway Configuration

An Internet Gateway named `aws-highavailable-igw` was created and attached to the VPC.

An Internet Gateway enables communication between the VPC and the internet when the necessary routing, IP addressing, and security configuration are in place.

Attaching an Internet Gateway alone does not make every resource in the VPC internet-accessible.

## 5. Route Table Configuration

### Public Route Table

The public route table is named `aws-public-rt`.

| Destination   | Target           | Purpose                                 |
| ------------- | ---------------- | --------------------------------------- |
| `10.0.0.0/16` | `local`          | Communication within the VPC            |
| `0.0.0.0/0`   | Internet Gateway | Route IPv4 traffic towards the internet |

Both public subnets are associated with this route table.

The `0.0.0.0/0` destination represents the default IPv4 route. The Internet Gateway is its target.

### Private Route Table

The private subnets are associated with a separate route table.

The expected initial configuration is:

| Destination   | Target  | Purpose                      |
| ------------- | ------- | ---------------------------- |
| `10.0.0.0/16` | `local` | Communication within the VPC |

Verify the actual routes in the AWS Console before treating this configuration as complete. No direct Internet Gateway route should be added to the private route table.

A NAT Gateway or suitable VPC endpoints may be added later if private workloads require outbound internet or access to supported AWS services.

## 6. Basic Network Architecture

```text
                  AWS Region: ap-south-1
                            |
                  aws-highavailable-vpc
                      10.0.0.0/16
                            |
                     Internet Gateway
                            |
                    Public Route Table
                      /             \
                     /               \
             Public Subnet A    Public Subnet B
             10.0.1.0/24        10.0.2.0/24
             ap-south-1a        ap-south-1b

                    Private Route Table
                      /             \
                     /               \
            Private Subnet A   Private Subnet B
            10.0.11.0/24       10.0.12.0/24
            ap-south-1a        ap-south-1b
```

This diagram represents the intended subnet and routing layout. It does not imply that the load balancer or backend EC2 instances have already been deployed.

## 7. Key Concepts Learned

### VPC

A logically isolated virtual network in AWS where cloud resources can be deployed.

### CIDR

CIDR notation defines an IP address range. For example, `10.0.0.0/16` contains 65,536 total IPv4 addresses.

### Public Subnet

A subnet whose associated route table includes a route to an Internet Gateway. Direct internet connectivity also requires appropriate addressing and security rules.

### Private Subnet

A subnet without a direct route to an Internet Gateway. Private resources can use controlled outbound paths such as a NAT Gateway when required.

### Route Table

A collection of routing rules that determine where traffic is directed.

### Internet Gateway

A VPC component that enables internet connectivity when routing and resource configuration permit it.

### Availability Zone

An isolated location within an AWS Region. Deploying across multiple AZs helps reduce dependence on a single AZ.

## 8. Verification Checklist

- [x] Created a custom VPC with CIDR `10.0.0.0/16`.
- [x] Created two public subnets across two Availability Zones.
- [x] Created two private subnets across two Availability Zones.
- [x] Created and attached an Internet Gateway.
- [x] Configured a public route table with a default route to the Internet Gateway.
- [x] Associated both public subnets with the public route table.
- [x] Verify the private route table has no direct route to the Internet Gateway.
- [x] Capture screenshots of the VPC, subnets, Internet Gateway, and route-table associations.

## 9. Planned Next Steps

The networking foundation will support the following application components:

1. Configure security groups with least-privilege access.
2. Create an internet-facing Application Load Balancer in public subnets.
3. Deploy backend EC2 instances in private subnets.
4. Configure an Auto Scaling Group across multiple Availability Zones.
5. Configure monitoring and alarms using Amazon CloudWatch.
6. Evaluate whether NAT Gateway or VPC endpoints are needed for private resources.

These components will be documented as they are implemented and verified.

## 10. Security and Cost Considerations

- Do not expose backend EC2 instances directly to the public internet unless there is a specific requirement.
- Restrict security-group rules to the required ports and trusted sources.
- Never commit AWS access keys, secret keys, passwords, or other credentials to GitHub.
- Review AWS pricing before creating NAT Gateways, load balancers, or running EC2 instances.
- Delete or stop billable resources when they are no longer required.

## Conclusion

The VPC networking foundation establishes the IP address plan, subnet separation, Internet Gateway attachment, and public routing required for the next stage of the Highly Available Web Application project.

The next stage is to configure security groups and deploy the application networking components while maintaining a clear separation between public entry points and private backend resources.
