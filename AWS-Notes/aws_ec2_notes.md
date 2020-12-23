# EC2
Compute service that provides resizable compute capacity in the cloud in minutes

Different instance types have different use cases

Subnets are completely randomized. `us-east-1b` can be different for one account when compared to another account

When you generate your private key, create a public key to use for `ssh`

On Windows:

`ssh-keygen -y -f [key pair name].pem > [keypair name].pub`

When using security groups, setting an inbound rule (ssh allowed, http allowed, etc) automatically creates an outbound rule

Can assign multiple security groups to an ec2 instance
# Building a web server on EC2 instance
Setup a new security group to assign to the instance and allow HTTP @ port 80 to allow web traffic

1. Get public IP

2. Connect to instance and run the commands below

##### Install Apache
`yum install httpd`

##### Start Apache Service
`service httpd start`

##### To start httpd service on reboot
`chkconfig on`

`/var/www/html` is the root directory for the website.

Any files in the directory are visible when visiting the ip (this is where we store the files for our website).

Under `Advanced Details` in `Configure Instance` step, you can run a bootstrap script to automate setup of instance on creation.

# Using an Elastic Load Balancer
Scroll down to `Load Balancers` in the EC2 side menu. It can take 5-10 minutes to create. When using with ec2, pay attention to availability zone. If creating a second server, use a different AZ than the first server.
We can use the load balancer DNS to go to our webpage instead of the EC2 instance IP. If an instance goes down or reboots, we don't need to worry about the IP address. Under the target groups settings, we can add or remove instances to a load balancer.

#### Types of Load Balancers:
- Application load balancers are used for media content and applications

- Network load balancers are used when we need extreme performance/static IP address

# EC2 CLI instructions
To setup credentials run:

`aws configure`

**You will need access key id and secret access key**

All we need to do is run:

`aws [service name]`

The following command would make an S3 bucket from an EC2 instance:

`aws s3 mb s3://[url name]`

To see S3 buckets in your current region:

`aws s3 ls`

To copy/upload a file to an S3 bucket:
`aws s3 cp  [file] s3://[bucket-url]`

To see and change credentials:

`cd /.aws` 
> This directory contains a credentials file.

You can attach a role to EC2 under `Actions->Security->Manage IAM`

Using a role with programmatic access is more secure than using credentials to connect to EC2 from the CLI.



# ENI vs EN vs EFA
#### ENI: Elastic Network interface
- Virtual network card attached to EC2 instance.
- Allows more public and private IP addresses.
- Use cases:
    - Create a management network.
    - Use network and security appliances in your VPC.
    - Create dual-homed instances with workloads/roles on distinct subnets.
    - Create a low-budget, high availability solution.

#### EN: Enhanced networking
- Uses single root I/O virtualization (SR-IOV) to provide high performance networking capabilities on supported instance types.
- Enhanced networking provides higher bandwidth, higher packet per second performance, and consistently lower inter-instance latencies. No additional charge for using it.
- Use cases:
    - When you want faster network performance.

#### EFA: Elastic Fabric Adapter
- Network device that you can attach to your EC2 instance to accelerate High Performance Computing (HPC) and machine learning applications.
- Provides lower and more consistent latency and higher throughput than TCP traditionally used in cloud-based HPC systems.
- Can use OS-bypass to enable HPC and machine learning apps to bypass the OS kernel and to communicate directly with EFA device. Results in high speed with low latency; only supported in Linux.
    


# EFS
Elastic File System is a file storage service that allows EC2 instances to share file storage volumes

When creating an EFS, make note of the security group that it is setup to use

To install EFS capability in the EC2 instance run:

`yum install -y amazon-efs-utils`

On the security group setup with EFS, you need to edit inbound rules to allow NFS and then set the source to the security group assigned to the EC2 instance.
To mount an EFS, follow the mount instructions provided by AWS in the EFS settings (click on the EFS name and you should see it).

**Note:** Deleting an EFS takes a bit of time.

# FSx
FSx is a Windows File Server so you can easily move your Windows-based applications that require file storage to AWS.

# Placement Groups
- A cluster placement group is a grouping of instances within a single AZ.
    - Recommended for apps that need low latency and high throughput.
    - Only certain instances can be launched into a clustered placement group.

- A spread placement group is a group of instances that are each placed on distinct underlying hardware.
    - Recommended for apps that have a small number of critical instances that should be kept seperate.
    - Think individual instances for reduncy.

- A partitioned placement group is EC2 divided into logical segments called partitions.
    - Each partition has its own set of racks and each rack has its own network and power source.
    - Think multiple instances packaged up into multiple groups.

# High Performance Compute on AWS
HPC is used by industries like genomics, finance, ML/AI, etc

What are the different services we can use to achieve HPC on AWS?
1. Data transfer
2. Compute and networking
3. Storage
4. Orchestration and automation

### 1. Data Transfer:
- Snowball 
    - AWS sends you a physical drive that can hold petabytes of data.
    - You load your data onto the drive then send it back.
- AWS DataSync to store on S3, EFS, FSx for Windows, etc.
- Direct Connect
    - Cloud solution that makes a dedicated network connection from your data center to AWS.

### 2. Compute and Networking:
- Compute
    - EC2 instances that are GPU/CPU optimized.
    - EC2 fleets.
    - Placement groups.
- Networking
    - Enhanced Networking (see EN above).
    - Elastic Network Adapters (device that enables EN).
    - Elastic Fabric Adapters (see EFA above).

### 3. Storage
- Instance-attached storage
    - EBS: Scale up to 64,000 IOPS with Provisioned IOPS (PIOPS).
    - Instance Store: Scale to millions of IOPS; low latency.
- Network Storage
    - S3: Distributed object-based storage.
    - EFS: Elastic File System scales IOPS based on total size.
    - FSx for Lustre: HPC-optimized distributed file system; millions of IOPS.

### 4. Orchestration and Automation
- AWS Batch:
    - Enables developers to run hundreds of thousands of batch computing jobs on AWS.
    - Supports multi-node parallel jobs, which allows you to run a single job that spans multiple EC2 instances.
    - Can easily schedule jobs and launch EC2 instances as needed.
- AWS Parallel Cluster:
    - Opensource cluster management tool that makes it easy to deploy and manage HPC clusters on AWS.
    - Uses a simple textfile to model and provision all the resources needed for HPC applications.
    - Automates creation of VPC, subnet, cluster type, and instance types.



# WAF
Web Application Firewall that lets you monitor the `HTTP` and `HTTPS` requests that are forwarded to Cloudfront, an Application Load Balancer, or API Gateway. Also lets you control access to your content. You can configure conditions such as what IP addresses are allowed to make certain requests or what query string params need to be passed for the request to be allowed.

