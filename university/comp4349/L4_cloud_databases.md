# Amazon EBS

## AWS Data Storage Services

|   | Service | Access | Maximum Storage Volume | Latency | Storage Cost |
|---|---------|--------|------------------------|---------|--------------|
| Object level | S3 | AWS API (SDKs, CLI), third-party tools | unlimited | high | very low |
| Block level | EBS (SSD) | Attached to an EC2 instance via network | 16 TiB | low | low |
| Block level | EC2 Instance Store (SSD) | Attached to an EC2 instance directly | 305 TB | very low | very low |
| File level | EFS | NFSv4.1 | unlimited | medium | medium|

## Block Storage vs Object Storage

- If you want to change one character in a 1 GB file:
  - Block storage: change one block (piece of the file) that contains the character
  - Object storage: entire file must be updated

## EBS: Persistent Storage Attached Over the Network

- Typical use is through creating individual storage volumes and attaching them to an EC2 instance
- They aren't part of the EC2 instances, the EBS volumes remain after the EC2 instance is terminated
  - Default value of DeleteOnTermination is true for the root volume of each EC2, but false for other volumes attached to the EC2
- Can only be attached to one instance at a time
- Volumes are automatically replicated within its AZ
- Can be backed up automatically to S3 through snapshots

## EBS Volume Types

|   | SSD |   | HDD |   |
|---|-----|---|-----|---|
|   | General Purpose | Provisioned IOPs | Throughput-Optimised | Cold |
| Max Volume Size | 16 TiB | 16 TiB | 16 TiB | 16 TiB |
| Max IOPS/Volume | 16,000 | 64,000 | 500 | 250 |
| Max Throughput/Volume | 250 MiB/s | 1,000 MiB/s | 500 MiB/s | 250 MiB/s |

## EBS Volume Type Use Cases

- SSD
  - General Purpose
    - Recommended for most workloads
    - System boot volumes
    - Virtual desktops
    - Low-latency interactive applications
    - Development and test environments
  - Provisioned IOPS
    - Critical business applications that required sustained IOPS performance, or more than 16,000 IOPS or 250 MiB/s of throughput per volume
    - Large database workloads
- HDD
  - Throughput-Optimised
    - Streaming workloads that require constant, fast throughput at a low price
    - Big data
    - Data warehouse
    - Log processing
    - Can't be a boot volume
  - Cold
    - Throughput-oriented storage for large volumes of data that is infrequently accessed
    - When the lowest storage cost is important
    - Can't be a boot volume

## EBS Features

- Snapshots
  - Point-in-time snapshots
  - Recreate a new volume at any time
- Encryption
  - Encrypted EBS volumes
  - No additional cost
- Elasticity
  - Increase capacity
  - Change to different types

## EBS Volumes, IOPS, and Pricing

1. Volumes
  - Persist independently from the instance
  - Charged by the amount that is provisioned per month
2. IOPS
  - General Purpose SSD: charged by the amount that you provision in GB per month until storage is released
  - Magnetic: charged by the number of requests to the volume
  - Provisioned IOPS SSD: charged by the amount that you privision in IOPS (multiplied by the percentage of days that you provision for the month)
3. Snapshots
  - Added costs of EBS snapshots to S3 is per GB-month of data stored
4. Data transfer
  - Inbound data transfer is free
  - Outbound data transfer across Regions incurs charges

## Typical Charges in Web Application Scenario

- EC2 instances with EBS storage running a web application
- EC2 charge:
  - Instance hourly rate
  - Inbound traffic is free
  - Outbound traffic charge: sending responeses back to the user
- EBS charge
  - Provisioned GB monthly charge
  - Data transfer between EC2 instances and EBS are free in general
  - Snapshots would incur storage costs
  - Copying snapshots across regions would incur costs


# Amazon VPC

## IP Addresses

- A unique numerical label assigned to devices connected to a network
  - IPv4 addresses are 32 bit
    - Provides 2^32 unique addresses: 0.0.0.0 - 255.255.255.255
    - Some are reserved for special purposes, e.g. 127.0.0.0
  - IPv6 addresses use 128 bits
- IPv4 addresses reserved for private networking:
  - 10.0.0.0 - 10.255.255.255
  - 172.16.0.0 - 172.31.255.255
  - 192.168.0.0 - 192.168.255.255

## Classless Inter-Domain Routing (CIDR) Block

- A common way to specify a range of IP addresses
  - CIDR block of a VPC defines the range of IP addresses resources in that VPC can be assigned to
  - Format: <IP Address>/<number>
  - e.g. 192.0.2.0/24 (192.0.2 - networking identifier, 0 - host identifier)
    - 192: fixed
    - 0: fixed
    - 2: fixed
    - 0: flexible
    - 24: tells you how many bits are fixed

## Amazon VPC

- Allows you to provision a logically isolated section of the AWS Cloud where you can launch AWS resources in a virtual network that you define
- Gives you control over your virtual networking resources including:
  - Selection of IP address range
  - VPC address range is specified using the private networking address as a CIDR block
  - Creation of subnets
  - Configuration of route tables and network gateways
- Enables you to customise the network configuration for your VPC
- Enables you to use multiple layers of security

## AWS Physical Infrastructure

- AWS Cloud Infrastructure resides in data centres that contain thousands of servers built into racks. Each rack has network routers and switches to route traffic
- Data centres are grouped in Availability Zones
- AZs are connected with a single digit millesecond latency network
- AZs are groupted together in an AWS Region
- Latency between AWS Regions is 10s of milliseconds

## VPCs and Subnets

- VPCs:
  - Logically isolated from other VPCs
  - Dedicated to your AWS account
  - Belongs to a single AWS Region and can span multiple AZs
- Subnets:
  - Range of IP addresses that divide a VPC
  - Belongs to a single AZ
  - Classified as public or private

## VPC, Subnets, and CIDR Blocks

- A subnet is a segment or partition of a VPC's IP address range where you can allocate a group of resources
- Subnets are a subset of the VPC CIDR block
- Subnet CIDR blocks can't overlap
- Each subnet resides entirely within one AZ
- You can add one or more sbunets in each AZ or in a Local Zone
- AWS reserves 5 IP addresses in each subnet

## Private and Public IP Addresses

- Many AWS resources (e.g. EC2) are created within a subnet of a VPC
  - They get allocated a private IP address within the CIDR range of the associated subnet
- Public IP addresses can be assigned 
  - Automatically assigned through subnet's auto IP assign property
    - Instances placed in a public subnet get a public IP assigned automatically
    - The public IP changes when the instance restarts
  - Manually through an Elastic IP address
    - Elastic IP addresses can be re-assigned to different instances

## Security Groups

- Acts as a firewall for VMs and other services
  - We can associate a security group with AWS resources to control traffic
  - Common for EC2 instances to have more than one security group associated with them
  - Common for a security group to be associated with multiple EC2 instancse
- Have rules that control inbound and outbound instance traffic
- Default SGs deny all inbound traffic and allow all outbound traffic

# Running Databases in the Cloud

## Running Databases in the Cloud

- Cloud hosted databases:
  - Install and manage an existing database server on VMs
  - Fully managed database services based on an existing database engine
    - e.g. AWS RDS, MongoDB Atlas
- Cloud native databases
  - Azure Cosmos DB
  - Google Bigtable, Google Spanner
  - Amazon DynamoDB, Amazon Aurora

## Cloud Native Databases

- Designed and built to incorporate cloud features
  - Scalability
  - Elasticity
  - Highly available
- NoSQL and SQL
  - Early versions use NoSQL or simplified SQL data model
  - Now full cloud scale SQL databases are supported by large providers

## Database Considerations

- Scalability
  - How much throughput is needed?
  - Will it scale?
- Storage requirements
  - Will the database need to be sized in gigabytes, terabytes, or petabytes of data?
- Data characteristics
  - What is your data model?
  - What are your data access patterns?
  - Do you need low latency?
- Durability
  - What level of data durability, availability, and recoverability is required?
  - Do regulatory obligations apply?

## Relational and Non-Relational Databases

| Features | Relational Databases | Non-Relational Databases|
|----------|----------------------|-------------------------|
| Structure | Tabular forms of columns and rows | Variety of structure models (key-value pairs, document, or graph-based) |
| Schema | Strict schema rules | Flexible schemas |
| Benefits | Ease of use, data integrity, reduced data storage, and common language (SQL) | Flexibility, scalability, and high performance |
| Use Case | When migrating an on-premises relational workload or if your workload involves OLTP | When a caching layer is needed to improve read performance, when storing JSON documents, or when a single digit millisecond data retrieval is needed |
| Optimisation | Optimised for structured data stored in tables, supports complex one-time queries through joins | Optimised for fast access to structured, semi-structured, or unstructured data with high read and write throughput |

## Amazon Database Options

- Relational databases:
  - Amazon RDS
  - Managed database service that provides seven database engines to choose from, including Amazon Aurora
- Non-relational databases:
  - Amazon DynamoDB
  - Amazon Neptune
  - Amazon ElastiCache
  - Variety of services designed for databases such as key-value, graph, and in-memory

## Database Capacity Planning

- Designed with the end goal in mind

1. Analyse current storage capacity
2. Predict capacity requirements
3. Determine if horizontal scaling or vertical scaling, or a combo is needed

# AWS RDS

## Amazon Relational Database Service

- A managed relational database service to deploy and scale relational databases
- Supports multiple database engines
- Uses Amazon EBS volumes for database and log storage

## Benefits of Amazon RDS

- Lower administrative burden: don't need to provision your infrastructure or install and maintain database software
- Can scale up or down the compute and memory resources powering your deployment
- Configure automated backups, database snapshots, and automatic host replacement
- Can isolate your database in your own virtual network

## RDS Read Replicas

- Features
  - Offers asynchronous replication
  - Can be promoted to primary if needed
- Functionality
  - Use for read-heavy database workloads
  - Offload read queries

## Availability vs Scalability

- Multi AZ Deployment:
  - Primarily focused on high availability and durability
  - Protects against infrastructure failures within a single Region
  - Uses synchronous replication, ensuring data is written to both primary and standby instances simultaneously
  - Provides automatic failover to the standby instance in case of primary instance filure
- Read replica:
  - Designed for read scaling, improving performance for read-heavy workloads
  - Can also be used for disaster recovery
  - Read replica can be in the same or different AZ

## Aurora

- A RDBMS built for the cloud with full MySQL and PostgreSQL compatibility
- Managed by Amazon RDS
- Provides high performance and availability at one-tenth of the cost
- Delivers Multi-AZ deployments with Aurora Replicas

## RDS EC2 Instance Types and Sizing

- General Purpose (T4, T3, M6g, and M5)
- Memory-optimised (R6g, R5, X2g, and X1E)

## RDS Security Best Practises

- Run RB instance in a VPC for greatest network access control
- Use IAM policies to assign permissions for managing RDS resourecs
- Use security groups to control connecting IP addresses and resources
- Use SSL or TLS connections with database instances running certain database engines
- Encrypt database instances and snapshots at rest with a Key Management Service key
- Use the security features of the database engine to control database access

# Amazon Aurora

## Amazon Aurora

- A RDBMS service that attempts to build SQL in a distributed environment
  - Runs on modified MySQL/PostgreSQL
- Offers a service with full SQL support but more scalable
  - Also other features such as durability, fault tolerance, high availability
- Innovate way of disaggregation of compute and storage architecture

## Aurora Overview

- Key design principle: separate DB engine layer from storage layer
- DB layer:
  - From the client perspective, it is similar to a mirrored MySQL configuration
    - Primary (writer) and replicas (readers) of DB
      - Runs modified MySQL/PostgreSQL
    - Actual data is not stored on the instance that runs the DB engine
      - Not even on the EBS volumes attached to the VM
      - Supports 1 writer + 15 readers
    - Latest update will be reflected in the memory of all available DB instances
- Storage layer:
  - Storage nodes
    - Group of EC2 instances with local SSD (instance store)
    - Scattered in different AZs
    - Persist and replicate data for the database
    - Database volume is "divided into small fixed size segments (10 GB). These are each replicated 6 ways into Protection Groups so that each PG consists of 6 10GB segments, organised across 3 AZs, with 2 segments in each AZ"

## Durability at Scale

- Replication is a typical way to acheive durability, availability, and scalability
- Typical replication factor in most distributed storage systems is 3
  - A quorum mechanism is used to ensure data consistency
- In a large distributed system with multiple AZs, replicas are usually distributed in different AZs to minimise the change of concurrent failures
- Aurora doubles the conut of replicas in each AZ to tolerate:
  - Losing an entire AZ and one additional node without losing read availability
  - Losing an entire AZ without impacting the write ability
- Data has 6 copies in 3 AZs and uses a write quorum of 4 and read quorum of 3

# Amazon Aurora: Log as Database

## The Burden of Amplified Write

- To answer a write request, a database engine usually needs to:
  - Append the write-ahead log (WAL)
  - UPdate the actual data file
- A number of other data must also be written in practise:
  - Binary log
  - Double-write
  - Metadata file

## Aurora's Approach: The Log is the Database

- During the write operation: only redo logs are sent across the network by the Primary Instance
- Replicate Instances receive redo logs to update their memory
- Storage nodes receive redo logs
  - They materialise database pages from logs independently and asynchronously
- Primary Instance waits for 4 out of 6 acknowledgements to consider the write as successful

## IO Traffic in Aurora Storage Nodes

1. Receive log record and add to an in-memory queue
2. Persist record on disk and acknowledge
3. Organise records and identify gaps in the log since some batches may be lost
4. Gossip with peers to fill in gaps
5. Coalesce log records into new data pages
6. Periodically stage log and new pages to S3
7. Periodically garbage collect old versions
8. Periodically validate CRC codes on pages

- Design consideration: minimise the latency of the foreground write request
- Implementation: majority of the storage processing is moved to background

## The Log Marching Forward

- Logs advance as an ordered sequence of changes
- Each log record has an associated Log Sequence Number (LSN) that is a monotonically increasing value generated by the primary database instance
- The primary instance handles multiple transactions at any particular time point
  - Operations in the transaction are either all executed or 0 executed
  - Log record of the last operation in a transaction represents a possible consistency point
- Storage serviecs might have different storage status at any time
  - DB: Issued logs for transaction x, y, z
  - SS: Haven't received all logs of transaction x yet, but received all for transaction y
  - If the DB crashes at this point, when recovering it needs to know to abort transaction x and all persisted logs should be discarded

## Combining DB View and Storage View

- During crash recovery, the storage service determines the highest LSN for which it can guarantee availability of all prior logs (Volume Complete LSM (VCL))
  - Lower level view, reflecting storage service status
- The database layer can specify a few particular LSNs as Consistency Point LSN (CPL)
  - Higher level view, DB can establish it based on transaction log record
- VDL (Volume Durable LSN) is the highest CPL that is smaller than or equal to VCL
- Primary instance gossips with replica instances to establish a common view of VDL and other info

## VCL, CPS, and VDL

- Even if we have the complete data up to LSN 1007 (VCL), the database may have declared that only 900, 1000, and 1100 are CPLs, in which case we must truncate at 1000 (VDL)
  - LSN1007 could represent an operation in the middle of a transaction

# Amazon Aurora: Replication and LSN Example

## DB Settings

- An Aurora database with 15 GB data
  - 2 PG groups: PG1 and PG2
- One primary and 4 replicas in 3 AZs
  - Primary in AZ1
  - 2 replicas each in AZ2 and AZ3
- 10 storage nodes in 3 AZs
  - AZ1: 3, AZ2: 3, AZ3: 4
  - PG1 is replicated in SN11, SN12, SN21, SN22, SN31, SN32
  - PG2 is replicated in SN11, SN13, SN22, SN23, SN33, SN34

## Sample Transactions

- X targets PG1, has the following redo logs:
  - PG1: 1001, 1002, 1003
- Y targets both PG1 and PG2, has the following logs:
  - PG2: 1004, 1005
  - PG1: 1006, 1007

## Write Successful Indication

- Write request expressed in redo log 1001
  - The primary will send the redo log to all read replicas and SN11, SN12, SN21, SN22, SN31, SN32
  - If 4/6 respond, the write is considered as successful
- Read replicas only use the log to update the in-memory data

## Storage Node Processing

- Node of interest: SN11
- Logs received and the order: 1002, 1003, 1001, 1004, 1005, 1006
- Each storage node only processes a subset of the logs, so each has a backtrack link identifying the previous log of that PG
  - e.g. 1002 has a link saying 1001 is the previous log record of this PG, 1006 has a link indicating 1003 is the previous log of this PG
  - Logs received will be inserted in memory queue in order and persist on disk, then acknowledge back to the primary
  - It gossips with peers to get the missing logs
  - New data pages will be written for a series of logs with no gaps

## Database View and Storage Service View

- DB view
  - Consistence Point LSN:
    - 1003 represents the last log of transaction X
    - 1007 represents the last log of transaction Y
- Storage view
  - Master received 4 or more asks for log 1001 to 1006, it only receives one for log 1007
    - VCL is 1006
    - But it's in the middle of transaction Y
    - VDL is 1003
