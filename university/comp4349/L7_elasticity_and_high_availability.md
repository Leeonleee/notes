# Federated User in AWS Academy Lab

## Session Policy in AWS Academy Lab

- All federated users in AWS academy assume the role "voclabs"
- The role is likely defined under a single or a few AWS accounts
- Each lab has different sets of permissions (policies) associated with this role
- At any time, different users can attempt different labs
- Policies are dynamically associated with this role, and multiple policies can be associated with it at the same time

## Session Policy

- Advanced policies that you pass as a parameter when you programmatically create a temporary session for a role or federated user
- When AWS academy backend calls AssumeRole for a federated user, it includes a Policy parameter that limits the session's permissions

## What Does Start Lab Do?

- We are already authenticated by AWS academy
- When we click the "start lab" button:
  - It calls AWS STS (AssumeRole) on a preconfigured IAM role - voclabs
    - Passes a custom session name like user267027=name to track the authenticated user
    - May attach a session policy to restrict what this session can do
  - AWS STS responds with temporary credentials: SessionToken, SecretAccessKey, etc
  - AWS Academy uses the info to generate a federated user sign-in URL
    - This is the URL you get when clicking the green AWS button
  - AWS Academy may also use a CloudFormation template to warm up the lab session

## When We Click the Green AWS Session

- This URL grants us browser-based access to the AWS Console using the temporary credentials
- The URL is opened in a new tab to display the AWS console
- When the AWS console opens, AWS sets a lot of cookies to keep track of the session
  - aws-creds: contains the STS credentials
  - aws-sessionID: tracks the console session
  - Others

## When We Sign Out From AWS

- All cookies will be cleared
- But, as long as the lab session is still on, we can click the green AWS button to start a new session
- The button is associated with the same federated user sign-in URL
- When we access the AWS console within the same lab session, we are recognised by AWS as the same user

## What Does End Lab Do?

- The lab session is managed by AWS Academy
- When we click end lab:
  - All resources created during the lab are automatically cleared
  - The session permissions (session policy) are revoked or disabled
  - The AWS button is disabled
- If the AWS Console is still open, you can still see the UI but most actions will fail
- AWS Academy cannot clear the AWS Console cookies due to the browser's same-origin policy
- You need to explicitly logout of the current AWS session to visit the console of a new lab

## Session Expiration

- Both AWS and lab sessions expire after inactivity or a set time
- When AWS session expires:
  - Same effect as explicitly logging out, you lose access to the resources
  - If your lab session is still valid, you may be able to launch the console again and resume your work
- When the lab session expires
  - The effect is the same as clicking the end lab button
  - All resources are cleared automatically and the permissions are revoked or disabled
  - Your temporary credentials become invalid
  - You must click "Start Lab" again to get new credentials and restart the environment

## Clicking Start Lab Again

- Core action:
  - Resets the timer of a current lab session
  - Each lab session has a fixed duration that gets extended when you click Start Lab
- What doesn't change:
  - Your AWS Console credentials (temporary STS tokens) don't always refresh unless they are close to expiration
  - Your assumed role and session information remain the same
  - Resources created during the lab session remain intact

# Scalability and Elasticity

## What is Scalability?

- The property of a system to handle a growing amount of work
- Usually judged by how easy it is to add more resources to handle such changes

## What is Elasticity

- Supports fine grained capacity expansion and contraction
  - Scalability in cloud setting
- Scalability in a traditional setting is mostly about capacity expansion

## Scaling a Computer System

- Implemented by adjusting the amounts of resources based on workload
- Horizontal scaling: add more instances to handle the workload
- Vertical scaling: increase instance size (e.g. better CPU, RAM, etc)

## What is Required to Achieve Elasticity in Cloud

- The ability to monitor the workload and make scaling decisions accordingly
  - The cloud platform provides the mechanism, e.g. CloudWatch, AWS Auto Scaling
  - The users need to configure then properly
- The ability to automatically start/stop new resources with minimal latency
  - The cloud platform provides the mechanisms, e.g. EC2 instances can be created from AMI with very small latency
  - Users need to configure them properly
- The resource change should be recognised by the running system
  - Cloud platforms provides the mechanisms, such as Amazon Elastic Load Balancing that can direct requests to newly added resources
  - The users need to ensure that the functionalities of the application are not affected by adding or removing resources

# Resource Monitoring

## Amazon CloudWatch

- Monitors:
  - AWS resources
  - Applications that run on AWS
- Collects and tracks:
  - Standard metrics
  - Custom metrics
- Alarms:
  - Send notifications to an Amazon SNS topic
  - Perform EC2 Auto Scaling or EC2 actions
- Events:
  - Define rules to match changes in AWS environment and route these events to one or more target functions or streams for processing

## CloudWatch Alarms

- Create alarms based on:
  - Static threshold
  - Anomaly detection
  - Metric math expression
- Specify:
  - Namespace
  - Metric
  - Statistic
  - Period
  - Conditions
  - Additional configuration
  - Actions

# Scaling Compute Resource

## Amazon EC2 Auto Scaling

- Manages a logical collection of EC2 instances called an EC2 Auto Scaling Group across AZs
- Launches or retires EC2 instances configured by launch templates
- Resizes based on events from scaling policies, load balancer health check notifications, or schedule actions
- Integrates with Elastic Load Balancing to send new instance registrations and receive health notifications
- Balances the number of instances across AZs
- Free of charge

## EC2 Auto Scaling Group Components

- Capacity:
  - Minimum instances
  - Maximum instances
  - Desired instances
- Launch template:
  - EC2 instance configuration
  - Amazon Machine Image (AMI) ID
  - Version
- Load balancer:
  - Receives load balancer health checks
  - Registers new instances with load balancer
- Scheduling mechanisms:
  - Schedule action
  - Dynamic scaling policies
  - Predicitive scaling policy

## Auto Scaling Group - Capacity

- An ASG is a collection of EC2 instances that are treated as a logical grouping for the purposes of automatic scaling and management

- Horizontal scaling with EC2 ASG:
  - Group desired capacity = 2, maximum capacity = 3, minimum capacity = 1

## Auto Scaling Group - Launch Template

- Contains all information needed to launch an EC2 instance
  - Instance type (size of VM)
  - Image (AMI)
  - Security Group
  - User Data
  - The subnet for the VM isn't defined in the launch template, but in the ASG

## Scheduling Mechanism

- Schedule-based:
  - e.g. turn off development and test instances at night
- Policy-based:
  - Usually using a CloudWatch alarm
  - Maintaining a constant number
    - Always 2 instances
  - Target tracking:
    - Maintaining an average CPU load of 60%
  - Other policies

# Elastic Load Balancing

## Elastic Load Balancing

- A managed load balancing service that distributes incoming application traffic across multiple EC2 instances, containers, IP addresses, and Lambda functions
- Can be external-facing or internal-facing
- Each load balancer receives a DNS name
- Recognises and responds to unhealthy instances

## Types of AWS Load Balancers

- Application Load Balancer:
  - Used for HTTP and HTTPS traffic
  - Operates at OSI layer 7 (application layer)
  - Used for application architectures
- Network Load Balancer:
  - Used for TLS offloading, UDP, and static IP addresses
  - Operates at OSI layer 4 (transport layer)
  - Used for millions of requests per second at ultra-low latency
- Gateway Load Balancer:
  - Used for third-party virtual appliance fleet using GENEVE protocl
  - Operates at OSI layer 3, the network layer
  - Used to improve security, compliance, and policy controls
- Classic Load Balancer:
  - Used for previous generation EC2-Classic networks
  - Operates at OSI layers 4 and 7 (transport and application layers)
  - Used if upgrading to other load balancers is not feasible

## What Does a Load Balancer Do?

- Used to route incoming requests to multiple EC2 instances, containers, or other targets
- The server running on the EC2 instance must be stateless
  - A stateless server stores any shared data remotely in a database or storage system
  - The capacity can grow or shrink depending on the workload
- When you create an ELB and enable it in 1+ AZs, AWS provisions load balancer nodes within those AZs
  - These nodes are the actual components that receive incoming traffic and distribute it to your backend instances

## Load Balancer Components

- Request -> Listeners (Rules) -> Target Groups (Targets) -> Compute target

## ALB Components

- Listener
  - Defines the port and protocol which the load balancer must listen on
  - Each ALB must have at least one listener
  - Each ALB can have multiple listeners
  - Listeners define routing rules
- Target Group
  - Logical grouping of AWS resources as target for the traffic
    - e.g. an ASG can be specified as the target group
  - Load balancer performs periodic health check on targets in the target group
  - The request will only be routed to healthy targets

## Listeners and Rules

- Application Load Balancers are used to distribute HTTP and HTTPS requests
  - They support HTTP and HTTPS listeners
  - One ALB must have one listener
- Each listener defines routing rules for incoming requests
  - Rules consist of conditions and actions
  - When a request meets the conditions of the rule, the action is taken
  - Typical actions:
    - Forwarding to a target group
    - Redirect to a url
    - Responding back to the client

## Picking a Target to Forward the Request

- All load balancers use some load balancing algorithm to distribute incoming requests among targets in a target group
- ALB uses the following 2 algorithms:
  - Default: round-robin algorithm
    - Cycling through target
  - Configurable: Least Outstanding Request algorithm

## Least Outstanding Request Algorithm

- LOR selects the target with the lowest number of requests waiting for responses

## Target Group Health Check

- ALB periodically sends requests to its registered targets to test their status
- These tests are called health checks
- Requests are sent to healthy targets unless there is no healthy target
- The user needs to configure the health check settings:
  - Protocol
  - Path
  - Timeout seconds
  - Interval
  - etc

## ELB Health Check and ASG Health Check

- ASG always have EC2 level health check turned on
  - EC2 Auto Scaling checks that instances are running and monitors for underlying hardware or software issues that might impair an instance
- Can be configured to use ELB health check as well
  - If the target group marks an instance as unhealthy, the ASG will terminate it and depending on the policy, may start a new instance
  - Can detect application-level and replace instance accordingly

# Scaling Database Resource

## Vertical Scaling with Amazon RDS: Push-Button Scaling

- Scale DB instances vertically up or down
- From micro to 24xlarge
- Scale vertically with minimal downtime

## Horizontal Scaling with Amazon RDS: Read Replicas

- Horizontally scale for read-heavy workloads
- Up to 5 read replicas and up to 15 Aurora replicas
- Replication is asychronous
- Available for Amazon RDS for MySQL, MariaDB, PostgreSQL, and Oracle
