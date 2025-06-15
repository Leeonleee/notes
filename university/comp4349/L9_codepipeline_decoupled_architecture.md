# DevOps and CI/CD

## DevOps

- A set of practises, cultural philosophies, and tools that aim to integrate and automate the processes between software development (Dev) and IT operations (Ops)
- The ultimate goal is to enable faster and more reliable software delivery with improved collaboration and communication between teams

## DevOps Culture

- Increased transparency, communication and collaboration between teams
- Shared responsibilities
- Autonomous teams
- Fast feedback
- Automation

## DevOps Best Practise

- Agile project management
- Shift left with CI/CD
- Build with the right tools
  - DevOps life cycle: discover, plan, build, test, monitor, operate, continuous feedback
- Implement automation
- Monitor the DevOps pipeline and applications
- Observability
  - 3 pillars of observability are logs, traces, and metrics
- Change the culture
  - You build it you run it

## AWS Supported DevOps Practise

- Microservice architecture
- Continuous integration and continuous delivery (CI/CD)
- Continuous monitoring and improvement
- Automation focused
- Infrastructure as code

## DevOps Tools

- CI/CD
  - AWS CodePipeline
  - AWS CodeBuild
  - AWS CodeDeploy
  - AWS CodeConnections
- Microservices
  - Amazon Elastic Container Service (Amazon ECS)
  - AWS Lambda
  - AWS Fargate
- Platform as a Service
  - AWS Elastic Beanstalk
- Infrastructure as Code
  - AWS CloudFormation
  - AWS OpsWorks
  - AWS Systems Manager
- Monitoring and Logging
  - Amazon CloudWatch
  - AWS CloudTrail
  - AWS X-Ray
  - AWS Config

## Continuous Integration

- A software development practise where members of a team use a version control system and frequently integrate their work to the same location, e.g. main branch
- Each change is built and verified to detect integration errors as quickly as possible
- Focused on automatically building and testing code
  - Version control system to manage the code change
  - A CI server to manage the automated process of building, testing, and validating changes

## Continuous Delivery/Continuous Deployment

- Continous Delivery extends CI by automatically releasing software to a repository
- Continous Deployment extends the process further by adding the step of automatically deploying software to production

# AWS CodePipeline

## CI/CD on AWS

- CI/CD is usually pictured as a pipeline with each stage structured as a logic unit in the delivery process
  - Specialised tool for each stage
  - Manual checking can be added at various points
  - Some stages may be skipped for certain pipelines

## AWS CI/CD Pipeline Components

- AWS can set up CI/CD pipelines using various development tools
  - AWS CodeCommit
  - AWS CodeBuild
  - AWS CodePipeline
  - AWS CodeDeploy
- The pipeline can be used to manage application code and infrastructure
  - Pipelines for infrastructure
  - The pipelines in the lab contains only two stages

## CodePipelines

- A service that allows users to automate steps required to release/deploy software on AWS easily

## Pipeline

- A pipeline is a workflow contruct that describes how software changes go through a release process
- Each pipeline is made up of several stages
  - The simplest pipeline may contain only two stages

## Stages

- Stage is a logic unit representing a relatively independent step in the process where actions can be performed on the artifact, such as source code
- There are stages corresponding to typical software development stages
  - Build, test, etc

## Pipeline Execution

- Traversing the stages in order and complete actions defined in the stages, which may end up with a successful execution or failed execution
- Pipeline can be configured to automatically start by triggers such as commit event

# Decoupled Architecture

## Tight Coupling in a Three-Tier Architecture

1. Web Tier: Web server (on EC2)
2. Application Tier: App server (on EC2)
3. Data Tier: Database (Amazon RDS)

- All communication is synchronous
- Direct dependencies: web server directly communicates with the application server
- Challenges:
  - Code changes for scaling
    - To scale out, you must update your code
    - Tight coupling makes dynamic scaling harder and slower
  - Single point of failure
    - If one app server fails, the system may become unavailable

## Tight Coupling Increases the Complexity of Scaling

- An application outage impacts all connected web servers
- Each new server requires multiple connections that must be updated in code

## Loose Coupling Between Tiers

- ALB: automatically manages workloads and failover routing
- Adding a new web server requires only two new connections

## Tight Coupling Within an Application

- Challenge: the failure of one function can bring down the entire application

# AWS SQS

## Point-to-Point Messaging

- You can decouple applications asychronously by using point-to-point messaging
- Use point-to-point messaging when the sending application sends a message to only one specific receiving application
- The sending application is called a producer
- The receiving application is called a consumer
- Point-to-point messaging uses a message queue to decouple applications

## Amazon Simple Queue Service

- Is a fully managed message queueing service
- Helps integrate and decouple distributed software systems and application components
- Provides highly available, secure, and durable message-queueing capabilities
- Provides an AWS Management Console interface and web services API

## Amazon SQS Basic Components

- A message can be up to 256 KB in size
- A message remains in a queue until it is explicitly deleted or exceeds the queue's message retention period
- Amazon SQS offers two types of queues: standard and first-in-first-out
- You can configure queue parameters, including:
  - Message retention period
  - Visibility timeout
  - Receive message wait time
- You can associate a dead-letter queue (DLQ) with any queue
- A DLQ stores messages that cannot be consumed successfully

## Queue Types

- Standard Queue
  - At-least-one delivery
  - Best-effort ordering
  - Nearly unlimited throughput
- FIFO Queue
  - FIFO delivery
  - Exactly once processing
  - High throughput

## Queue Configuration: Polling Type

- Short polling:
  - Definition: immediate request, returns message if available
  - Wait time: 0 seconds
  - Behaviour: sends multiple rapid requests
  - Use case: low latency requirements, but less efficient and may return empty responses
- Long polling:
  - Definition: waits for messages to arrive before responding
  - Wait time: > 0 seconds
  - Behaviour: reduces the number of empty responses and API calls
  - Use case: more efficient, preferred for reducing cost and latency in message retrieval

## Queue Configuration: Message Visibility

- Once a message is retrieved from the queue, it becomes invisible for a set period (visibility timeout)
- If not processed and deleted within this time, it reappears and can be reprocessed by another consumer

## Amazon SQS Message Lifecycle

- Create: producer sends message to the queue, which gets distributed redundantly
- Process: message gets picked up for processing by a customer and the visibility timeout starts
- Delete: message gets deleted by the consumer after it is processed

# AWS SNS

## Publish/Subscripe Messaging

- You can decouple applications asynchronously by using publish/subscribe messaging
- Use pub/sub messaging when the sending application sends a message to multiple receiving applications and has little to no knowledge about the receiving applications
- The sending application is called a publisher
- The receiving application is called a subscriber
- Pub/sub messaging uses a topic to decouple application

## Amazon Simple Notification Service

- Is a fully managed pub/sub messaging service
- Helps decouple appliations through notifications
- Provides highly scalable, secure, and cost-effective notification capabilities
- Provides an AWS Management Console interface and a web services API

## Types of Subscribers

- Email destination
- Mobile text messaging destination
- Mobile push notifications endpoint
- HTTP or HTTPS endpoint
- AWS Lambda function
- SQS queue
- Amazon Kinesis Data Firehose delivery stream

## Amazon SNS Use Cases

- Application and system alert notification
- Email and text message notification
- Mobile push notification

## Amazon SNS Consideration

- Message publishing
  - Single published message
  - No recall options
- Message delivery
  - Use a standard topic if the message delivery order does not matter
  - Use a FIFO topic if an exact message delivery order is required
  - Customise the delivery policy of HTTP or HTTPS endpoint to control the retry behaviour

## Amazon SNS and Amazon SQS Comparison

- Amazon SNS:
  - Messaging model: publisher-subscriber
  - Distribution model: one to many
  - Delivery mechanism: push (passive)
  - Message persistence: no
- Amazon SQS:
  - Messaging model: producer-consume
  - Distribution model: one to one
  - Delivery mechanism: pull (active)
  - Message persistence: yes


