# Amazon VPC Basic Concepts

## AWS Physical Infrastructure

- AWS cloud infrastructure resides in data centres that contain thousands of servers built into racks
  - Every rack has network routers and switches to route traffic
- Data centres are grouped together in Availability Zones
- AZs are connected with single digit millisecond latency networks
- AZs are grouped together in an AWS Region
  - Latency between AWS Regions is in the 10s of milliseconds

## VPCs

- Logically isolated from other VPCs
- Dedicated to your AWS account
- Belongs to a single Region and can span multiple AZs

## Subnets

- Range of IP addresses that divide a VPC
- Belong to a single AZ
- Classified as public or private

## IP Addressing

- When you create a VPC, you assign it to an IPv4 CIDR block (range of private IPv4 addresses)
- Cannot change the address range after you create the VPC
- Largest IPv4 CIDR block size is /16
- Smallest is /28
- IPv6 is also supported
- CIDR blocks of subnets cannot overlap

## Reserved IP Addresses

Example:

- A VPC with an IPv4 CIDR block of 10.0.0.0/16 has 65,536 total IP addresses
- The VPC has 4 equal-sized subnets (10.0.0.0/24, 10.0.1.0/24, 10.0.2.0/24, 10.0.3.0/24)
- Only 251 IP addresses are available for use by each subnet

- Reserved addresses for a CIDR block 10.0.0.0/24:
  - 10.0.0.0: network address
  - 10.0.0.1: internal communication
  - 10.0.0.2: domain name system resolutoin
  - 10.0.0.3: future use
  - 10.0.0.255: network broadcast address

## Public IP Address Types

- Public IPv4 address
  - Manually assigned through an Elastic IP address
  - Automatically assigned through the auto-assign public IP address settings at the subnet level
- Elastic IP address
  - Associated with an AWS account
  - Can be allocated and remapped anytime
  - Additional costs may apply

## Elastic Network Interface

- A virtual network interface that you can:
  - Attach to an instance
  - Detach from the instance and attach to another to redirect network traffic
- Its attributes follow when it is reattached to a new instance
- Each instance in your VPC has a defauly network interface that is assigned a private IPv4 address from the IPv4 address range of your VPC
- An instance can have multiple ENIs
- The ENI's network traffic is processed by the Nitro Card for VPC on the physical host

# VPC Routing

## Route Tables and Routes

- Route tables are essential for controlling network flow
  - Contain a set of rules/routes that determine where network traffic is directed
- Each route specifies a destination and a target
  - Destination: the CIDR block specifying where the traffic is going
  - Target: the next hop or device that the traffic should be sent to in order to reach the destination
- Example: Home Network
  - Scenario: accessing a website like google.com
  - Destination: the IP addresses associated with google.com
  - Target: your home router
  - Explanation: all traffic from devices within your home network destined for the internet is first routed through your router

## Main Route Table in an AWS VPC

- Default Traffic Control: every VPC automatically comes with a "main route table"
  - Acts as the default traffic director for all subnets within the VPC, unless a subnet is explicitly associated with a custom route table
- Initial configuration: by default, the main route table contais a local route, allowing communication within the VPC itself
  - The local route typically has a destination of the VPC's CIDR block and a target of "local"
    - The "local" target is used for routes that allow communication between subnets within the same VPC

## Subnet and Route Table

- Subnet Association: when you create a new subnet in your VPC, if you don't specify a route table it's automatically associated with the main route table
  - This is why all subnets within a VPC can communicate with each other by default
- Customisation: you can modify the main route table to add routes for internet access, or other destinations
  - Best practise is to create custom route tables, and leave the main route table as the defauly
- Each subnet within a VPC can only be associated with one route table at a time

## Internet Gateway

- A horizontally scaled, redundant, and highly available VPC component
  - Allows communication between instances in the VPC and the internet
- 2 key functions:
  - Network Address Translaton (NAT): translates private IP addresses in your VPC to public IP addresses, enabling instances to connect to the internet
  - Route Table Target: provides a target in the VPC route tables for internet-routable traffic
- One-to-One Relationship:
  - An IGW can only be attached to a single VPC at a time
  - A VPC can only have one IGW attached to it

## NAT Gateway Basics

- 2 types of NAT gateways
  - Public NAT Gateway: allows instances in the private subnet to connect to the internet (outgoing only)
  - Private NAT Gateway: allows instances in private subnets to connect to other VPCs or on premise network
- Public NAT Gateway:
  - Created within a public subnet
  - Assigned an elastic IP address
  - Has redundancy within the AZ
  - Private subnets in different AZs should have their own NAT gateway

## Internet Gateway and NAT Gateway

- A network gateway is a device or node that connects disparate networks by translating communications from one protocol to another
- Amazon Internet Gateway (IGW) is a virtual gateway that allows instances with public IPs to access the internet
  - Allows two-way traffic
  - One per VPC
- Amazon NAT Gateway (NGW) is a managed Network Address Translation (NAT) service that allows instances with no public IPs to access the internet
  - Allows one-way traffic (outgoing)
  - One per AZ (recommended)

## Recommended Subnet Selections for Each Use Case

- Database instances: private subnet
- Batch-processing instances: private subnet
- Web application instances: public or private subnet
- NAT gateway or instance: public subnet

# VPC Security

## Security Layers of Defence for VPC

1. Secure network protocol
  - Encryption and protection in transit
  - Ensures the traffic from the client to internet gateway is encrypted and secure
  - e.g. HTTPS, SSH, TLS
2. Internet Gateway
  - Entry point into VPC from the public internet
  - Connects the client request to the public subnet (via route table)
3. Route Table Layer
  - Controls which traffic is allowed to enter/exit subnets based on destination
4. Network ACL Layer
  - Stateless firewall applied at a sbunet level
  - Filters traffic before it reaches the EC2 instances based on:
    - IP addresses
    - Protocol (e.g. TCP, UDP)
    - Port number
  - Stateless: must allow both inbound and outbound rules explicitly
5. Subnet Layer
  - Where the EC2s live
  - ACLs apply at this level
6. Security Group Layer
  - A stateful firewall attached directly to EC2 instances
  - Filters at the instance level
  - Only checks inbound rules

## Security Groups and Network ACL Scope

- Security groups protect the instances: like an apartment door only letting in the owner who has a key
- Network ACLs protect the subnet: like an apartment doorman who only lets apartment owners and tenants in the building

## Security Group Basics

- Security groups have rules that control inbound and outbound instance traffic
  - Each rule specifies a source/destination, protocol and port range
- All rules are allow-rules
  - When a new SG is created, there is no inbound rule and there is an outbound rule to allow all outbound traffic
    - No allowed inbound traffic but all outbound traffic are allowed
- Security groups are stateful, return traffic is automatically allowed, regardless of any rule

## Network Access Control Lists

- A network ACL has separate inbound and outbound rules, and each rule can allow or deny traffic
- Default network ACLs allow all inbound and outbound IPv4 traffic
- Network ACLs are stateless

## Custom Network ACLs

- Custom network ACLs deny all inbound and outbound traffic until you add rules
- You can specify both allow and deny rules
- Rules are evaluated in number order, starting with the lowest number

## Security Group vs Network ACLs

| Attribute | Security Groups | Network ACLs |
|-----------|-----------------|--------------|
| Scope | Instance level | Subnet level |
| Supported rules | Allow rules only | Allow and deny rules |
| State | Stateful (return traffic is automatically allowed) | Stateless (return traffic must be explicitly allowed by rules) |
| Order of rules | All rules are evaluated before decision to allow traffic | Rules are evaluated in number order before decison to allow traffic |
| Default rules | No inbound traffic is allowed, all outbound traffic is allowed | All inbound and outbound traffic is allowed |

# Connecting to Managed Services

## VPC Endpoints

- Allow private connection between a VPC and AWS services without going through the internet
- It helps improve security and performance, as well as reducing potential networking cost
- 2 types:
  - Interface Endpoint:
    - Powered by PrivateLink
    - Uses Elastic Network Interfaces
  - Gateway Endpoint
    - Targeted at S3 and DynamoDB
    - Adds a route in your route table

## EC2 Attached ENI vs VPC Endpoint ENI

- Both are backed by Nitro hardware
- EC2-attached ENIs
  - Backed by Nitro hardware: Nitro card for VPC
  - Handles VPC networking, security groups, monitoring, and encryption offloaded from the host CPU
- ENIs for interface VPC endpoints
  - Also backed by Nitro hardware
  - Not attached to an EC2 instance, but AWS still provisions them on Nitro-backed hypervisors running in AWS-managed infrastructure
  - These are "invisible" service instances, purpose-built to act as entry points to AWS services via PrivateLink

## Gateways and VPC Endpoints

- A gateway connects your VPC to another network
  - e.g. use an internet gateway to connect your VPC to the internet
- Use a VPC endpoint to connect to AWS services privately, without the use of an internet gateway or NAT device

- Gateway:
  - Purpose: connect to external networks (internet, other VPCs)
  - Services: Internet Gateway (2-way) / NAT Gateway (outbound only)
  - Traffic Type: Leaves AWS network
  - Traffic Direction: inbound and outbound
  - Cost: NAT Gateway costs, Internet Gateway is free
- VPC Endpoint:
  - Purpose: connect privately to AWS services
  - Services: Interface/Gateway Endpoint
  - Traffic Type: stays in AWS network
  - Traffic direction: primarily outbound to AWS services
  - Cost: endpoints have low cost (data processing fee)

# AWS Internal Traffic (Routing for VPC Destination)

## Multi-tenant Physical Host

- Instances in different VPCs can have the same private IP address

# AWS External Traffic


