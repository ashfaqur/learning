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
  - [Security Groups](#security-groups)
  - [SSH to EC2 Instance](#ssh-to-ec2-instance)
  - [IAM Role in EC2 Instance](#iam-role-in-ec2-instance)
  - [EC2 Instance Purchasing Options](#ec2-instance-purchasing-options)
  - [EC2 Spot Instances](#ec2-spot-instances)
    - [Spot Fleets](#spot-fleets)
    - [Spot Allocation Strategies](#spot-allocation-strategies)
  - [IPv4 and IPv6](#ipv4-and-ipv6)
  - [EC2 Default IP Behavior](#ec2-default-ip-behavior)
- [Elastic IP (EIP)](#elastic-ip-eip)


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


## Security Groups

Security Groups are the foundation of network security in AWS.
They act as virtual firewalls controlling traffic to and from EC2 instances.

1. Core Concept
- Security Groups control:
  - Inbound traffic (into the instance)
  - Outbound traffic (out of the instance)
- They only contain **allow rules** (no explicit deny rules)
- Rules can reference:
  - IP addresses (IPv4 / IPv6)
  - Other Security Groups

2. What They Regulate
- Access to specific ports
- Authorized IP ranges
- Inbound network traffic
- Outbound network traffic

They operate at the instance level.

3. Important Characteristics
- Can be attached to multiple EC2 instances
- Scoped to a specific Region and VPC
- Operate outside the EC2 instance
  - If traffic is blocked, the EC2 never sees it
- Stateful:
  - If inbound traffic is allowed, response traffic is automatically allowed

4. Default Behavior
- All inbound traffic is blocked by default
- All outbound traffic is allowed by default

5. Troubleshooting Tips
- Timeout → Likely a Security Group issue
- "Connection refused" → Application not running or misconfigured

6. Referencing Other Security Groups
- You can allow traffic from another Security Group
- Useful for:
  - App server ↔ Database communication
  - Layered architecture security
- Improves security by avoiding public IP exposure

7. Common Ports to Know
- 22 → SSH (Linux login)
- 21 → FTP
- 22 → SFTP (over SSH)
- 80 → HTTP
- 443 → HTTPS
- 3389 → RDP (Windows login)

## SSH to EC2 Instance

SSH (Secure Shell) is used to securely connect to a Linux EC2 instance.

1. Requirements
- EC2 instance must be running
- Instance must have:
  - Public IP (or accessible via VPN/Bastion Host)
  - Port 22 open in Security Group
- You must have the private key (.pem file)

2. Basic SSH Command (Linux / macOS)
- Set correct permissions:
  - chmod 400 my-key.pem
- Connect:
  - ssh -i my-key.pem ec2-user@<Public-IP>

3. Default Usernames
- Amazon Linux → ec2-user
- Ubuntu → ubuntu
- CentOS → centos
- Debian → admin

4. Security Considerations
- Never share your private key
- Restrict SSH access to your IP only
- Do not allow 0.0.0.0/0 for port 22 in production


## IAM Role in EC2 Instance

An IAM Role allows an EC2 instance to securely access other AWS services without using access keys.

1. Purpose
- Grant permissions to EC2 instances
- Avoid storing AWS access keys inside the instance
- Improve security

2. How It Works
- Create an IAM Role with required permissions (policies)
- Attach the role to the EC2 instance
- EC2 automatically receives temporary credentials
- AWS manages credential rotation

3. Common Use Cases
- EC2 accessing S3 buckets
- Writing logs to CloudWatch
- Accessing DynamoDB
- Calling other AWS APIs

## EC2 Instance Purchasing Options

EC2 offers multiple purchasing models depending on workload duration, flexibility, and cost sensitivity.

1. On-Demand Instances
- Pay for what you use
- Linux/Windows billed per second (after first minute)
- Other OS billed per hour
- No upfront cost
- No long-term commitment
- Highest cost compared to other options
- Best for short-term, unpredictable workloads

2. Reserved Instances (RI)
- Up to 72% discount compared to On-Demand
- Commit for 1 year or 3 years
- Reserve specific attributes:
  - Instance type
  - Region
  - Tenancy
  - OS
- Payment options:
  - No Upfront
  - Partial Upfront
  - All Upfront
- Scope:
  - Regional
  - Zonal (reserves capacity in specific AZ)
- Best for steady-state workloads (e.g., databases)
- Can buy/sell in Reserved Instance Marketplace

Convertible Reserved Instances
- Can change:
  - Instance family
  - Instance type
  - OS
  - Scope
  - Tenancy
- Up to 66% discount
- More flexibility than standard RI

3. Savings Plans
- Up to 72% discount (similar to RIs)
- Commit to spend a fixed amount per hour for 1 or 3 years
- Locked to:
  - Instance family
  - Region
- Flexible across:
  - Instance size
  - OS
  - Tenancy
- Usage beyond commitment billed at On-Demand rate

4. Spot Instances
- Up to 90% discount
- Can be terminated anytime if spot price exceeds your bid
- Most cost-efficient option
- Suitable for:
  - Batch jobs
  - Data analysis
  - Image processing
  - Distributed workloads
  - Flexible start/end time workloads
- Not suitable for critical systems or databases

5. Dedicated Hosts
- Entire physical server dedicated to you
- Full control over instance placement
- Supports bring-your-own-license (BYOL)
- Purchasing options:
  - On-Demand
  - Reserved (1 or 3 years)
- Most expensive option
- Used for compliance or licensing requirements

6. Dedicated Instances
- Hardware dedicated to you
- May share hardware with other instances in same account
- No control over placement
- Hardware may change after stop/start

7. Capacity Reservations
- Reserve On-Demand capacity in a specific AZ
- No long-term commitment
- No discount (billed at On-Demand rate)
- Pay whether instance is running or not
- Can combine with RIs or Savings Plans for billing discounts
- Suitable when you must guarantee capacity in a specific AZ

TL;DR
- On-Demand → flexible, no commitment, highest cost
- Reserved → long-term steady workloads, big discount
- Savings Plans → spend-based commitment, flexible within family
- Spot → cheapest, can be interrupted
- Dedicated Host → entire physical server, compliance/licensing
- Dedicated Instance → hardware isolated but less control
- Capacity Reservation → guarantee capacity in specific AZ, no discount

EC2 Purchasing Options

| Requirement / Scenario                         | Best Option                    | Why                                  |
|------------------------------------------------|--------------------------------|--------------------------------------|
| Short-term, unpredictable workload             | On-Demand                      | No commitment, flexible              |
| Steady-state, long-term workload               | Reserved Instance (1 or 3 yr)  | Large discount                       |
| Long-term but want flexibility                 | Savings Plan                   | Flexible across size/OS within family|
| Maximum discount, interruption acceptable      | Spot Instance                  | Cheapest option                      |
| Guaranteed capacity in specific AZ             | Capacity Reservation           | Reserves capacity, no discount       |
| Compliance / Bring Your Own License (BYOL)     | Dedicated Host                 | Full physical server control         |
| Hardware isolated but no placement control     | Dedicated Instance             | No shared hardware with other accounts |

Quick decision rule:
- Need flexibility → On-Demand
- Need discount → Reserved or Savings Plan
- Need cheapest → Spot
- Need guaranteed capacity → Capacity Reservation
- Need compliance/licensing control → Dedicated Host


## EC2 Spot Instances

Spot Instances allow you to use unused EC2 capacity at a very large discount.

1. Core Concept
- Up to 90% cheaper than On-Demand
- You define a maximum price
- Instance runs while current Spot price < your max price
- Spot price changes based on supply and demand

2. Interruption Behavior
- If Spot price exceeds your max price:
  - Instance can be stopped or terminated
- You receive a 2-minute warning before interruption
- Not suitable for critical systems or databases

3. Spot Block (Fixed Duration)
- Request Spot Instances for 1 to 6 hours
- No interruptions during the block period
- Rarely, AWS may still reclaim capacity

Use Cases
- Batch processing
- Data analysis
- Image/video processing
- Distributed workloads
- Flexible start and end times

When to Avoid
- Databases
- Critical production systems
- Stateful workloads without fault tolerance

### Spot Fleets

Spot Fleet = collection of Spot Instances (optionally mixed with On-Demand)

Purpose:
- Automatically maintain target capacity
- Select cheapest or most available instances

You define:
- Target capacity
- Max price
- Launch pools:
  - Instance type
  - OS
  - Availability Zone

Fleet stops launching when:
- Target capacity is reached
- Max cost is reached

### Spot Allocation Strategies

1. lowestPrice
- Chooses cheapest pool
- Best for cost optimization
- Good for short workloads

2. diversified
- Distributes instances across multiple pools
- Improves availability
- Good for long-running workloads

3. capacityOptimized
- Chooses pool with best capacity availability
- Reduces interruptions

4. priceCapacityOptimized (recommended)
- Prioritizes pools with highest capacity
- Then selects lowest price among them
- Best general-purpose strategy

## IPv4 and IPv6

Networking primarily uses two IP formats:
- IPv4 → example: 1.160.10.240
- IPv6 → example: 3ffe:1900:4545:3:200:f8ff:fe21:67cf

In most AWS scenarios IPv4 is used.

IPv4 format:
- [0-255].[0-255].[0-255].[0-255]
- Supports about 3.7 billion public addresses

IPv6:
- Newer version
- Designed to solve IP address exhaustion
- Common in IoT and modern networking

1. Public IP

- Identifies a machine on the public internet (WWW)
- Must be globally unique
- Can be geo-located
- Required for direct internet access
- Assigned to EC2 instances if public access is needed

2. Private IP

- Used inside a private network (VPC)
- Must be unique within the private network
- Can be reused across different private networks
- Not directly reachable from the internet
- Internet access requires:
  - NAT
  - Internet Gateway

Key Differences

1. Public IP:
- Internet reachable
- Globally unique
- Exposed to the public network

2. Private IP:
- Internal communication only
- Not internet reachable directly
- Used for secure internal architecture

## EC2 Default IP Behavior

By default, when you launch an EC2 instance in a public subnet:

- It receives a private IP address for internal AWS communication
- It receives a public IP address for internet access (if enabled)

1. Private IP
- Used for communication inside the VPC
- Always assigned to the instance
- Does not change during stop/start

2. Public IP
- Used to access the instance from the internet
- Required for SSH from your local machine
- Can change if the instance is stopped and started

3. SSH Access
- You cannot SSH using the private IP from your home computer
- You must use the public IP
- Unless:
  - You are connected via VPN
  - You use a Bastion Host inside the same VPC

4. Important Behavior
- Stop → Start:
  - Public IP may change
  - Private IP remains the same
- Terminate:
  - Both IPs are released


# Elastic IP (EIP)

An Elastic IP is a fixed public IPv4 address that you can allocate and attach to an EC2 instance.

1. Why Elastic IP Exists
- When you stop and start an EC2 instance:
  - Public IP may change
- If you need a fixed public IP:
  - Use an Elastic IP

2. Key Characteristics
- Static public IPv4 address
- Owned by your AWS account until released
- Can be attached to only one instance at a time
- Can be reassigned quickly to another instance

3. Use Case
- Mask instance failure by remapping EIP to another EC2 instance
- Useful for simple failover scenarios

4. Limits
- Default limit: 5 Elastic IPs per region
- Can request increase from AWS

5. Best Practice
- Avoid overusing Elastic IPs
- Often indicates poor architecture
- Prefer:
  - DNS (Route 53) pointing to instance
  - Load Balancer instead of public IP
  - High availability design
