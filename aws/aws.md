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



