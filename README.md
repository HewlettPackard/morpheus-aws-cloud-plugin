# Morpheus Amazon Web Services Plugin

This plugin provides a full integration between [Amazon Web Services](https://aws.amazon.com) and [Morpheus](https://morpheusdata.com). It enables cloud inventory sync, EC2 provisioning, EBS snapshot backups, Route 53 DNS, Elastic Load Balancer management, security groups, S3 storage, autoscaling, and AWS cost reporting from within the Morpheus platform.

## 📑 Table of Contents

- [Features](#features)
- [Requirements](#requirements)
- [Installation](#installation)
- [Configuration](#configuration)
- [Building the Plugin](#building-the-plugin)
- [License](#license)

## Features

### Cloud Sync
The following AWS resources are discovered and kept in sync:

- **Regions & Availability Zones**
- **VPCs, Subnets, and Route Tables**
- **EC2 Instances** — running and stopped
- **AMIs** — public and private machine images
- **EBS Volumes & Snapshots**
- **Security Groups**
- **Elastic IPs & Network Interfaces**
- **Key Pairs**
- **IAM Instance Profiles & Roles**

Any additions, updates, and removals in AWS are automatically reflected in Morpheus on the next sync cycle.

### Provisioning
EC2 instances can be provisioned directly from Morpheus using standard instance types and layouts. Supported operations include create, start, stop, restart, resize, and delete, along with multi-NIC and multi-volume configurations and cloud-init guest customization.

### Backups
EBS snapshot-based backups are supported through the Morpheus backup framework — created, listed, and restored from the Morpheus UI.

### Networking & DNS
- **Route 53** integration provides DNS record management (`DNSProvider`).
- AWS networks and subnets are managed through the `NetworkProvider`.

### Load Balancing
Elastic Load Balancers are managed via the `LoadBalancerProvider`, enabling creation and assignment of load balancers to provisioned workloads.

### Security Groups
AWS security groups and rules are managed through the `SecurityGroupProvider`.

### Storage
S3 buckets are managed through the `StorageProvider`, supporting bucket creation and use as a Morpheus storage backend.

### Autoscaling
The `ScaleProvider` integrates with AWS autoscaling for horizontal scaling of workloads.

### Cost Reporting
AWS Cost and Usage Reports are ingested for cloud cost visibility within Morpheus.

### IaC Resource Mapping
The plugin implements `IacResourceMappingProvider`, enabling Morpheus to map Terraform and other IaC-managed resources back to AWS objects for lifecycle management.

## Requirements

| Component | Minimum Version |
|-----------|-----------------|
| Morpheus | 8.0.0 |
| Java (build) | 11 |
| Gradle | Wrapper included (`./gradlew`) |

An AWS account with programmatic access (Access Key / Secret Key) or an EC2 instance role with the appropriate IAM permissions is required.

## Installation

1. Download the latest `.jar` from the [Releases](https://github.com/HewlettPackard/morpheus-aws-cloud-plugin/releases) page, or [build it yourself](#building-the-plugin).
2. In Morpheus, navigate to **Administration → Integrations → Plugins**.
3. Click **Choose File**, select the `.jar`, and upload it.
4. The **Amazon Web Services** cloud type will be available after the plugin loads.

## Configuration

When adding an AWS cloud in Morpheus (**Infrastructure → Clouds → Add Cloud**), the following options are available:

| Field | Description |
|-------|-------------|
| **Access Key** | AWS access key ID (omit when using host IAM credentials) |
| **Secret Key** | AWS secret access key |
| **Use Host IAM Credentials** | Use the EC2 instance role instead of static keys |
| **STS Assume Role / External ID** | Optional cross-account role assumption |
| **Region** | Target AWS region |
| **VPC** | VPC to inventory and provision into |
| **Inventory Level** | Level of resource discovery to perform |
| **EBS Encryption** | Enable EBS volume encryption |
| **Costing** | Optional Cost and Usage Report configuration for cloud cost ingestion |

Credentials can also be stored as a Morpheus [Credential](https://docs.morpheusdata.com/en/latest/administration/credentials/credentials.html) and selected at cloud setup time.

## Building the Plugin

Run the following command to compile and package the plugin jar:

```bash
./gradlew shadowJar
```

The packaged jar will be written to `build/libs/`.

To execute the test suite:

```bash
./gradlew test
```

## License

This project is licensed under the Apache License, Version 2.0. See the [LICENSE](LICENSE) file for details.
