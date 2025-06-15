# Introduction to Cloud Computing

## What is Cloud?

- Informally: a way of renting/sharing IT resources
  - Through internet/web
  - Has an innovative way to specify, measure, and charge the rented resources
  - Many other features
- Not every kind of IT resource renting is called cloud
  - Lease from Dell to equip labs
  - Rent space from ISP to set up a website

## What are IT resources?

- Infrastructure (physical facilities)
- Hardware (servers, desktops, VMs)
- Networks (internet, routers)
- Software (Office 365, operating systems, Hadoop, MongoDB)

## Running a renting business

- To run a renting business, you need:
  - A way to package/measure/quantify your rental product/service
  - A way to charge the customer:
    - Hourly/daily/monthly/yearly rate
    - subscription
  - A way to deliver the product/service
    - Truck, courier, pickup, or Internet
  - A way to guarantee your product/service meet the client's requirements
- "Cloud" as we know it wasn't possible until supporting technologies were mature
  - Fast, reliable internet
  - Cheap storage and virtualisation
  - Automation tools
  - Secure online payments
- There had to be strong incentives for both providers and customers:
  - For providers: new revenue streams and scalable infrastructure
  - For customers: lower upfront cost, pay-as-you-go, flexibility

## Broad Definition of Cloud Computing

- According to the US Government:
  - "A model for enabling ubiquitous, convenient, on-demand network access to a shared pool of configurable computing resources (e.g. networks, servers, storage, applications, and services)"

## Three Service Models of Cloud Computing

1. Infrastructure as a Service (IaaS)
2. Platform as a Service (PaaS)
3. Software as a Service (SaaS)

## Typical Architecture in a Cloud Environment

3 main service layers:

1. Software as a Service (SaaS)
  - Resources managed:
    - Business applications, web services, multimedia
  - e.g. Google Apps, Facebook, YouTube, Salesforce.com
2. Platform as a Service (PaaS)
  - Resources managed:
    - Software frameworks (Java/Python/.net), storage (DB/file)
  - e.g. Azure, Google AppEngine, Amazon SimpleDB/S3
3. Infrastructure as a Service (IaaS)
  - Resources managed:
    - Infrastructure: computation (VM), storage (block)
    - Hardware: CPU, memory, disk, bandwidth
  - Infrastructure examples: EC2, GoGrid, Flexiscale
  - Hardware examples: Data centres

## What is SaaS?

- The consumer uses an application, but does not control the operating system, hardware, or network infrastructure on which it's running
- Examples:
  - Business applications: CRM solutions from salesforce.com
  - Business/personal applications: Gmail, Google Docs, etc
- Quite different compared to the other models

## What is PaaS?

- The consumer uses a **hosting environment** for their applications
- The consumer controls the applications that run in the environment (and possibly have some control over the hosting environment), but does not control the operating system, hardware, or network infrastructure on which they are running
- The platform is typically an application framework
- Examples:
  - Google App Engine
  - AWS Elastic MapReduce

## What is IaaS?

- The consumer uses "fundamental computing resources" such as processing power, storage, networknig components, or middleware
- The consumer can control the operating system, storage, deployed applications, and possibly networking components such as firewalls and load balancers, but not the cloud infrastructure beneath them

## SaaS Service Specification and Pricing

- The service specification depends on the actual application
  - Could be the number of user accounts supported, the size of storage, etc
- The pricing is usually subscription based
  - e.g. monthly or yearly price

## IaaS Service Specification and Pricing

- Specification is similar to the specifications when you purchase a computer
  - e.g. CPU speed, number of cores, memory, etc
  - pay-as-you-go hourly rate or even "per second billing"

## PaaS Service Specifications and Pricing

- In between SaaS and IaaS, could be fine-grained hourly rate or subscription based
  - e.g. If you start a MapReduce cluster in Azure/AWS, you can specify how many nodes you want to have an the node type, and you're charged hourly/secondly based on those instance prices

## Service Delivery

- All XaaS models are delivered through the internet, with a web interface

# Introduction to Amazon Web Services

## What are web services?

- A web service is any piece of software that makes itself available over the internet and uses a standardised format (e.g. XML/JSON) for the request and response of an API interaction
- Typically accessible via the internet using typical web protocols (e.g. HTTP) and are used by machines/humans through a UI
## What is AWS?

- A platform of web services that offers solutions for computing, storing, and networking, at different layers of abstraction

## AWS Global Infrastructure

- The AWS Global Infrastructure is designed and built to deliver a flexible, reliable, scalable, and secure cloud computing environment with high-quality global network performance
- Has regions and availability zones globally

## What are AWS Regions?

- A geographical area
  - Data replication across regions is controlled by you
  - Communication between regions uses the AWS Backbone network infrastructure
- Each region providse full redundancy and connectivity to the network
- A region typically consists of two or more Availability Zones

## What are Availability Zones?

- Each region has multiple Availability Zones
- Each Availability Zone is a fully isolated partition of the AWS infrastructure
  - AZs consists of discrete data centres
  - Designed for fault isolation
  - Interconnected with other AZs using high-speed private networking
  - You can choose your AZs
  - AWS recommends replicating data and resources across AZs for resiliency

## E-Commerce Site - AWS Sample Application

Bare minimum setup
- Requirements:
  - A web server handles requests from customers
  - A database stores product information and orders
- "On-premise" setup:
  - Rent servers in a data centre
- Cloud/AWS setup
  - Using a virtual machine (e.g. AWS EC2) as a web server
  - Cloud hosted database (e.g. AWS RDS) as a database

Enhanced setup
- Split web app into dynamic/static content to reduce load on web servers
  - Deliver static content over a content delivery network (CDN) to improve performance
- Run the web server on multiple VMs to achieve high availability and to reduce response time

Even better setup
- Replicate DB across data centres
- Multiple VMs as web servers across data centres
- Load balancer to distribute customer requests

## Cost of AWS

Free tier
- For new accounts within the first 12 months of signing up
  - Limited services types
- Education credit/access

## AWS Billing 

- 3 major billing categories:
  - Based on time of use
  - Based on traffic
  - Based on storage usage
- There are other quality based charging categories

# Exploring AWS Services

## Overall Picture of AWS Services

- Services are created and managed by sending requests to the corresponding API
  - Through a web-based GUI like the management console
  - CLI interfaces like AWS CLI
  - Programmatically via SDK
- Virtual machines can be accessed through SSH and can be managed in the same way as a physical server
- Majority of the services are behind the APIs

## Managing a Simple Web Application

- Administrators use AWS APIs to create/configure necessary services
- The VM can be setup further through SSH
  - Uploading web server code
  - Configuring parameters

## Front End User Perspective

- The VM is the frontend
- They send HTTP requests to the VM, which runs a web server along with a custom PHP web application
- The web application talks to AWS services to answer HTTP requests from users
  - query data from a NoSQL database
  - store static files
  - send emails
- Communication between the web application and AWS services is handled by the API

# Interacting with AWS Services

## Four Ways to Interact with AWS

- Represents different end user interfaces of the same API
  - Management console
  - Command line
  - SDK
  - Blueprints

## AWS Management Console

- Starting point for nearly all users
- Easy to use
- Best for setting up simple infrastructure for development and testing

## AWS CLI

- Allows users to manage and access AWS services through their terminal
- Best for automating/semi-automating recurring tasks
- Typical use cases:
  - Create new infrastructure based on blueprint
  - Upload files
  - Inspect services

## AWS SDK

- Language specific SDKs wrap up the AWS APIs so that AWS services can be integrated into applications
- Supported languages:
  - JavaScript, Python, PHP, .NET, Ruby, Java, Go, Node.js, C++

## AWS Blueprints

- A description of a system containing all resources and their dependencies
- An Infrastructure As Code tool compares your blueprint with the current system and calculates the steps to create, update, or delete your cloud infrastructure
- e.g. Amazon CloudFormation, Terraform

# Accessing AWS Services

## AWS Accounts and IAM Services

- An AWS account is needed to start any AWS service
- There are other ways to gain access to AWS resources:
  - e.g. all employees of a company could use the same account
    - they don't use the actual account name and password to login
    - most cloud platforms offer an Identity and Access Management (IAM) service to handle such access scenarios

## Essential Components of IAM

- IAM user: a person or application that can authenticate with an AWS account
- IAM group: a collection of IAM users that are granted identical authorisation
- IAM policy: the document that defines which resources can be accessed and the level of access to each resource
- IAM role: useful mechanism to grant a set of permissions for making AWS service requests

## Authenticating as an IAM user

- When you define an IAM user, you select what types of access they are permitted to use
- Programmatic access:
  - Authenticate using:
    - Access key ID
    - Secret access key
  - Provides AWS CLI and AWS SDK access
- AWS Management Console Access
  - Authenticate using:
    - 12-digit Account ID or alias
    - IAM username
    - IAM password
  - If enabled, MFA will prompt for an authentication code

## Authentication vs Authorisation

- Authentication: verifies the identify of a user
- Authorisation: what actions an authenticated user is allowed to do

## IAM: Authorisation

- Assign permissions by creating an IAM policy
- Permissions determine which resources and operations are allowed:
  - All permissions are implicitly denied by default
  - If something is explicitly denied, it is never allowed
- Best practise: principle of least privilege
- Note: the scope of IAM service configurations is global. Applies across all AWS regions

## What are IAM policies?

- A document that defines permissions
  - Enables fine-grained access control
- Two types of policies:
  - Identity-based:
    - Attach a policy to any IAM entity (user, group, role)
    - Policies specify:
      - Actions that may be performed by the entity
      - Actions that may not be performed by the entiries
    - A single policy can be attached to multiple entities
    - A single entity can have multiple policies attached to it
  - Resource-based:
    - Attached to a resource (such as an S3 bucket)

## What are IAM groups?

- A collection of IAM users
- Used to grant the same permissions to multiple users
  - Permissions are granted by attaching IAM policies to the group
- A user can belong to multiple groups
- There is no default group
- Groups cannot be nested

















