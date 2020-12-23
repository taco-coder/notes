# EC2
Compute service that provides resizable compute capacity in the cloud in minutes

Different instance types have different use cases

Subnets are completely randomized. `us-east-1b` can be different for one account when compared to another account

When you generate your private key, create a public key to use for `ssh`

On Windows
`ssh-keygen -y -f [key pair name].pem > [keypair name].pub`

When using security groups, setting an inbound rule (ssh allowed, http allowed, etc) automatically creates an outbound rule

Can assign multiple security groups to an ec2 instance
# Building a web server on EC2 instance
Setup a new security group to assign to the instance and allow HTTP @ port 80 to allow web traffic

1. Get public ip

2. Connect to instance

1. ###### Install Apache
`yum install httpd`
2. ###### Start Apache Service
`service httpd start`

- ###### To start httpd service on reboot
>>>chkconfig on

/var/www/html is the root directory for the website
any files in the directory are visible when visiting the ip (this is where we store the files for our website

under advanced details in configure instance step, can run a script to automate setup of instance on creation





---using an elastic load balancer---
scroll down to loadbalancers in ec2 side menu

can take 5-10 minutes to create

when using with ec2, pay attention to availability zone. if creating a second server, use a different AZ than the first

we can use the load balancer DNS to go to our webpage instead of the ec2 instance IP. If an instance goes down or reboots, we don't need to worry about the ip address

under the target groups settings we can add or remove instances to a load balancer

application load balancers-use for media content and applications; 7 layer OSI model 

network load balancers-extreme performance/static ip address





---EC2 CLI instructions---
to setup credentials run
>>> aws configure
!will need access key id and secret access key!

all we need to do is run
>>>aws [service name]

r`unning
>>> aws s3 mb s3://[url name] 
!would make an S3 bucket!


to see s3 buckets in current region
>>>aws s3 ls

to copy/upload file to s3 bucket
>>>aws s3 cp  [file] s3://[bucket-url]

to see and change credentials
>>>cd .aws
!this directory contains a credentials file!

can attach role to ec2 under actions->security->manage IAM 

using roles is more secure than using credentials to connect to ec2 from the CLI



---ENI vs EN vs EFA---
ENI: Elastic Network interface
    +virtual network card attached to EC2 instance
    +allows more public and private IP addresses
    +Use cases:
        > Create a management network
        > Use network and security appliances in your VPC
        > create dual-homed instances with workloads/roles on distinct subnets
        > create a low-budget, high availability solution

EN: Enhanced networking
    +uses single root I/O virtualization (SR-IOV) to provide high performance networking capabilities on supported instance types
    +enhanced networking provides higher bandwidth, higher packet per second performance, and consistently lower inter-instance latencies. No additional charge for using it
    +Use cases:
        >when you want faster network performance

EFA: Elastic Fabric Adapter
    +network device that you can attach to your EC2 instance to accelerate High Performance Computing (HPC) and machine learning applications
    +provides lower and more consistent latency and higher throughput than TCP traditionally used in cloud-based HPC systems
    +can use OS-bypass to enable HPC and machine learning apps to bypass the OS kernel and to communicate directly with EFA device. Results in high speed with low latency; only supported in 
     Linux
    


---EFS---
Elastic File System is a file storage service that allows EC2 instances to share file storage volumes

When creating an EFS, make note of the security group that it is setup to use

In the EC2 instance run
>>>yum install -y amazon-efs-utils
to install EFS capability

On the security group setup with EFS, you need to edit inbound rules to allow NFS and then set the source to the security group assigned to the EC2 instance

To mount an EFS, follow the mount instructions provided by AWS in the EFS settings (click on the EFS name and you should see it)

As a note, deleting an EFS takes a bit of time





---FSx---
Windows File Server so you can easily move your Windows-based applications that require file storage to AWS

Is built for Windows Server




---Placement Groups---
A cluster placement group is a grouping of instances within a single AZ
    +Recommended for apps that need low latency and high throughput
    +only certain instances can be launched into a clustered placement group

A spread placement group is a group of instances that are each placed on distinct underlying hardware
    +recommended for apps that havea  small number of critical instances that should be kept seperate
    +think individual instances for reduncy

A partitioned placement group is EC2 divided into logical segments called partitions
    +Each partition has its own set of racks and each rack has its own network and power source.
    +think multiple instances packaged up into multiple groups





---High Performance Compute on AWS---
HPC is used by industries like genomics, finance, ML/AI, etc

What are the different services we can use to achieve HPC on AWS?
    1) Data transfer
    2) Compute and networking
    3) Storage
    4) Orchestration and automation

1) Data Transfer:
    +Snowball 
        > AWS sends you a physical drive that can hold petabytes of data
        > you load your data onto the drive then send it back
    +AWS DataSync to store on S3, EFS, FSx for Windows, etc.
    +Direct Connect
        >cloud solution that makes a dedicated network connection from your data center to AWS

2) Compute and networking
    +Compute
        >EC2 instances that are GPU/CPU optimized
        >EC2 fleets
        >Placement groups
    +Networking
        >Enhanced Networking (see EN above)
        >Elastic Network Adapters (device that enables EN)
        >Elastic Fabric Adapters (see EFA above)

3) Storage
    +Instance-attached storage
        >EBS: Scale up to 64,000 IOPS with Provisioned IOPS (PIOPS)
        >Instance Store: Scale to millions of IOPS; low latency
    +Network Storage
        >S3: Distributed object-based storage
        >EFS: Elastic File System scales IOPS based on total size
        >FSx for Lustre: HPC-optimized distributed file system; millions of IOPS

4) Orchestration and Automation
    +AWS Batch 
        >enables developers to run hundreds of thousands of batch computing jobs on AWS
        >supports multi-node parallel jobs, which allows you to run a single job that spans multiple EC2 instances
        >can easily schedule jobs and launch EC2 instances as needed
    +AWS Parallel Cluster
        >opensource cluster management tool that makes it easy to deploy and manage HPC clusters on AWS
        >uses a simple textfile to model and provision all the resources needed for HPC applications
        >automates creation of VPC, subnet, cluster type, and instance types



---WAF---
Web application firewall that lets you monitor the HTTP and HTTPS requests that are forwarded to Cloudfront, an Application Load Balancer, or API Gateway
Also lets you control access to your content

Can configure conditions such as what IP addresses are allowed to make certain requests or what query string params need to be passed for the request to be allowed


    


