# Learning CloudFormation

This repository contains various AWS CloudFormation templates for setting up different AWS resources.

## Project Structure

```
.
├── vpc-setup/         # VPC infrastructure setup
├── s3-bucket/         # Simple S3 bucket creation
├── ec2-instance/      # Standalone EC2 instance setup
├── ec2-in-vpc/        # EC2 instances in VPC (public/private)
└── load-balancer/     # Application Load Balancer setup
```

## Templates Overview

### VPC Setup (`vpc-setup/vpc-setup.yaml`)

- Creates a VPC with public and private subnets
- Sets up Internet Gateway and NAT Gateway
- Configures route tables for both subnets
- CIDR: 10.0.0.0/16
- Public Subnet: 10.0.1.0/24
- Private Subnet: 10.0.2.0/24

### S3 Bucket (`s3-bucket/s3-bucket.yaml`)

- Creates a simple S3 bucket with versioning enabled
- Bucket name follows pattern: my-cloudshell-bucket-${AccountId}-${Region}

### EC2 Instance (`ec2-instance/ec2-instance.yaml`)

- Launches a t2.micro EC2 instance
- Installs and configures Nginx
- Opens ports 22 (SSH), 80 (HTTP), and 443 (HTTPS)
- Uses Amazon Linux 2 AMI

### EC2 in VPC (`ec2-in-vpc/ec2-public-private.yaml`)

- Deploys two EC2 instances (public and private)
- Requires existing VPC infrastructure
- Uses parameters file for VPC, subnet, and key pair values

### Load Balancer (`load-balancer/load-balancer.yaml`)

- Sets up an Application Load Balancer
- Creates target group and listener
- Configures security group for HTTP traffic
- Requires existing VPC and target instances

## Usage

To deploy any template, use the AWS CloudFormation Console or AWS CLI:

```bash
# Using AWS CLI
aws cloudformation create-stack \
  --stack-name <stack-name> \
  --template-body file://<template-path> \
  [--parameters file://<parameters-file>]
```

## Deployment Commands

Deploy the stacks in the following order:

### 1. VPC Stack

```bash
aws cloudformation create-stack \
  --stack-name my-vpc-stack \
  --template-body file://vpc-setup.yaml \
  --capabilities CAPABILITY_NAMED_IAM
```

### 2. S3 Bucket Stack

```bash
aws cloudformation create-stack \
  --stack-name my-s3-stack \
  --template-body file://s3-bucket.yaml \
  --capabilities CAPABILITY_NAMED_IAM
```

### 3. EC2 Instance Stack

```bash
aws cloudformation create-stack \
  --stack-name my-ec2-stack \
  --template-body file://ec2-instance.yaml \
  --parameters ParameterKey=KeyName,ParameterValue=MyCloudKey \
  --capabilities CAPABILITY_NAMED_IAM
```

### 4. EC2 in VPC Stack

```bash
aws cloudformation create-stack \
  --stack-name ec2-in-vpc-stack \
  --template-body file://ec2-public-private.yaml \
  --parameters file://parameters.json \
  --capabilities CAPABILITY_NAMED_IAM
```

### 5. Load Balancer Stack

```bash
aws cloudformation create-stack \
  --stack-name my-loadbalancer-stack \
  --template-body file://load-balancer.yaml \
  --parameters file://parameters.json \
  --capabilities CAPABILITY_NAMED_IAM
```

**Note**: Make sure to:

1. Navigate to the respective directory before running each command
2. Update parameter values in the parameters.json files with your actual values
3. Replace 'MyCloudKey' with your actual EC2 key pair name

## Dependencies

For proper deployment:

1. Deploy VPC stack first (`vpc-setup.yaml`)
2. Use VPC outputs for EC2 in VPC and Load Balancer setups
3. Make sure you have the required key pairs for EC2 instances

## Notes

- All templates are configured for us-east-1 region
- EC2 instances use Amazon Linux 2 AMI
- Security groups are configured with basic settings and should be modified for production use
