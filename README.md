# CloudLaunch-AWS-Project
This project demonstrates the implementation of a secure, scalable AWS infrastructure for CloudLaunch, including static website hosting with S3, IAM user management with permissions, and VPC network design following AWS best practices.

[Static Website](http://altschoolcloudlaunch-site-bucket.s3-website-eu-west-1.amazonaws.com)
[cloud front](d2ct8mo7k18ugr.cloudfront.net)

For Task 1: **Static Website Hosting Using S3 + IAM User with Limited Permissions**
S3 Buckets Created

Bucket 1 For cloudlaunch-site-bucket

Hosts the CloudLaunch static website
Configured for static website hosting
Publicly accessible (read-only for anonymous users)
Contains: index.html, error.html

Bucket 2 For cloudlaunch-private-bucket
1.Private bucket for internal file storage
2.Not publicly accessible
3.IAM user has GetObject and PutObject permissions only

Bucket 3 For cloudlaunch-visible-only-bucket
1.Private bucket visible in listings only
2.IAM user can list the bucket but cannot access contents


**IAM User Configuration:**

User Name: cloudlaunch-user
Access Type: Programmatic access (no console access by default)
Permissions: Custom policy with granular S3 and VPC access

**Static Website Features:**

Fintech mobile webpage done with Html and CSS
Optimized for performance and accessibility

**Task 2: VPC Design for CloudLaunch Environment**
VPC Infrastructure:

VPC Name: cloudlaunch-vpc
CIDR Block: 10.0.0.0/16
Design Philosophy: Multi-tier architecture with layered security

Subnets Created:

Public Subnet (cloudlaunch-public-subnet)

CIDR: 10.0.1.0/24
Purpose: Future load balancers and public-facing services
Internet access via Internet Gateway


Application Subnet (cloudlaunch-app-subnet)

CIDR: 10.0.2.0/24
Purpose: Future application servers
Private subnet (no direct internet access)


Database Subnet (cloudlaunch-db-subnet)

CIDR: 10.0.3.0/28
Purpose: Future RDS and database services
Private subnet with restricted access


Network Components:

Internet Gateway: cloudlaunch-igw (attached to VPC)
Route Tables:

cloudlaunch-public-rt: Routes traffic to IGW
cloudlaunch-app-rt: Local traffic only
cloudlaunch-db-rt: Local traffic only

**Security Groups:**

cloudlaunch-app-sg

Allows HTTP (port 80) access within VPC only
Source: 10.0.0.0/16

cloudlaunch-db-sg

Allows MySQL (port 3306) access from app subnet only
Source: 10.0.2.0/24


JSON POLICY

## IAM Policy Configuration

The cloudlaunch-user has the following combined policy attached:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "ListAllThreeBuckets",
            "Effect": "Allow",
            "Action": "s3:ListBucket",
            "Resource": [
                "arn:aws:s3:::cloudlaunch-site-bucket",
                "arn:aws:s3:::cloudlaunch-private-bucket",
                "arn:aws:s3:::cloudlaunch-visible-only-bucket"
            ]
        },
        {
            "Sid": "ReadSiteBucket",
            "Effect": "Allow",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::cloudlaunch-site-bucket/*"
        },
        {
            "Sid": "ReadWritePrivateBucket",
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:PutObject"
            ],
            "Resource": "arn:aws:s3:::cloudlaunch-private-bucket/*"
        },
        {
            "Sid": "VPCReadOnlyAccess",
            "Effect": "Allow",
            "Action": [
                "ec2:DescribeVpcs",
                "ec2:DescribeSubnets",
                "ec2:DescribeRouteTables",
                "ec2:DescribeInternetGateways",
                "ec2:DescribeSecurityGroups",
                "ec2:DescribeSecurityGroupRules",
                "ec2:DescribeNetworkAcls",
                "ec2:DescribeVpcEndpoints",
                "ec2:DescribeNatGateways",
                "ec2:DescribeVpcPeeringConnections",
                "ec2:DescribeNetworkInterfaces",
                "ec2:DescribeAvailabilityZones",
                "ec2:DescribeRegions"
            ],
            "Resource": "*"
        },
        {
            "Sid": "ListOnlyCloudLaunchVPC",
            "Effect": "Allow",
            "Action": [
                "ec2:DescribeVpcs"
            ],
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "ec2:ResourceTag/Name": "cloudlaunch-vpc"
                }
            }
        }
    ]
}
```

Policy Contains

1. S3 Permissions:

List all three CloudLaunch buckets
Read access to site bucket
Read/Write access to private bucket
No delete permissions anywhere

**2. VPC Permissions:**

Read-only access to all VPC components
Can describe but not modify network resources

**Security Features**
Network Security:

**Zero Trust Architecture**: Private subnets have no internet access
Micro-segmentation: Database subnet only accessible from app subnet
Layered Defense: Multiple security boundaries (subnets, security groups)

**IAM Security:**

**Principle of Least Privilege**: User has minimal required permissions just enough for what is required
Resource-Based Access: Permissions limited to specific CloudLaunch resources
No Administrative Access: Cannot delete or modify critical resources
**
S3 Security:**

**Bucket-Level Permissions:** Granular access control per bucket
Public Access Limited: Only site bucket is publicly readable others are not
No Delete Permissions: Prevents accidental data loss by user

Account-id——mjmarkson (9098-8634-8770)
[Console sign-in URL](https://mjmarkson.signin.aws.amazon.com/console)
User name—————— cloudlaunch-user
Console password—— tmm7M9$_

