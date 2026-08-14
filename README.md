# Morpheus Amazon Web Services Plugin

The Morpheus Amazon Web Services (AWS) plugin integrates Morpheus with AWS to provide EC2 instance provisioning, RDS database provisioning, CloudFormation stack provisioning, load balancer management, DNS management via Route 53, object storage via S3, auto-scaling, backup, and cost reporting. The plugin communicates with AWS through the AWS SDK for Java.

## Table of Contents

- [Features](#features)
- [Requirements](#requirements)
- [Repository structure](#repository-structure)
- [Building the plugin](#building-the-plugin)
- [License](#license)
- [Installing](#installing)
- [Detailed Usage Steps](#detailed-usage-steps)
- [API Endpoints](#api-endpoints)

---

## Features

### EC2 Instance Provisioning

Provision, resize, clone, and decommission EC2 virtual machines. Supports selection of region, VPC, subnet, security groups, key pairs, IAM instance profiles, EBS encryption, and image (AMI). Includes console access via Systems Manager.

### RDS Database Provisioning

Provision and manage Amazon RDS database instances directly from Morpheus, including selection of DB subnet groups and instance types.

### CloudFormation Stack Provisioning

Provision infrastructure from CloudFormation templates. Manage stack lifecycle from within Morpheus.

### ALB Load Balancer Management

Create and manage Application Load Balancers (ALB), including target groups and listener rules.

### ELB Load Balancer Management

Create and manage Classic Elastic Load Balancers (ELB).

### Route 53 DNS Management

Create, update, and delete DNS records in AWS Route 53 hosted zones from Morpheus DNS integrations.

### S3 Object Storage

Use AWS S3 buckets as Morpheus storage providers for image storage, backups, and other artifacts.

### Auto Scaling

Integrate with EC2 Auto Scaling groups, enabling scale-out and scale-in policies managed by Morpheus.

### Backup

Back up and restore EC2 instances using the AWS backup provider integration.

### Cost Reporting

Report cloud costs from AWS Cost and Usage Reports (CUR). Configure cost reporting bucket, folder, and report name in cloud settings.

### Cloud Sync

Morpheus synchronises the following AWS resources for inventory and governance:

- EC2 instances and AMIs
- VPCs, subnets, route tables, internet gateways, NAT gateways, VPN gateways, transit gateways and transit gateway VPC attachments, egress-only internet gateways, VPC peering connections
- Security groups
- ALB and ELB load balancers
- EC2 Auto Scaling groups and their members
- EBS volumes and snapshots
- Key pairs, IAM roles, and instance profiles
- Availability zones and regions
- CloudWatch alarms
- Service plans and pricing
- RDS DB subnet groups

---

## Requirements

| Requirement | Version |
|-------------|---------|
| Morpheus | 8.0.0 or later |
| Java | 11 or later |
| Gradle | Use the included Gradle wrapper (`./gradlew`) |

Additional prerequisites:

- An AWS account with programmatic access (Access Key ID and Secret Access Key), or an EC2 instance with an IAM role (host IAM credentials), or an assumed role via STS (Role ARN and optional External ID)
- IAM permissions sufficient for the services in use (EC2, RDS, CloudFormation, ELB/ALB, Route 53, S3, Auto Scaling, Backup, Cost and Usage Reports)
- Network access from the Morpheus appliance to the AWS API endpoints (`*.amazonaws.com`) over HTTPS (port 443)
- For cost reporting: an existing AWS Cost and Usage Report delivered to an S3 bucket

---

## Repository structure

```
src/main/groovy/com/morpheusdata/aws/
├── AWSPlugin.groovy                    - Plugin entry point; registers all providers
├── AWSCloudProvider.groovy             - Cloud provider: sync, credentials, costing
├── EC2ProvisionProvider.groovy         - EC2 instance provisioning
├── RDSProvisionProvider.groovy         - RDS database provisioning
├── CloudFormationProvisionProvider.groovy - CloudFormation stack provisioning
├── ALBLoadBalancerProvider.groovy      - Application Load Balancer management
├── ELBLoadBalancerProvider.groovy      - Classic Load Balancer management
├── AWSNetworkProvider.groovy           - VPC and subnet network management
├── Route53DnsProvider.groovy           - Route 53 DNS management
├── AWSScaleProvider.groovy             - EC2 Auto Scaling integration
├── AWSBackupProvider.groovy            - EC2 backup and restore
├── S3StorageProvider.groovy            - S3 object storage provider
├── AWSIacResourceMappingProvider.groovy - Infrastructure-as-code resource mapping
├── AWSOptionSourceProvider.groovy      - UI option source data (regions, VPCs, etc.)
├── LoadBalancerOptionSourceProvider.groovy - LB-specific option source data
├── sync/                               - Sync tasks (one class per AWS resource type)
└── utils/                              - AWS SDK client factory and shared utilities
src/main/resources/i18n/               - Internationalisation message bundles
src/main/resources/scribe/             - Seed/migration scripts
src/test/groovy/                        - Spock unit and integration tests
build.gradle, gradle.properties         - Build configuration and plugin metadata
```

---

## Building the plugin

Run the following command to compile and package the plugin jar:

```bash
./gradlew clean build
```

The packaged jar will be written to `build/libs/`.

To execute tests, use the following command:

```bash
./gradlew test
```

---

## License

This project is licensed under the Apache License 2.0.

See the [LICENSE](LICENSE) file for details.

---

## Installing

1. Build the plugin (see [Building the plugin](#building-the-plugin)) or download a released jar.
2. In Morpheus, navigate to **Administration > Integrations > Plugins**.
3. Click **Add** and upload the `morpheus-aws-cloud-plugin-<version>.jar` from `build/libs/`.
4. Navigate to **Infrastructure > Clouds** and add a new **Amazon Web Services** cloud, providing AWS credentials and region.

---

## Detailed Usage Steps

### Adding an AWS Cloud

1. Go to **Infrastructure > Clouds > Add**.
2. Select **Amazon Web Services** as the cloud type.
3. Enter a **Name** and select the **Region** (endpoint).
4. Under **Credentials**, choose **Access Key / Secret Key**, **Host IAM Credentials**, or **Assume Role (STS)** and supply the relevant values.
5. Optionally configure **VPC**, **Image Transfer Store** (S3 bucket for image uploads), **EBS Encryption**, **Inventory** level, and **Costing Report** details.
6. Save. Morpheus will validate credentials and begin syncing AWS resources.

### Provisioning an EC2 Instance

1. Go to **Provisioning > Instances > Add**.
2. Select an instance type backed by the AWS cloud.
3. Choose the target **Group**, **Cloud**, **Region**, and **Availability Zone**.
4. Select **Plan** (instance type), **AMI**, **VPC**, **Subnet**, **Security Groups**, and **Key Pair**.
5. Optionally configure **IAM Instance Profile** and **EBS Encryption**.
6. Complete and provision. Morpheus creates the EC2 instance and begins agent installation.

### Provisioning an RDS Database

1. Go to **Provisioning > Instances > Add**, select an RDS-backed instance type.
2. Choose the target AWS cloud, region, and plan (DB instance type).
3. Select the **DB Subnet Group**.
4. Configure database credentials and settings, then provision.

### Provisioning a CloudFormation Stack

1. Go to **Provisioning > Instances > Add**, select a CloudFormation-backed instance type.
2. Provide the CloudFormation template (inline or S3 URL) and supply any parameter values.
3. Provision. Morpheus deploys the stack and tracks the resulting resources.

### Managing Load Balancers

1. Go to **Infrastructure > Load Balancers > Add**.
2. Select **Amazon ALB** or **Amazon ELB** and choose the AWS cloud.
3. Configure the listener, target group, and health check settings.
4. Associate instances with the load balancer via **Actions > Load Balancers** on an instance.

### Configuring Route 53 DNS

1. Go to **Infrastructure > DNS > Add**.
2. Select **Route 53** and choose the AWS cloud.
3. Morpheus syncs hosted zones. DNS records are created automatically when instances are provisioned if DNS is configured on the instance type.

### Configuring S3 Storage

1. Go to **Infrastructure > Storage > Storage Servers > Add**.
2. Select **Amazon S3** and provide the bucket name and credentials.
3. Use this storage server as the backing store for object store volumes or image uploads.

### Setting Up Cost Reporting

1. In AWS, create a Cost and Usage Report delivered to an S3 bucket.
2. When adding or editing the AWS cloud in Morpheus, expand **Costing** and provide the **Costing Report**, **Costing Report Name**, **Costing Folder**, and **Costing Bucket** values.

---

## API Endpoints

This plugin communicates with AWS through the **AWS SDK for Java**. It does not call raw HTTP endpoints directly. The following AWS services are used:

| AWS Service | Purpose |
|-------------|---------|
| EC2 | Instance, AMI, VPC, subnet, security group, key pair, volume, snapshot, auto scaling, gateway sync and provisioning |
| RDS | Database instance provisioning and DB subnet group sync |
| CloudFormation | Stack provisioning and lifecycle management |
| Elastic Load Balancing (ALB/ELB) | Load balancer provisioning and sync |
| Route 53 | DNS record management |
| S3 | Object storage and image transfer |
| STS | Assume-role credential support |
| Systems Manager | EC2 console/session access |
| Cost and Usage Reports / S3 | Cloud cost ingestion |
