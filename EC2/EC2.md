**EC2 - Elastic Cloud Compute**

Scalable cloud virtual server. AWS provides the EC2 service, which helps create a cloud VM to deploy our application or for other purposes.

These servers are scalable. We can add resources as per our requirement.

**Why EC2?**

If we create VM servers on our own physical servers, we need to take care of hardware maintenance.

Here, we don’t want to worry about any infrastructure maintenance.

**AMI(Amazon Machine Image)**

It decides OD and distribution. We have varieties like Linux ubuntu, Amazon Linux, Redhat, Debian, CentOs.

**Instance type**

AWS offers different types of EC2

- General purpose → balanced (e.g., t3)
- Compute optimized → CPU heavy apps
- Memory optimized → large in-memory DBs
- Storage optimized → high disk throughput
- Accelerated computing → GPU (ML, AI)

Advanced feature:
Termination protect, encryption, stop protect and other



**Traditional VM**

CPU, RAM, and virtual disk are typically managed together as a single VM object. The virtual disk still exists separately on the hypervisor storage, but from an administrator's perspective it feels like one unit.

**AWS EC2**

AWS intentionally separates compute (CPU/RAM) from persistent storage (EBS). The EC2 instance provides compute resources, while the root filesystem is stored on an EBS volume attached to the instance.

**Benefits of this design:**

Stop/start EC2 without losing data.
Easily resize storage independently of compute.
Take snapshots and backups of disks.
Move or recover disks by attaching them to another EC2 instance.
Reduce dependence on a particular physical host.
Scale compute and storage separately.

EC2 (CPU + RAM)
|
+-- EBS Root Volume (OS)
+-- EBS Volume (MongoDB)
+-- EBS Volume (Logs) 


**Region and Availability Zone (Data center)**

- AWS has its data centers in different parts of the world. Also has multiple availability zones in the same region for disaster recovery. 
- User can create resources as per their base location to have better latency.
- Region → geographical area (e.g., Mumbai)
- Availability Zone (AZ) → isolated data centers inside a region
- Deploy across AZs → high availability, AZs are isolated but connected.Virtual firewall attached to EC2

**Public IP**

This will keep on changing; it is used to access the application running in our VM from the outside world. For fixed IP use elastic IP.

**Public IP Assignment**

By default, an EC2 instance inherits the subnet's Auto-assign Public IPv4 Address setting.

If the subnet has Auto-assign Public IP = Enabled, new instances launched into that subnet receive a public IP by default.
You can override the subnet setting while launching the instance by enabling or disabling Associate Public IP Address for that specific instance.

**Can an EC2 instance have a public IP in a private subnet?**

Technically, you can associate a public or Elastic IP with an EC2 instance. However, if the subnet's route table does not have a route to an Internet Gateway, the instance still cannot communicate with the internet. Therefore, a public IP alone does not make an instance publicly accessible; the subnet must also be a public subnet with an IGW route.

**Why is it still considered a best practice to avoid public IPs?**

If doesn't have a public IP and is in a private subnet, then even if someone mistakenly opens the security group, the instance still isn't directly reachable from the internet because there's no public address and no internet-facing path to it.

**How to access resource from private subnet**

If users on the internet need to access an application running in a private subnet, a public-facing load balancer is the standard solution.

Typical architecture
Internet
    │
Internet Gateway
    │
Public ALB
    │
Private EC2 / ECS / EKS Pods

**Security Group**

We can configure inbound and outbound traffic.

Inbound rules:
Control incoming traffic
Example: Allow port 22 → SSH access

Outbound rules:
Control outgoing traffic
Example: Allow internet access

Key Pair 
Used for SSH login
Public key → stored in AWS
Private key → with you
