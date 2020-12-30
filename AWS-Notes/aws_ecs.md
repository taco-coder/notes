# Elastic Container Service
### What are Containers and Docker?
A container is a package that contains an application, libraries, runtime, and tools required to run it. They run on a container engine like Docker. They provide the isolation benefits of virtualization with less ovverhead and faster
starts than VMs. Containerized applications are portable and offer a consistent environment.

### What is Elastic Container Service (ECS)?
It's a managed container orchestration service. You can create clusters to manage fleets of container deployments. ECS manages EC2 or Fargate instances. It schedules containers for optimal placement and you can define rules for CPU 
and memory requirements. Allows you to monitor resource utilizations. You can update, deploy, and roll back your containers as well. Above all, it's completely free to use. You only need to pay for the instances that you use. Integrates
with VPC, security groups, Elastic Load Balancers, and EBS volumes. 


### ECS components
- Cluster: Logical collection of ECS resources - either ECS EC2 instances or Fargate instances.
- Task: Single running copy of any containers defined by a task definition. One working copy of an application (e.g. DB and web containers).
- Task Definition: Defines your application. Similar to a Dockerfile but for running containers in ECS. Can contain multiple containers.
- Service: Allows task definitions to be scaled by adding tasks. Defines minimum and maximum values.
- Container Definition: Inside a task definition, it defines the individual containers a task uses. Controls CPU and memory allocation and port mappings.
- Registry: Storage for container images (e.g. Elastic Container Registry (ECR) or Docker Hub). Used to download images to create containers.

### Fargate
Fargate is a serverless container engine. It eliminates the need to provision and manage servers. You specify and pay for resources per applications and works with both ECS and EKS (Elastic Kubernetes Service). Each workload runs in
its own kernel and provides isolation and security. You would use EC2 instead though if you need to meet compliance requirements, you need broader customization, or you need GPUs. 

### EKS (Elastic Kubernetes Service)
Kubernetes (K8s) is an open-source software that lets you deploy and manage containerized applications at scale. Unlike ECS, you can use the same toolset on-premises and in the cloud. Containers are grouped into pods. EKS supports
EC2 and Fargate. You should use EKS if you're already using K8s and want to migrate to AWS. 

### ECR (Elastic Container Registry)
ECR is the Docker container registry in AWS. It lets you store, manage and deploy images. It's integrated with ECS and EKS and works with on-premises deployments. It's highly available and integrated with IAM. You simply pay for 
storage and data transfer.


