# AWS CLI

## AWS Services and API Calls

- Services are created and managed by sending requests to the corresponding API
  - Through a web-based GUI like management console
  - Command line interface like AWS CLI
  - Programmatically via SDK
- Creating an EC2 instance:
  - Perform an ec2:RunInstances call

## Four Options of Interacting with AWS

- They represent different end user interfaces of the same API

1. Management Console
2. Command line
3. SDK
4. Blueprints

## Risks from Manual Processes

- Does not support repeatability at scale
  - How do you replicate deployments to multiple Regions?
- No version control
  - How do you roll back the production environment to a prior version?
- Lack of audit trails
  - How will you ensure compliance?
  - How will you track changes to configuration details at the resource level?
- Inconsistent data management
  - e.g. how will you ensure matching configurations across multiple EC2 instances?

## AWS CLI

- Convenient way to interact with AWS from a terminal
- Requires installation and configuration
  - CLI request authenticates the user by access key ID and secret access key
  - A configuration process would store the information to be used by each request
- EC2 instance with Amazon AMI usually comes with AWS CLI preinstalled and configured
- Example commands:
  - `aws configure`
  - `aws s3 ls`
  - `aws ec2 describe-instances`
  - `aws cloudformation create-stack..`

## CLI Command Examples

```bash
aws ec2 run-instances \
  --image-id ami-0ccedee93274bbb8d \
  --instance-type t2.micro \
  --count 1

aws s3api create-bucket --bucket abcd1234 --region us-east-1
```

# Infrastructure as Code (IaC)

## IaC Overview

- The process of writing a template that provisions and manages your cloud resources
  - Human readable, machine consumable
- With IaC, you can replicate, redeploy, and repurpose infrastructure

## IaC Benefits

- Rapidly deploy complex environments with configuration consistency
- Propagate a change to all stacks by modifying the template
- Clean up by deleting the stack, which deletes the resources created
- Key benefits are reusability, repeatability, and maintainability

## CloudFormation

- Provides a simplified way to model, create, and manage a collection of AWS resources
- A collection of resources is called a CloudFormation stack
- There is no extra charge (pay only for the resources that you create)
- Can create, update, and delete stacks
- Enables orderly and predictable provisioning and updating of resources
- Enables version control of AWS resource deployments

## How CloudFormation Works

1. Define your resources in a template, or use a prebuilt template
2. Upload the template to CloudFormation, or point to a template stored in an S3 bucket, or use `aws cloudformation`
3. Run a create stack action, resources are created across multiple services in your AWS account as a running environment
4. The stack retains control of the resources that are created. You can later update stack,detect drift, or delete stack

## Stacks

- A collection of AWS resources that can be managed as a single unit
- Stacks are created based on CloudFormation templates
  - Think of template as a class and stacks as objects created based on that class
- There are different ways of grouping resources into a stack
  - Cohesion
  - Lifecycle
  - Security
  - Others
- A layer approach is typical: network layer, application layer, database layer

# CloudFormation Template

## CloudFormation Template Syntax

- YAML example

```
AWSTemplateFormatVersion: 2010-09-09
Resource:
    awsexamplebucket1:
        Type: AWS::s3::Bucket
```

- JSON example

```
{
    "AWSTemplateFormatVersion": "2010-09-09",
    "Resources" : {
        "awsexamplebucket1": {
            "Type": "AWS::s3::Bucket"
        }
    }
}
```

- YAML advantages:
  - Optimised for readability
  - Less verbose:
    - No brackets
    - Quotes can be omitted most of the time
  - Supports embedded comments
- JSON advantages:
  - More widely used by other computer systems (e.g. APIs)
  - Usually less complex to generate and parse

## Anatomy of a CloudFormation Template

- When you write a CloudFormation template, you have a choice of sections to include based on your workload
- The only required section is Resources
- Example of a YAML-formatted template fragment in the suggested order of sections:

```
AWSTemplateFormatVersion: "version date"
Description:
    String
Metadata:
    template metadata
Parameters:
    set of parameters
Rules:
    set of rules
Mappings:
    set of mappings
Conditions:
    set of conditions
Transform:
    set of transforms
Resources:
    set of resources
Outputs:
    set of outputs
```

## CloudFormation Resources

- The resources section contains multiple resources
- A resource has at least a name, type, and some properties

```
Resources:
    resourceName1:                              // Chosen by the user
        Type: AWS::ServiceName::ResourceType    // string with given format
            Properties:
                PropertyName1:PropertyValue1    // properties that can be included depends on the resource type
    ...
    resourceName2:
    ...
```

## CloudFormation Parameters

- The Paramter section is optional but is a powerful way to customise the stacks created
  - Think of this as defining a parameterised constructor to allow you to pass arguments to create different objects
  - Typically use to specify property values of stack resources

```
Parameters:
    ParameterLogicalID:
        Description: information about the parameter
        Type: DataType  // String, Number, or an AWS-specific parameter type
        Default: value
        AllowedValues:
            - value1
            - value2
```

## CloudFormation Output

- Optional section to declare the output values for the stack
  - Users define what data they want to pass as the output

```
Outputs:
    OutputLogicalID:
        Description:
        Value: value to return
        Export:
            Name: name of the resource to export
```

## Intrinsic Functions

- A list of built-in functions that can be used in the templates to assign values to peroperties that are not available until runtime
- Basic syntax:
  - `Fn::func_name inputs`
  - YAML short form: `!func_name input(s)`
- `Ref` is also an intrinsic function
  - `Ref input`
  - Returned value depends on the input specified
    - The value of the specified parameter or resource
    - The output of a function

## Infrastructure Composer

- A visual way to work with CloudFormation templates

# Creating Stack from AWS CLI

## Creating Stack

```
aws cloudformation create-stack \
    --stack-name mybucket \
    --template-body file://my_bucket.yaml
```

```
// my_bucket.yaml
// creating S3 bucket with default setting

AWSTemplateFormatVersion: "2010-09-09"
Description: This is my first bucket
Resources:
    MyBucket:
        Type:AWS::S3::Bucket
```

## Updating Stack

```
aws cloudformation update-stack \
    --stack-name mybucket \
    --template-body file://my_bucket.yaml
```

```
AWSTemplateFormatVersion: "2010-09-09"
Description: This is my first bucket
Resources:
    MyBucket:
        Type:AWS::S3::Bucket
        Properties:
            AccessControl:PublicRead
```

## Supplying Parameters

```
aws cloudformation create-stack \
    --stack-name my-s3-bucket-stack \
    --template-body file://s3-bucket.yaml \
    --parameters ParamaterKey=BucketName,ParameterValue=my-unique-bucket-name
```

```
AWSTemplateFormatVersion:'2010-09-09'
Description: creates an S3 bucket with a user provided name

Parameters:
    BucketName:
        Type:String
        Description: The name of the S3 bucket to create
Resources:
    MyS3Bucket:
        Type: AWS::S3:Bucket
        Properties
            BucketName: !Ref BucketName
```

