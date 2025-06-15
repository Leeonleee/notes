# Virtualisation Overview

## Cloud and Enabling Technologies

- Cloud computing allows users to rent various resources from providers on hourly (secondly), daily, monthly, or yearly basis
- A few general models (e.g. XaaS) are used to describe the spectrum of cloud computing
- IaaS and many PaaS services use virtualisation technology to present virtual machines to users
  - Virtualisation technology is relatively standard and mature, particularlly with respect to allocating CPU and memory

## What is Virtualisation?

- Narrow and common perception of virtualisation: "Virtualisation lets you run multiple virtual machines on a single physical machine, with each VM sharing the resources of that one physical computer across multiple environments"
- Broadly, the term is used in many other contexts, some that are closely related
  - e.g. virtual memory, storage, network, virtual reality

## Early Virtualisation Example: Virtual Memory

- Provides more accessible memory to CPU than the amount physically present
- Important component of modern operating systems
- Actions involved:
  - Address translation
  - Data transfer
  - Data placement strategy for swap in/out

## Early Virtualisation Example: Mainframe Virtualisation

- IBM released mainframe virtualisation solution in 1972
- The overall architecture looks similar to modern system VMs
- Solves the problem of OS migration triggered by new hardware

## Early Virtualisation Example: Hot Standby Router Protocol

- Solves the problem of a single point of failure
  - One host can only define a default gateway
- Uses virtual IP and a preconfigured priority to decide which physical router would act as the default gateway
- Other routers with the same virtual IP can take over if the default one fails

## Common Features of Virtualisation

- Emulation: a pre-existing IT resource was emulated (e.g. memory, mainframe, IP addresses)
- Transparency: the consumers of the resources (CPU, mainframe users, network hosts) cannot distinguish between real and emulated resources
- Benefits: compared with directly using actual resources, virtualisation has many benefits (e.g. memory expansion, resource optimisation, high availability)


## Broader Definition of Virtualisation

- The transparent emulation of an IT resource producing to its customers benefits that were unavailable in its physical form

# Server Virtualisation

## AWS Definition of a Virtual Machine

- A software-defined computer that runs on a physical computer with a separate operating system and computing resources
- The physical computer is called the host machine, and VMs are guest machines
- Multiple VMs can run on a single physical machine
- VMs are abstracted from the computer hardware by a hypervisor

## AWS Definition of a Hypervisor

- A software component that manages multiple VMs in a computer
- Ensures that each VM gets the allocated resources and does not interfere with the operation of other virtual machines

# Hypervisors

## Hypervisor and OS

- Hypervisors perform a lot of the traditional operating system tasks:
  - Resource sharing
  - Device management
  - Virtual storage management

## 2 Types of Hypervisors

1. Type 1 - Bare-metal
  - Runs directly on the hardware
  - Better performance and resource control
2. Type 2 - Hosted
  - Runs within a host OS
  - Easier to setup

## The Role of a Hypervisor

- Provides an environment identical to the physical environment
- Provide that environment with minimal performance cost
- Retain complete control of the system resources

## Managing Resources with a Hypervisor

- Each VM has a fraction of the resources of the physical host
  - e.g a host may have 256GB of memory, but a guest VM may believe that it has 64GB
- The hypervisor abstracts the hardware resources and controls access to them to ensure each VM gets its allocated resources
  - All access to hardware resources (e.g. memory, storage, network) goes through the hypervisor

## Xen Hypervisor Components

- Xen Hypervisor: thin software layer that runs directly on the hardware and is responsible for managing CPU, memory, and interrupts
- Control Domain/Domain 0:
 - A specialised VM for handling I/O and for interacting with the other VMs
 - Exposes a control interface to the outside world, through which the system is controlled
- Guest Domains/VMs: the VM allocated to users
- Toolstack and Console: admin interface for creating, destroying, and configuring guest domains

## Hyper-V Hypervisor Components

- Hypervisor:
  - A layer of software that sits on top of the hardware
  - Its primary job is to provide isolated execution environments called partitions
  - The hypervisor controls and arbitrates access to the underlying hardware
- Partition: the VM running on the hypervisor
- Root Partition: Similar to Xen's domain 0, manages I/O and communicates with other partitions
- Child Partition: All other guest VMs

## KVM Hypervisor

- Turns the Linux kernel into a hypervisor using a few modules (KVM, QEMU, libvert)
- Development started soon after chip manufacturers introduced hardware support for virtualisation
- Both GCP and AWS use KVM-based virtualisation

## KVM Architecture

- Turns the Linux Kernel into a hypervisor
- Several hardware-aware versions
- When a new OS is booted on KVM, it becomes a process of the host OS and therefore is schedulable like any other process
  - But it is executed in a different mode compared to the regular OS

## QEMU and Libvert

- QEMU (Quick Emulator): a generic emulator that virtualises I/O devices as well
- Libvert: provides a common management layer to manage VMs running on hypervisors

# VM Resource Management

## Managing CPUs for a VM

- Physical CPU cores are time shared by virtual CPUs (vCPU) in server virtualisation
- The dynamic scheduling of vCPUs on the physical CPU is managed by the hypervisor following a customised scheduler algorithm
  - e.g. credit scheduler of Xen

## Multiprocessors, cores, and hyperthreading in VMs

- A host server may be configured with multiple processor chips, each with multiple cores
- The core may support hyperthreading to have 2+ logic cores for one physical core

## Vm with Multiple vCPUs

- vCPU count of VM: maximum number of threads the VM can run at any given moment
- vCPU and physical CPU (pCPU):
  - A VM can run on any and all of the host CPUs over a period of time
    - Some hypervisors may support pinning
  - For a VM with multiple vCPUs, the hypervisor will need to schedule each vCPU on a different pCPU (logic core also counts)
    - The max number of vCPUs you can assign to a single VM is bound by the hardware capacity
    - A VM with more vCPUs may have a longer waiting time to get scheduled
  - Most applications are not designed to run on multiple threads
    - Configuring a VM with too many vCPUs may not have any benefit
    - A VM with 2 vCPUs is very typical

## Virtualising Memory Management

Memory Management

- The OS is designed to manage the mapping of virtual memory and physical memory through a Translation Lookaside Buffer (TLB) and Page Tables
- Guest OSes get a dedicated portion of the physical memory to ensure strong isolation
- Guest OS doesn't know the other parts of memory and shouldn't interfere with another VM's memory
- It shouldn't even know which part of the physical memory it is using
- Only the hypervisor has access to the physical memory and can manage the mapping

## Virtualising I/O

- I/O refers to all input/output devices
  - Disk, network, printer, monitor, mouse
- There are many devices with different ways of controlling them
- The OS has efficient ways to deal with I/O by providing a standard interface and by loading various drivers

Disk and Network:
- A disk is a partitioned device
  - Each VM can get a partition of it exposed as a virtual disk
  - External disk drives can be mounted easily
- Network adapter is a shared device
  - The physical adapter can be time shared by each VM
- The requests are passed to and managed by the Virtual Machine Monitor/Hypervisor

# EC2 Overview

## What is EC2?

- Provides resizable compute capacity in the cloud
  - Provides VMs (servers)
  - Provisions servers in minutes
  - Can autoamtically scale capacity up or down as needed
  - Enables you to pay only for the capacity that you use

## EC2 Instances

- A virtual machine that runs on a physical host
  - YOu can choose different configurations of CPU and memory capacity
  - Supports different storage options
    - Instance store
    - Amazon Elastic Block Store (Amazon EBS)
  - Provides network connectivity

## EC2 Use Cases

Use EC2 when you need:

- Complete control of your computing resources, including operationg system and processor type
- Options for optimising your compute costs:
  - On-demand instances, reserved instances, and spot instances
  - Savings plans
- Ability to run any type of workload
  - Simple websites
  - Enterprise applications
  - Generative AI applications

## Steps for Provisioning an EC2 Instance

1. AMI
  - Amazon Machine Image: a template used to create an EC2 instance
    - Contains a Windows or Linux operating systems
    - Often also has some software pre-installed
  - Choices:
    - Quick Start: Linux and Windows AMIs provided by AWS
    - My AMIs: Any AMIs that you create
    - AWS Marketplace: pre-configured templates from third parties
    - Community AMIs: AMIs shared by others
  - Benefits of AMIs
    - Repeatability: an AMI can be used to repeatedly launch instances with efficiency and precision
    - Reusability: instances launched from the same AMI are identically configured
    - Recoverability: you can create an AMI from a configured instance as a restorable backup, you can replace a failed instance by launching a new instance from the same AMI
2. Instance Type
  - Consider your use case
  - The instance type that you choose determines:
    - Memory (RAM)
    - Processing power (CPU)
    - Disk space and disk type (Storage)
    - Network performance
  - Instance type categories:
    - General purpose
    - Compute optimised
    - Memory optimised
    - Accelerated computing
  - Instance types offer family, generation, and size

|   | General Purpose | Compute Optimised | Memory Optimised | Accelerated Computing | Storage Optimised |
|---|-----------------|-------------------|------------------|-----------------------|-------------------|
| Instance Types | a1, m4, m5, t2, t3 | c4, c5 | r4, r5, x1, z1 | f1, g3, g4, p2, p3 | d2, h1, i3 |
| Use Case | Broad | High performance | In-memory database | Machine learning | Distributed file systems |

  - Networking features
    - The network bandwidth (Gbps)
  - To maximise the networking and bandwidth performance of your instance type:
    - If you have interdependent instances, launch them into a cluster placement group
    - Enable enhanced networking
  - Enhanced networking types are supported on most instance types
    - Elastic Network Adapter (ENA): supports network speed of up to 100 Gbps
    - Intel 82599 Virtual Function Interface: up to 10 Gbps
  - AWS Compute Optimiser
    - Recommends optimal instance type, instance size and Auto Scaling group configuration
    - Analyses workload patterns and makes recommendations
    - Classifies instance findings as under-provisioned, over-provisioned, optimised, or none
3. Key Pair
  - At instance launch, you specify an existing key pair or create a new key pair
  - A key pair consists of:
    - A public key that AWS stores
    - A private key file that you store
  - It enables secure connections to the instance
  - For Windows AMIs:
    - Use the private key to obtain the administrator password that you need to log into your instance
  - For Linux AMIs:
    - Use the private key to use SSH to securely connect to you instance
4. Network Settings
  - Where should the instance be deployed?
    - Identify the VPC and optionally the subnet
  - Should a public IP address be automatically assigned?
    - To make it internet-accessible
5. Security Group
  - A set of firewall rules that control traffic to the instance
    - Exists outside of the instance's guest OS
  - Create rules that specify the source and which ports tha network communications can use
    - Specify the port number and protocol
    - Specify the source (e.g. IP or another SG) that is allowed to use the rule
6. Storage Options
  - Configure the root volume: where the guest operating system is installed
  - Attach additional storage volumes (optional): AMI might already include more than one volume
  - For each volume, specify:
    - Size of the disk (in GB)
    - Volume type: different types of SSDs/HDDs
    - If the volume will be deleted when the instance is terminated
    - If encryption should be used

| AWS EC2 Storage Resource | Root Volume | Data Volumes for a Single Instance | Data volumes for data that is accessible from multiple Linux instances | Data volume for data that is accessible from multiple Windows instances |
|---|---|---|---|---|
| Amazon EBS (SSD-backed only) | Yes | Yes | No | No |
| Instance store | Yes | Yes | No | No |
| Amazon Elastic File System (Amazon EFS)[Linux] | No | No | Yes | No |
| Amazon FSx for Windows File Server | No | No | No | Yes |

  - Amazon Elastic Block Store:
    - Durable, block-level storage volume
    - You can stop the instance and start it again, and the data will still be there
  - Amazon EC2 Instance Store:
    - Ephemeral storage is provided on disks that are attached to the host computer where the EC2 instance is running
    - If the instance stops, data stored here is delete
  - Other options for storage (not for the root volume)
    - Mount an Amazon Elastic File System (Amazon EFS) file system
    - Connect to Amazon Simple Storage Service (Amazon S3)
  - Instance Store
    - An instance store provides non-persistent storage to an instance. The data volume is stored on the same physical server where the instances run
    - Characteristics:
      - Temporary block-level storage
      - Use HDD or SSD
      - Instance store is lost when the instance is stopped or terminated
    - Example use cases:
      - Buffers
      - Cache
      - Scratch data
  - Amazon EBS
    - EBS volumes provide network-attached persistent storage to an EC2 instance
    - Characteristics:
      - Is persistent block-level storage
      - Can attach to any instance in the same AZ
      - Use HDD or SSD
      - Can be encrypted
      - Supports snapshots that are persisted to S3
      - Data persists independently from the life of the instance
  - Shared file systems for EC2 instances
    - EBS attaches only to one instance
    - S3 works, but isn't ideal
    - EFS and FSx both are ideal

7. IAM Role (optional)

  - Attach an IAM role if the software on the EC2 instance needs to interact with other AWS services
  - An AWS IAM role that is attached to an EC2 instance is kept in an instance profile
  - You can also attach a role to an instance that already exists

8. User Data Script (optional)

  - Specify a user data script that runs at instance launch
  - Can be used to customise the runtime environment of your instance

9. Tags

  - A label that you can assign to an AWS resource
  - Attaches metadata to an EC2 instance
  - Benefits: filtering, automation, cost allocation, and access control

# AWS Nitro Systems

## Nitro System

- The current virtualisation system used to provision EC2 and all related services
- AWS used to use Xen hypervisor
- Nitro is an example of a micro service architecture
  - Very thin HVM based hypervisor mainly for partitioning and scheduling CPU and memory
  - Hardware support for all other resources, including monitoring and management are built into Nitro cards
    - Doesn't need Domain 0

## Nitro System Architecture

- Hypervisor
  - CPU and memory
- Nitro card(s)
  - Networking, storage, management, monitoring, security
- Security chips

## Nitro card for networking

- Nitro card for VPC
- The network interface of EC2
- Looks like a network adapter
- Supports AWS's Elastic Network Adapter (ENA) API
  - Driver independent of actual hardware capacity

## Nitro Card for Storage

- Nitro card for EBS
  - NVMe controller
  - Encryption support
  - NVM to remote storage protocol
- Nitro card for instance storage
  - Instance storage has been on and off in AWS
  - Networking and storage evolved along a different path
    - Offer local storage when I/O throughput is faster than network throughput
    - When network throughport is greater than storage throughput, offering network drives makes more sense
    - Local storage comes back with SSD and NVMe

## AWS Bare Metal Instance

- Bare metal instances don't use Nitro hypervisor
- They still use Nitro cards to access VPC, EBS, and other AWS services
- Not much performance difference between hypervisor supported and bare metal instances
- Use instances backed by hypervisor in most cases
