# Function as a Service

## A Berkeley View on Cloud Computing

- Paper published by UC Berkeley researchers
- Identified various XaaS models and highlighted 2 competing approaches to virtualisation in the cloud
  - "Amazon EC2 at one end of the spectrum. An EC2 instance looks much like physical hardware, and users can control nearly the entire software stack, from the kernel upward"
  - "At the other extreme of the spectrum are application domain-specific platforms such as Google App Engine, enforcing an application structure of clean separation between a stateless computation tier and a stateful storage tier"
- The market decision: IaaS won the first battle
  - AWS has the largest share in cloud market
  - Google, Microsoft, and other cloud companies offered similar interfaces

## IaaS: The First Model Embraced by Market

- Early cloud users wanted to recreate the same computing environment in the cloud that they had on the local environment
  - Experienced users, relative low migration cost, low risk if cloud computing could not go far
- Downsides:
  - Developers need to manage the VM to set up the environment
  - Needs a lot of administrative knowledge
- The success of cloud gave users more trust on the technology and generated requests for easier management
  - Users are willing to give up control for simpler operation

## Function as a Service

- "Serverless computing originated as a design pattern for handling low duty-cycle workloads, such as processing in response to infrequent changes to files stored in cloud"
  - e.g. send images from a phone application to the cloud, which should create a thumbnail image then place it on the web
    - Simple code, but may need an entire web application stack to run it
    - Running a server for infrequent requests is not cost effective
- In 2015, Amazon released AWS Lambda
  - User writes a function, specifies simple configuration requests
  - When the function is invoked, Amazon would start the necessary environment and run it
  - The user is charged based on the actual execution time
- The function should be stateless

## Serverless vs Serverful Computing

- Decoupled computation and storage
  - Computation and storage are scaled independently, computation is stateless, state is saved elsewhere (e.g. S3, Database, etc)
- Executing code without managing resource allocation
  - User provides a piece of code and the code automatically provisions resources to execute that code
- Paying in proportion to resources used instead of for resources allocation

## How Serverless Works

- A function usually runs in a container or other type of sandbox with limited resources launched by the provider

## Attractiveness of Serverless

- Providers perspective:
  - Business growth by bringing new customers and helping existing customers use more service types
  - More predictable resource usages means better utilisation
  - Cloud providers can also utilise less popular computers
- Customer perspective:
  - Programming productivity
  - Cost saving

## Limitations Caused by Storage

- Similar to containers, most serverless functions do not have persistent storage
  - Serverless relies on cloud storage services for maintaining states
  - Certain applications have fine-grained state sharing needs may suffer
    - Latency caused by retrieving and storing states
    - Cost caused by frequent querying
- Serverless functions typically use:
  - Object storages like AWS S3, Azure Blob storage, and Google Cloud storage has low storage cost, but high access cost and high access latency
  - Key-value databases, such as AWS DynamoDB, Google Cloud Datastore, or Azure Cosmos DB provide high IOPS, but are expensive and can take a long time to scale up

# Amazon Serverless Computing

## AWS Lambda

- A fully managed compute service
- Runs your code on a schedule or in response to events (e.g. changes to an A3 bucket or DynamoDB table)
- Supports Java, Go, PowerShell, Node.js, C#, Python, Ruby, and Runtime API
- Can run at edge locations closer to your users

## AWS Lambda Basic Concepts

- Function
  - A piece of code that can be run by Lambda
- Trigger
  - A resource or configuration that can invoke a Lambda function
- Event
  - A JSON formatted document that can be passed to a Lambda function for processing
  - Many AWS resources can generate events
- Execution environment
  - The container or VM that runs the function
- Runtime
  - The language specific environment in an execution environment
- Deployment package
  - A prebuilt package with code and other dependencies
  - ZIP file or container image formats are accepted

## AWS Lambda Typical Workflow

- Event source
  - Changes in data state
  - Requests from endpoints
  - Changes in resource state
  - Scheduled events
  - Publishes to AWS Lambda
  - Receives response from Lambda
- AWS Lambda
  - Runs a Lambda function
  - Can call AWS services
- Services

## Anatomy of a Lambda Function

- Handler()
  - Function to be run upon invocation
- Event object
  - Data sent during Lambda function invocation
- Context object
  - Methods available to interact with runtime information (request ID, log group, and more)

```python
import json

def lambda_handler(event, context):
    # TODO implement
    return {
        "statusCode": 200,
        "body": json.dumps("Hello World")
    }
```

## AWS Layers

- Without layers:
  - Each Lambda function contains:
    - Function code
    - Code dependencies
    - Custom runtimes
  - This leads to duplication:
    - Every function bundles its own dependencies, leading to larger package sizes
    - Harder to update and maintain
- With layers:
  - Lambda Layers are shared components you can attach to multiple functions
  - Separates:
    - Function code
    - Reusable logic:
      - Layer 1: Custom runtime
      - Layer 2: Code dependencies
  - Benefits:
    - Smaller function packages
    - Easier updates (change layer, not every function)
    - Shared logic across functions
    - Version-controlled components

## Lambda Local Storage

- Temporary local storage /tmp
- The storage is ephemeral, and only exists for the duration of function invocation
- Common use cases:
  - Downloading S3 files for processing
  - Storing intermediate results, etc

## Invoking Functions

- Through Lambda console for testing
- In a program using AWS SDK
- Invoke API
- AWS command like: `aws lambda invoke --function-name my-function`
- From another service by creating a trigger

# Lambda Execution Environment

## Multitenancy and Isolation

- Multitenancy is one of the features of cloud platforms
  - Applications from different customers can co-exist on the same hardware
- Offers lots of benefits for both cloud consumers and providers
  - e.g. elasticity, improved utilisation of servers, etc
- Isolating workloads from each other presents significant challenges to multitenancy
  - Security concerns
  - Performance concerns

## Trusted and Untrusted Workloads

- Workloads generated by internal applications can trust each other that no one would intentionally harm the system or each other's environment
  - The environment does not need to provide the highest level of isolation
  - Cloud focuses more on performance optimisation
  - Kubernetes and ECS are designed to run trusted workloads
    - The containers collocate in the same pod give up isolation for performance gain
- Workloads generated by different clients cannot have such trust
- Some workloads could be malicious by design, others could contain vulnerabilities or bugs that could be exploted to compromise the system or data
  - The environment needs to provide a very strong level of isolation

## Lambda Execution Environment

- Early versions of Lambda uses container technology to isolate functions and virtualisation technology to isolate user accounts
  - Each function runs on its own container when invoked
  - Multiple functions from the same user account may run on the same VM
  - The functions from the different user account will always run on different VMs
- Potential consequences on resource usage:
  - At least one VM for each user account resulted in inefficient resource usage
  - Scheduling algorithms cannot be optimised for resource usage

## MicroVM

- Serverless computing needs to provide a platform for untrusted workloads to share resources efficiently
  - Workloads from different user accounts can be scheduled on the same node
  - VM level isolation is desirable
    - Each workloads runs inside its own VM
- The VM is used to run some function for a short period of time
  - Many traditional OS tools or device drivers are not needed
- A specialised VM, MicroVM would provide better utilisation and performance
- A minimum VM that offers high isolatoin and relatively low resource consumption
  - In between a container and traditional VM

- Container:
  - Isolation: medium
  - Resource consumption: low
  - Start latency: low
  - Use case: microservice application, deployment everywhere
- MicroVM:
  - Isolation: high
  - Resource consumption: medium
  - Start latency: medium
  - Use case: serverless computing
- Traditional VM:
  - Isolation: high
  - Resource consumption: high
  - Start latency: high
  - Use case: general purpose

## Lambda Function Location Options

- By default:
  - Lambda functions run in a Lambda-managed VPC
  - It has access to the public internet
    - Allows them to connect to external services
  - We can't configure or see this VPC
  - Lambda functions in their default configuraion can access other outside VPC services like S3, DynamoDB, SecretsManager through preconfigured endpoints
  - Can't access resources on our own VPC, such as RDS

## Placing Lambda Function Inside a VPC

- An easy way is to place the Lambda function inside a VPC, on a public or private subnet and configure VPC endpoints for the services that don't reside in a VPC

# Lambda Execution Anti-Patterns

## General Design Principle

- Having many, short functions is preferred over fewer, larger ones
- Each function should be designed to just handle the event passed into the function
- Functions are not expected to have knowledge of the overall workflow or other functions

## The Lambda Monolith

- A single function contains all application logic and is triggered for all events
- Has all the issues of a non-modularised code
  - Hard to upgrade
  - Hard to maintain
  - Hard to reuse
  - Hard to test
- Some cloud specific issues
  - Package sizes too big
  - Hard to enforce least privilege

## Better Alternative to Lambda Monolith

- Microservice application
- Lambda function for every function

## Recursive Patterns that Cause Runaway Lambda Functions

- Generally, the service or resource that invokes a Lambda function should be different to the service or resource that the function outputs to
- When an S3 upload event triggers a Lambda function which puts an object back in the same bucket, the trigger event needs to be carefully configured

## Lambda Functions Calling Lambda Functions

- Deep function call stack doesn't work well in serverless environments
- Error handling becomes more complex
- Workflow is limited by the slowest function
- Scaling complexities

# Amazon API Gateway

## Amazon API Gateway

- Enables you to create, publish, maintain, monitor and secure APIs that act as entry points to backend resources for your applications
- Handles up to hundreds of thousands of concurrent API calls
- Can handle workloads that run on:
  - EC2
  - Lambda
  - Any web application
  - Real-time communication applications
- Can host and use multiple versions and stages of your APIs

## Amazon API Gateway Security

- Require authorisation
- Apply resource policies
- Throttling settings
- Protection from Distributed Denial of Service and injection attacks


