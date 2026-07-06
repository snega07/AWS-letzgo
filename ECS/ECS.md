## ECS- Elastic container Service

Amazon ECS (Elastic Container Service) is AWS's fully managed container orchestration service for deploying and managing Docker containers.

An ECS cluster can use one of three capacity providers:
AWS Fargate (Serverless)
Amazon EC2 (Self-managed instances)
Amazon ECS Managed Instances (AWS-managed EC2)

**Fargate** -> AWS provisions and manages the compute infrastructure automatically.

- No EC2 instances to manage.
- No OS patching or infrastructure maintenance.
- You specify only:
CPU
Memory
Number of tasks
AWS launches the required infrastructure behind the scenes.
- Ideal for microservices and applications where infrastructure management should be minimal.

**Limitations**

- No access to the underlying EC2 instance.
- Cannot choose the AMI or operating system.
- Less flexibility for custom networking or host-level software.

**EC2**

You provision and manage the EC2 instances yourself.

**Responsibilities include:**

- Selecting the AMI
- OS installation
- Security patching
- Capacity planning
- Scaling EC2 instances
- Monitoring infrastructure

**Advantages**

- Full control over the operating system
- Install custom agents
- GPU instances
- Specialized storage
- Custom networking

Best suited for workloads requiring complete infrastructure control.

**Managed EC2**

This is a newer option where AWS manages much of the EC2 lifecycle while still allowing you to choose instance characteristics.

**AWS manages:**

- Infrastructure lifecycle
- OS updates
- Security patching
- Instance replacement

You still have more flexibility than Fargate.It combines much of the simplicity of Fargate with more control over the compute environment.

**Operating System (OS)**

The operating system is Linux (Amazon Linux, Ubuntu, RHEL, etc.).

It provides

- Kernel
- File system
- User management
- Networking
- Process management

Examples

Amazon Linux 2023
Ubuntu 24.04
Red Hat Enterprise Linux

**AMI (Amazon Machine Image)**

An AMI is not the operating system itself.

An AMI is a complete machine image used to launch an EC2 instance. Think of it like a virtual machine template.

It contains

Operating system
Installed software
Packages
Configuration
Default users
Boot configuration
Filesystem snapshot

 - We can run our container as a task here which is equivalent to k8s pods where all the required envs and health check configuration container image and ports will passed.
 - Auto healing and auto-scaling will be handled by ECS service which act similar to k8s deployment. Help to maintain desired number of replicas.
 - We can have internal ALB which act as kubernetes service of type clusterIp. External ALB will help to access the task publicly which act as ingress.

**ECS Task**

An ECS Task is similar to a Kubernetes Pod.

**A Task defines:**

Container image
CPU
Memory
Environment variables
Secrets
Ports
Logging
Health checks
Volumes
IAM Task Role

A Task can contain one or multiple containers.

**ECS Service**

An ECS Service manages Tasks similarly to a Kubernetes Deployment.

Responsibilities

Maintain desired number of tasks
Restart failed tasks
Rolling deployments
Auto Scaling
Load balancer integration
Health monitoring

**Networking**

Every ECS Task receives its own network interface (using awsvpc mode).

**Tasks communicate using**

- Private IP
- Service Discovery (AWS Cloud Map)
- Internal Load Balancer
- External Load Balancer

**Load Balancer**

**Internal ALB**

Used for communication inside the VPC.

Examples

Microservice A → Microservice B
Private APIs

This is not exactly equivalent to a Kubernetes ClusterIP Service.

A closer comparison is:

Internal ALB ≈ Internal Ingress/Load Balancer
ECS Service Discovery (Cloud Map) ≈ Kubernetes Service (ClusterIP)

**Internet-facing ALB**

Used for public traffic.

Internet
      │
Internet ALB
      │
ECS Service
      │
    Tasks

This is roughly equivalent to:

Ingress Controller + Ingress in Kubernetes.

**pros:**

- Simple architecture and use less resource when compared to k8s platform. As the k8s architecture has its own components resources which makes its extensive.
- No need to maintain under;lying resources. Also has felxibilty to create cluster based on our requirement as Fargate, EC2, Managed EC2.
- Suitable for startup and small application.
- Comes with cloudwatch log visualization.
- Simpler architecture than Kubernetes.
- Lower operational overhead.
- No control plane management.
- Multiple launch options:
Fargate
EC2
ECS Managed Instances
- Native integration with AWS services.
- Built-in integration with:
CloudWatch
IAM
Application Load Balancer
Auto Scaling
CloudTrail
Secrets Manager
Systems Manager
- Lower learning curve than Kubernetes.
- Well suited for AWS-centric organizations.


**Cons:**

- Vendor lockin. When we use EKS, AKS we can easily move to other kubernetes platform. But it is hard to do if we are using ECS. Migration is difficult.
- Less community support.
- Many devops tools are k8s based like argocd, fluxcd, grafana, prometheus and other.
- Vendor lock-in to AWS.
- Migration to Kubernetes requires redesigning ECS-specific components.
- Smaller ecosystem compared to Kubernetes.
- Many cloud-native tools are Kubernetes-first, such as:
Argo CD
Flux CD
Prometheus
Grafana
- Fewer community extensions and operators.
- Less flexibility than Kubernetes for advanced scheduling, networking, and custom controllers.

