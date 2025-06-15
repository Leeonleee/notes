# AWS Shared Responsibility Model

## Shared Responsibility Model

Customer: Responsibility for security "in" the cloud

- Customer data
- Platform, applications, identify and access management
- Operating system, network and firewall configuration
- Client-side data encryption and data integrity authentication
- Server-side encryption (file system and/or data)
- Networking traffic protection (encryption, integrity, identity)

AWS: Responsibility for the security "of" the cloud

- Software
  - Compute
  - Storage
  - Database
  - Networking
- Hardware/AWS Global Infrastructure
  - Regions
  - Availability Zones
  - Edge locations

## AWS Responsibility: Security of the Cloud

- AWS services:
  - Compute, Storage, Database, Networking
- AWS responsibilities:
  - Physical security of data centres
    - Controlled, need-based access
  - Hardware and software infrastructure
    - Storage decommissioning, host operating system access logging, and auditing
  - Network infrastructure
    - Intrusion detection
  - Virtualisation infrastructure
    - Instance isolation

## Customer Responsibility: Security in the Cloud

- Customer responsibilities:
  - Amazon EC2 instance OS
    - Including patching, maintenance
  - Applications
    - Passwords, role-based access, etc
  - Security group configuration
  - OS or host-based firewalls
    - Including intrusion detection or prevention systems
  - Network configurations
  - Account management
    - Login and permission settings for each user

## Service Characteristics and Security Responsibility
- Example services managed by the customer:
  - EC2, EBS, VPC
- Example services managed by AWS:
  - Lambda, RDS, Elastic Beanstalk

- Infrastructure as a Service:
  - Customer has more flexibility over configuring networking and storage settings
  - Customer is responsible for managing mroe aspects of the security
  - Customer configures the access controls
- Platform as a Service:
  - Customer does not need to manage the underlying infrastructure
  - AWS handles the OS, database patching, firewall configuration, and disaster recovery
  - Customer can focus on managing code or data
- Software as a Service:
  - Software is centrally hosted
  - Licenced on a subscription model or pay-as-you-go basis
  - Services are typically accessed via web browser, mobile app, or API
  - Customers do not need to manage the infrastructure that supports the service

# Identity and Access Management

## Identify and Access

- Identity:
  - Each entity (e.g. user, administrator, system) needs an identity
  - The process of verifying that identity is called authentication
- Access:
  - Access management is about ensuring that entities can perform only the tasks they need to perform
  - The process of checking what access an entity should have is called authorisation

## Typical Identity Management in Linux OS

- Users
  - Root user: user id 0
    - Created when the OS is installed
  - System/service users: user id 1-999
    - Created when certain packages are installed
  - Regular/normal/local users: user id > 1000
    - Usually one regular user will be created during installation
    - Other can be created by root or admin user
- Groups
  - Most Linux OS have a sudo group to represent users with admin privilege
  - Other groups can be created

## Typical Security Practise

- Root user is disabled by default in many Linux systems, including AWS EC2 instances
  - An initial user is added to the sudo group
  - Sudo allows an authorised use to temporarily elevate their privileges using their own password instead of having to know the password belonging to the root account

## Typical Identity Management in Application

- Example: in MySQL
  - Account Users
  - Privileges:
    - Administrative, database, table, etc
  - Roles:
    - Represents collections of privileges
  - Privileges can be granted to users
  - Roles can be set for users

## Terminologies and Concepts in AWS

- Users: account users and IAM users
- Groups: IAM groups, representing a collection of IAM users
- Roles: can be assumed by user or services
- Policies: define the actions permitted

## Securing the Root Account

- The account root user has a large amount of power
- Recommended security steps:

1. Create an IAM admin user
2. Lock away the root user credentials
3. Further access to account for most tasks via:

  - AWS Management Console
  - AWS CLI
  - AWS Tools and SDKs

## IAM Components: Review

- IAM user: defined in your AWS account, use credentials to authenticate programmatically or via the AWS Management Console
- IAM group: a collection of IAM users that are granted identical authorisatino
- IAM policy: defines which resources can be accessed and the level of access to each resource
- IAM role: mechanism to grant temporary access for making AWS service requests. Assumable by a person, application, or service

## IAM User Authentication: Review

- When you define an IAM user, you select what types of access the user is permitted to use
- Programmatic access:
  - Authenticate using:
    - Access key ID
    - Secret access key
  - Provides AWS CLI and AWS SDK access
- AWS Management Console access:
  - Authenticate using:
    - 12-digit Account ID or alias
    - IAM user name
    - IAM password
  - If enabled, MFA prompts for an authentication code

## Group and Roles in Non-Cloud Setting

- The "group" concept is mainly used in OS identity management
- The "role" concept is mainly used in application identity management
- Refer to a similar mechanism
  - Representing a collection of permissions that can be granted to different users
  - A user can be assigned to multiple groups or roles

## Group and Roles in AWS

- They represent a collection of permissions
- IAM group is similar to the group concept in OS and the role concept in application
- IAM roles are used to handle cloud specific authorisation scenarios
  - Allow AWS resources to use AWS services
    - e.g. allow EC2 instance to access S3 or Parameter Store in system manager
  - Provide access to externally authenticated users
  - Provide access to third parties
  - Provide cross-account access

## IAM Groups

- Use IAM groups to grant the same access rights to multiple users
- All users in the group inherit the permissions assigned to the group
  - Makes it easier to manage access across multiple users
- Combine approaches for fine-grained individual access
  - Add the user to a group to apply standard access based on job function
  - Optinally attach an additional policy to the user for needed exceptions

## Challenge of Managing Permissions

- Assigning permissions directly to users is difficult to manage
- Initial configuration
  - Each developer is given full access to EC2 through policies that are attached to individual users
- Additional access is needed
  - Each developer needs access to S3
  - To implement this change, an administrator needs to make 3 modifications, one for each AWS IAM user's policy
- The number of developers grows
  - This approach becomes hard to manage

## Use IAM Groups to Attach Permissions to Multiple Users

- Permissions in a group can be based on a job function
- All users in the group inherit the permissions assigned to a group

## IAM Roles

- IAM role characterisics
  - Provides temporary security credentials
  - Not uniquely associated with one person
  - Assumable by a person, application, or service
  - Often used to delegate access
- Use cases:
  - Provide AWS resources with access to AWS services
  - Provide access to externally authenticated users
  - Provide access to third parties
  - Switch roles to access resources in:
    - Your AWS account
    - Any other AWS account

# IAM Policies

## IAM Policies and Permissions

- Use policies to fine-tune the permissions that are granted to principals
- 2 types of policies
  - Identity-based: attach to an IAM user, group, or role
  - Resource-based: attach to an AWS resource
- Define permissions in IAM policy documents
  - The document is formatted in JSON
  - The policy defines which resources and operations are allowed or denied
  - Follow the principle of least privilege

## IAM Policy Document Structure

- Version: version of the policy language that you want to use
- Statement: defines what is allowed or denied based on conditions
- Effect: allow or deny
- Principal:
  - For a resource-based policy, the account, user, or federated user to allow or deny access to
  - For an identity-basd policy, the principal is implied as the user or role that the policy is attached to
- Action:
  - Action that is allowed or denied
  - e.g. "Action": "s3:GetObject"
- Resource:
  - Resource or resources that the action applies to
  - e.g. "Resource": "arn:aws:sqs:us-west-2:1234566789012:queue1"
  - ARN = AWS resource name
- Condition: conditions that must be met for the rule to apply

## IAM Policy Document Structure Example

```
{
    "Version": "2012-10-17",
    "Statement": [{
        "Effect": "effect",
        "Action": "action",
        "Resource": "arn",
        "Condition": {
            "condition": {
                "key": "value"
            }
        }
    }]
}
```

- Effect: either Allow or Deny
- Action: type of access that is allowed or denied
  - e.g. "Action": "s3:GetObject"
- Resource: resources that the action will act on
- Condition: conditions that must be met for the rule to apply

```
"condition": {
    "StringEquals": {
        "aws:username":"johndoe"
    }
}
```

## ARNs and Wildcards

- Resources are identified using Amazon Resource Name (ARN) format
  - Syntax: arn:partition:service:region:account:resource
  - Example: "Resource": "arn:aws:iam::123456789012:user/major"
- You can use a wildcard (*) to give access to all actions for a specific AWS service
  - e.g. "Action": "s3:*", "Action":"iam:*AccessKey"

# Attributed Based Access Control

## Challenges of Scaling with Role-Based Access Control (RBAC)

- To setup RBAC with IAM, do the following:
  - Create an IAM policy with the permissions for the job role
  - The policy lists the individual resources to be accessed
  - Attach the policy to an IAM entity (user, group, or role)
- To update policies when access to a new resource ie needed:
  - Update the policy
  - Modify multiple policies if the new resource is used by multiple roles or to add access to multiple resources

## Using Attribute-Based Access Control (ABAC)

- This authorisation strategy defines permissions based on attributes
- Attributes are a key or a key-value pair
- In AWS, these attributes are called tags
- Tags can apply to IAM resources (users or roles) and AWS resources

- Benefits:
  - More flexible than policies that require you to list each individual resource
  - Granular permissions are possible without a permissions update for every new user or resource
  - Highly scalable approach to access control
  - Fully auditable

## Tagging in AWS

- Tags are a resource metadata consisting of a key/value pair
- Can apply to resources across AWS accounts and IAM users or roles
- Customers can create user-defined tags
- Many different AWS API operations return tag keys and values
- Can have multiple uses like billing, filtered views, and access control
- Example tags applied to an EC2 instance:
  - Name: Web Server
  - Project: Unicorn
  - Env: Dev

# Federating Users

## Identity Federation

- A system of trust between two parties to authenticate and convey information
- Identify provider (IdP) is responsible for user authentication
  - Examples:
    - OpenID connect (OIDC) IdPs like Login with Amazon, Facebook, Google
    - Security Assertion Markup Language (SAML) IdPs like Shibboleth or Active Directory Federation Services
- Service provider (SP) is responsible for controlling access to its resources
  - Examples:
    - AWS services
    - Social media platforms
    - Online bank

## AWS Services that Support Identity Federation

- AWS Identity and Access Management
- AWS IAM Identity Center (successor to AWS Single Sign-On)
- AWS Security Token Service
- Amazon Cognito

## AWS IAM Identity Center

- Successor to AWS Single Sign-On
- Can create or connect identities once and manage access centrally across your AWS accounts
- Provides a unified administration experience to define, customise, and assign fine-grained access
- Provides a user portal to access all assigned AWS accounts or cloud applications
- Used optionally in conjunction with IAM

## AWS Security Token Service (AWS STS)

- A web service (API) that enables you to request temporary, limited-privilege credentials
- The credentials can be used by IAM users, federated users, or applications

## Identify Federation to AWS with an Identify Broker

1. User signs in with existing credentials for their IdP
  - Users sign in using an identity that is already known by an IdP
  - e.g. Amazon.com ID or corporate login
2. Identity broker acts an an intermediary between IdP and SP
  - The identity broker requests temporary credentials from AWS STS
3. AWS STS generates temporary credentials dynamically
  - Credentials last from a few minutes to several hours and are not recognised after they expire
4. Identity broker passes temporary credentials to application
  - AWS STS returns the temporary credentials to the identity broker
  - The identity broker passes them onto the application for the user

## Amazon Cognito

- A fully managed service that provides the following services and features:
  - Authentication, authorisation, and user management for web and mobile applications
  - Federated identities for sign in with social identity providers (Amazon, Facebook, Google) or with SAML
  - User pools that maintain a directory with user profiles authentication tokens
  - Identity pools that enable the creation of unique identities and permissions assignment for users




