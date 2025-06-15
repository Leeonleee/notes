# Container


## Containerisation

- Operating system level virtualisation
- Refers to an operating system feature in which the kernel allows the existence of multiple isolated user-space instances
- These instances are called containers, partitions, virtualisation engines or jails, and look like real computers from the POV of programs running in them

## Linux Kernel, System, and Distribution

- Linux kernel is the most basic component of a Linux OS
  - Does 4 basic jobs:
    - Memory management
    - Process management
    - Device drivers
    - System calls and security
- Linux system
  - Kernel + system libraries and tools
    - e.g. GNU tools like gcc
- Linux distribution
  - Pre-packaged Linux system + more applications
    - e.g. news servers, web browsers, text-processing and editing tools
  - Redhat, Debian, Amazon Linux, Android?

## Linux Kernel Feature: Namespace

- To create the illusion of a full computer system for each container, we need to give each a "copy" of the kernel resources
  - An independent file system starting with a root directory
  - An independent set of process ids, with id 1 assigned to init process
  - An independent set of user ids, with id 0 assigned to root user
  - etc...
-  To avoid confict, the OS provides a feature called namespace
- The namespace provides containers their own view of the system

## Namespace Kinds

- Early Linux had a single namespace for each resource type
- Gradually, namespaces were added to different resources
- Mount (mnt)
  - Deals with the file system
  - Each container can have its own rootfs
  - Each container manages its own mount points
- Process ID (pid)
  - Each container has its own numbering starting at 1
  - When PID 1 goes away, all other processes exit immediately
  - PID name spaces are nested. The same process may have different PIDs in different namespaces
- User ID
  - Provide user segregation and privilege isolation
  - There is a mapping between container UID to host OS UID
  - UID 0 (root) in container may have a different UID in the host

## Linux Kernel Feature: Cgroup

- Control Groups is another kernel feature to enable isolated containers
- Used to control kernel resources allocated to each container/process
  - Metering and limiting
- Resources include:
  - Memory, CPU, I/O (file and network)

## Container Runtimes

- Runtime
  - Lifecycle of a program, e.g. runtime error
  - Language specific environment to support its execution, e.g. JRE
- Container runtime has similar respnosibilities as a Java Runtime Environment
  - Enables containers to run by setting up namespces and cgroupsA
- Low level container runtimes: focus on just running containers
  - LXC, Systemd-nspawn, OpenVZ, Sandboxie, etc
- High level container runtimes: contain other features like defining image formats, managing images, etc
  - e.g. Docker

## Container vs Virtual Machine

- All containers use the kernel of the host machine
- Each VM contains a full OS

- Operating Systems and Resources
  - Running a full OS inside a VM takes up a certain amount of resources even if no app is running in the VM
  - Starting OS takes some time
  - Container exits when the process inside it finishes unless we specify an interactive container, or run a server in it
  - A container is much faster to start, similar to starting an application
- Isolation for performance and security
  - VMs have very good isolation and security
  - Have hardware support and mature technology
  - Containers offer reasonable levels of isolation using kernel techniques like namespace and control groups


# Docker Overview

## Overview

- Docker is the most famous container runtime
- Not just a container runtime, also a packaging and deployment system built on container technology
- Has a large ecosystem of various components
- Container part is usually a black box where docker users don't need to know a lot about how it works
- For most users, the dependency management and deploy everywhere are the most prominent features

## Main Concepts

- Images: something you package your application and its environment into
- Registries: Docker Registry is a repo that stores your Docker images and facilitates easy sharing of thsoe images
- Containers: a regular container created from a Docker-based container images. A running container is a process running on the host running Docker

1. Developer tells Docker to build and push image
2. Docker builds the image
3. Docker pushes image to registry
4. Developer tells Docker on production machine to run image
5. Docker pulls image from registry
6. Docker runs container from image

## Docker Image

- Docker images are defined in Dockerfile
  - A text document containing a sequence of instructions/commands to build and run your application
  - Similar purpose to a Makefile
  - You declare dependencies, set environment variables and other configurations in it
- Docker images are composed of read-only layers
- Base layers can be shared by many images

## Container and Image Layers

- Images are read-only templates, all image layers are read-only
- When an image is loaded into a container to run, the container adds a thin writable layer on top of it
  - All writes to the container are stored in this layer
  - It is deleted when container exits
  - Should be used as temporary storage
- If multiple images use a same base image, only one copy of the base image is required
  - Small space
- When a container starts, only a new writable layer is added on top of the existing image layers
  - Fast startup
- Application persistence should be handled differently

## Docker Container

- The actual container uses many OS technologies to provide isolation and to allocate resources
  - CPU, memory, I/O
- Achieved using Linux kernel features
  - Namespaces, Cgroups, others
- Linux Containers (LXC) was used as the execution driver
- The current driver Libcontainer is more general, allowing Docker to run on platforms other than Linux

## Data Volume

- Apart from storing data within the writable layer of a container, there are other preferred ways to persist data
  - Bind monut is an early version storage option, allows a client to mount any file or directory on the host machine into a container
  - Volumes use a designated location in the host machine as container storage, completely managed by Docker
  - tmpfs is a rarely used option, which uses host memory to simulate storage

## Networking

- Docker containers have networking enabled by defauly and they can make outgoing connections
- Docker provides a few options for containers to decide how they want to connect with the outside:
  - Host
  - Bridge: default/user defined
  - None
  - Overlay
  - Others...
- There are specified during the start of the container, and if nothing is specified the default bridge driver is used

## Networking: Host

- Host driver is the simplest option
- Removes the isolation between container and host
- Container is treated the same way as a process in the host
- Connects directly to the host NIC (host networking namespace)

## Networking: Bridge

- Docker manages its own private network
- Adding a software bridge to the host
- The mapping from internal/private address to public/host-based address is done through a Network Address Translation

# Microservices Architecture

## MVC Architecture

- Web tier
- Application tier
- Database tier
- Components are strongly connected to each other

## Microservices Architecture

- Decouple services into individual services that can be deployed separately from one another on separate hosts
- e.g. RESTful API over HTTP

## Container and Microservices

- Each container is supposed to run a single service
  - Deployment is simplified since all dependencies are included in the container
  - More resource efficient than running a service on a VM
- Docker focuses on single service deployment
  - Start a single Jupyter notebook service in a container
  - Start a single web server in a container
- An orchestrating service is needed for distributed applications with multiple containers
  - e.g. ECS or EKS (based on Kubernetes)

# AWS Elastic Container Service

## AWS Container Services

- Registry
  - Amazon Elastic Container Registry
- Orchestration
  - Amazon Elastic Container Service
  - Amazon Elastic Kubernetes Service
- Compute options
  - Amazon Elastic Compute CLoud
  - AWS Fargate
  - AWS Lambda

## Decomposing Monoliths

e.g. Monolithic forum application
- Users, Topics, Messages

1. Create container images
- Create image for each service (users, topics, messages)
- Push images to Amazon ECR

2. Create service task definition and target groups
- Service Task Definition:
  - Launch type = [EC2 or Fargate]
  - Name = [service-name]
  - Image = [service ECR repo URL]: version
  - CPU = [256]
  - Memory = [256]
  - Container port = [3000]
  - Host port = [0]
- Service Target Group:
  - Name = [service-name]
  - Procotol = [HTTP]
  - Port = [80]
  - VPC = [vpc-name]

3. Connect load balancer to services
- Listener rules
  - IF Path = /api/[service-name]*
  - THEN Forward to [service-name]

## Inter-Service Communication

- ECS Service Discovery
  - Each service can automatically register itself with a DNS name in a CloudMap Namespace
  - Services referring to each other using the DNS name when they need to talk to each other, Route 53 helps to resolve the location
- ECS Service Connect
  - Each service has a logical name registered with CloudMap
  - ECS Service Connect handles the routing
- AWS App Mesh
  - More advanced approach
  - App Mesh provides a dedicated infrastructure layer for service-to-service communication


