- [Create AWS Account](#create-aws-account)
- [How to Choose an AWS Region](#how-to-choose-an-aws-region)
- [AWS Availability Zones (AZs)](#aws-availability-zones-azs)
- [AWS Points of Presence (Edge Locations)](#aws-points-of-presence-edge-locations)
- [AWS Identity and Access Management (IAM)](#aws-identity-and-access-management-iam)
- [IAM Policies Structure](#iam-policies-structure)
- [AWS CLI](#aws-cli)
  - [Installation](#installation)
  - [CLI Usage](#cli-usage)
  - [Cloud Shell](#cloud-shell)
- [Amazon EC2 (Elastic Compute Cloud)](#amazon-ec2-elastic-compute-cloud)
  - [Core Concept](#core-concept)
  - [Main Components Around EC2](#main-components-around-ec2)
  - [EC2 Sizing \& Configuration Options](#ec2-sizing--configuration-options)
  - [EC2 User Data](#ec2-user-data)
  - [EC2 Instance Types](#ec2-instance-types)
    - [Naming Convention](#naming-convention)


# Create AWS Account

Create a new AWS Account done


# How to Choose an AWS Region

When launching a new application, consider the following factors:

1. Compliance & Data Governance
- Some countries require data to remain within specific geographic boundaries
- AWS ensures data does not leave a Region without explicit permission
- Important for regulated industries (finance, healthcare, government)

2. Proximity to Customers (Latency)
- Choose a Region closest to your users
- Reduced physical distance results in lower latency
- Improves performance for user-facing and real-time applications

3. Service Availability
- Not all AWS services or features are available in every Region
- New services and features are released in select Regions first
- Always verify service availability before choosing a Region

4. Pricing
- AWS pricing varies by Region
- Pricing is transparent on the AWS service pricing pages
- Can impact overall cost, especially at scale

Select the Region that satisfies
- compliance requirements
- minimizes latency
- supports required services
- offers optimal pricing.


# AWS Availability Zones (AZs)

1. Core Concepts
- Each AWS Region has multiple Availability Zones
  - Usually 3 AZs
  - Minimum: 3, Maximum: 6
- AZ naming format: Region + letter (e.g., ap-southeast-2a)

2. Infrastructure
- Each AZ is made of one or more discrete data centers
- Built with redundant:
  - Power
  - Networking
  - Connectivity

3. Fault Tolerance
- AZs are physically isolated from each other
- Designed to prevent a single disaster from impacting multiple AZs
- Each AZ acts as a separate failure domain

4. Connectivity
- AZs are connected by:
  - High-bandwidth
  - Ultra-low latency
  - Private AWS networks

Deploy resources across multiple AZs to achieve high availability and fault tolerance.

# AWS Points of Presence (Edge Locations)

AWS Edge Locations are part of AWS’s global edge network used to deliver content with low latency.

1. Core Concept
- Edge Locations are AWS data centers located close to end users
- Also called Points of Presence (PoPs)
- They are not Regions or Availability Zones

2. Purpose
- Reduce latency by serving requests from locations closest to users
- Improve performance for static and dynamic content
- Reduce load on origin servers

3. Services Using Edge Locations
- Amazon CloudFront (CDN)
- AWS Global Accelerator
- Amazon Route 53 (DNS)

4. Global Distribution
- AWS has many more Edge Locations than Regions
- Distributed across major cities worldwide
- Users are automatically routed to the nearest Edge Location

5. How Content Delivery Works
- Requests go to the nearest Edge Location
- Cached content is served immediately
- Uncached content is fetched from the origin and then cached

6. Fault Tolerance
- Edge Locations are highly available
- Traffic is automatically routed to another Edge Location if one fails

Edge Locations are used for low-latency content delivery and routing, while compute and databases run in Regions and Availability Zones.


# AWS Identity and Access Management (IAM)

1. Users & Groups
- IAM is a global service for managing access
- Root account: full access, should not be used daily
- Users represent individuals or entities
- Groups contain users (not other groups)
- Users can belong to multiple groups or none

2. Permissions
- Permissions are assigned via policies (JSON documents)
- Policies define what users or groups can do
- Follow the least privilege principle: grant only necessary permissions

# IAM Policies Structure

IAM policies are **JSON documents** that define what actions are allowed or denied on AWS resources.

1. Policy Document Elements
An IAM policy consists of the following main elements:

- Version
  - Defines the policy language version
  - Always use `"2012-10-17"` (current and recommended)

- Id (optional)
  - Identifier for the policy
  - Useful for management and auditing

- Statement (required)
  - One or more individual permission statements
  - Can be a single statement or an array of statements

2. Statement Elements
Each Statement contains the actual permission rules:

- Sid (optional)
  - Statement identifier
  - Helps reference or describe the statement

- Effect
  - Defines whether access is granted or denied
  - Possible values:
    - Allow
    - Deny

- Principal
  - Specifies who the policy applies to
  - Can be an AWS account, user, role, or service
  - Commonly used in resource-based policies

- Action
  - List of AWS API actions that are allowed or denied
  - Example: s3:GetObject, ec2:StartInstances

- Resource
  - List of AWS resources the actions apply to
  - Defined using ARNs
  - Can be specific resources or `*` (all)

- Condition (optional)
  - Specifies conditions under which the policy is in effect
  - Examples:
    - IP address
    - Time of day
    - MFA requirement

Summary:
- IAM policies are JSON documents defining permissions
- Always use `"2012-10-17"` as the policy version
- Policies contain one or more Statements
- Statements define:
  - Who (Principal)
  - What (Action)
  - On which resources (Resource)
  - When (Condition)
- Use `Allow` or `Deny` and follow the least privilege principle

# AWS CLI

## Installation

Instructions:

https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html

```shell
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```

Update

```shell
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install --bin-dir /usr/local/bin --install-dir /usr/local/aws-cli --update
```

## CLI Usage

```shell
aws configure
aws iam list-users
```

## Cloud Shell

CLI in the web interface

# Amazon EC2 (Elastic Compute Cloud)

Amazon EC2 is a core AWS service that provides Infrastructure as a Service (IaaS).

It allows you to rent and manage virtual servers in the cloud instead of owning physical hardware.

## Core Concept
- EC2 = Elastic Compute Cloud
- Provides on-demand virtual machines in AWS
- You control:
  - Operating system
  - Instance type (CPU, RAM)
  - Storage
  - Networking
- You are responsible for managing the instance (patching, security, etc.)

## Main Components Around EC2

1. EC2 Instances
  - Virtual machines running in AWS
  - Used to host applications, APIs, websites, databases, etc.

2. EBS Elastic Block Store
  - Virtual hard drives attached to EC2 instances
  - Persistent storage (data survives instance stop/start)

3. ELB Elastic Load Balancer
  - Distributes incoming traffic across multiple EC2 instances
  - Improves availability and fault tolerance

4. ASG Auto Scaling Group
  - Automatically adds or removes EC2 instances
  - Maintains desired capacity
  - Handles scaling based on traffic or metrics

## EC2 Sizing & Configuration Options

When launching an EC2 instance, you must choose how it is sized and configured based on your workload needs.

1. Operating System (OS)
- Choose the OS for the instance:
  - Linux
  - Windows
  - macOS
- Determines software compatibility and licensing cost

2. Compute Power (CPU)
- Number of vCPUs (virtual CPUs)
- Defines processing capability
- Choose based on workload type (general, compute-intensive, etc.)

3. Memory (RAM)
- Amount of Random-Access Memory
- Important for memory-heavy applications (databases, caching, analytics)

4. Storage Options

- Network-attached storage
  - EBS (Elastic Block Store) → persistent block storage
  - EFS (Elastic File System) → shared file system

- Instance Store
  - Physically attached hardware disk
  - Very fast
  - Data is lost if the instance stops or terminates

5. Networking
- Network card performance (varies by instance type)
- Can assign a Public IP address
- Impacts bandwidth and throughput

6. Security
- Security Groups
  - Act as a virtual firewall
  - Control inbound and outbound traffic

7. EC2 User Data (Bootstrap Script)
- Script executed at first launch
- Used to:
  - Install software
  - Configure services
  - Automate setup
- Helps automate instance provisioning

## EC2 User Data

EC2 User Data allows you to automatically run a script when an EC2 instance launches.

1. What is EC2 User Data
- Used to bootstrap an EC2 instance
- Bootstrapping means running commands automatically when the machine starts
- The script runs only once at the first launch of the instance

2. Purpose
- Automates initial configuration
- Reduces manual setup
- Ensures consistent environment setup across instances

3. Common Use Cases
- Installing system updates
- Installing required software (e.g., Nginx, Apache, Docker)
- Downloading files from the internet
- Configuring services
- Running custom setup commands

4. Execution Details
- Runs automatically during instance launch
- Executes with root user privileges
- Can be written as:
  - Bash script (Linux)
  - PowerShell script (Windows)

## EC2 Instance Types

EC2 provides different instance types optimized for different workloads and use cases.

1. General Purpose
- Balanced resources:
  - Compute
  - Memory
  - Networking
- Suitable for:
  - Web servers
  - Code repositories
  - Small to medium applications
- Example: `t2.micro`
- Good default choice when workload is not specialized

2. Compute Optimized
- High-performance processors
- Designed for compute-intensive workloads
- Use cases:
  - Batch processing
  - Media transcoding
  - High-performance web servers
  - High Performance Computing (HPC)
  - Scientific modeling & machine learning
  - Dedicated gaming servers

3. Memory Optimized
- Large amounts of RAM
- Designed for workloads processing large datasets in memory
- Use cases:
  - High-performance databases (Relational & NoSQL)
  - In-memory databases (e.g., BI systems)
  - Distributed cache stores
  - Real-time big data processing

4. Storage Optimized
- High, sequential read/write performance
- Uses fast local storage (e.g., NVMe SSD)
- Designed for storage-intensive workloads
- Use cases:
  - High-frequency OLTP systems
  - Relational & NoSQL databases
  - Data warehousing
  - Distributed file systems
  - Cache layers (e.g., Redis)

### Naming Convention

Example: `m5.2xlarge`

- m → Instance class (family)
  - Defines the workload category
  - Example:
    - m = General purpose
    - c = Compute optimized
    - r = Memory optimized

- 5 → Generation
  - Newer generations offer:
    - Better performance
    - Improved networking
    - Better price/performance ratio

- 2xlarge → Size within the instance family
  - Defines amount of:
    - vCPUs
    - RAM
    - Network bandwidth
  - Larger size = more resources

Key Idea
- Same family → similar performance characteristics
- Different sizes → scale up/down resources
- Newer generation → typically better efficiency

| Instance        | vCPU | Memory (GiB) | Storage              | Network Performance | EBS Bandwidth (Mbps) |
|-----------------|------|--------------|----------------------|---------------------|----------------------|
| t2.micro        | 1    | 1            | EBS Only             | Low to Moderate     | -                    |
| t2.xlarge       | 4    | 16           | EBS Only             | Moderate            | -                    |
| c5d.4xlarge     | 16   | 32           | 1 × 400 GB NVMe SSD  | Up to 10 Gbps       | 4,750                |
| r5.16xlarge     | 64   | 512          | EBS Only             | 20 Gbps             | 13,600               |
| m5.8xlarge      | 32   | 128          | EBS Only             | 10 Gbps             | 6,800                |
