## Deploying Static webpage in EC2 with proper Virtual private cloud setup

1) Created VPC setup using VPC and more option in AWS console.
Components created:
VPC
2 Private and public subnet each on diffrent availability zone.
1 Public RTB and 2 Private RTB(Since we go for AZ specific NAT gateway to cut off cost for cross region request for internet outbound request from Private subnet resources, Using one NAT Gateway per Availability Zone avoids cross-AZ traffic charges and provides higher availability if one AZ becomes unavailable.)
1 Internet Gateway and attach it to the created VPC.(Add this as destination for internet traffic from public subnet)

2) Bastian Server

EC2 instance placed inside the public subnet.

Purpose:

Acts as a secure jump server.
Used to SSH into EC2 instances running in private subnets.

Security group -> Allow SSH 22, source = Ypur public IP don't allow anywhre 0.0.0.0/0
Copy private key from local to Bastian server. Then SSH to EC2 instance running in Private subnet.

**Note:**
Instead of copying the private key to the Bastion Host, a better practice is to use SSH agent forwarding or AWS Systems Manager Session Manager, which avoids storing private keys on the Bastion instance.

3) EC2 Autoscaling:

The **Launch Template** defines how new EC2 instances are created.

Typically includes:

AMI
Instance Type
Key Pair
Security Group
IAM Role
User Data
Storage Configuration

It does not require subnet selection because the Auto Scaling Group decides where to launch instances.

Auto scaling:

The Auto Scaling Group uses the Launch Template to create EC2 instances.
Networking -> VPC, Private Subnets
AZ distribution
Desired Capacity
Minimum Capacity
Maximum Capacity
Health Checks
Instance Replacement Policy

Replacement policies:

Launch before terminate
Terminate and launch
None

Optional features:

Capacity Reservations
Instance Refresh
Mixed Instance Types
Warm Pool
Lifecycle Hooks

To do:
capacity reservation
VPC lattice

4) Security Group

EC2(Private subnet)

**Inbound**
allow ssh Source = Bastion Security Group, allow Custom TCP port 3000(application port) Source = ALB Security Group. 
By using the ALB Security Group as the source, only the Application Load Balancer is allowed to communicate with the application. Even if it is 0.0.0.0/0 no issue since the Ec2 is in private subnet no public IP. But its better to follow best practice.

**Outbound**

All Traffic

This allows the instance to:

Download application packages
Pull Docker images
Reach AWS services
Access the internet through the NAT Gateway

EC2(Bastian) -> allow ssh 22, source= your public ip
ALB -> allow http 80, allow https 443. Outbound -> Custom TCP 3000
Destination = EC2 Security Group. Although the default outbound rule ("All Traffic") works, restricting it to the EC2 security group is a good security practice.

5) Application Loadbalancer

The ALB is deployed in the public subnets so it can receive internet traffic.

The Internet Gateway allows traffic to reach the ALB.

Traffic flow:

Internet
      |
Internet Gateway
      |
Application Load Balancer
      |
Target Group
      |
EC2 Instances (Private Subnet)

Targets -> Register Targets -> EC2 private IPs and port, Health Check, protocol where the application is running. The ALB continuously checks application health and only forwards requests to healthy instances.
Listener -> The Listener accepts client requests. Listen on http port 80. Forward to target group.
ALB -> public subnet -> have access directly from the internet gateway -> Target group


ALB:

Target Group
Listener -> Protcol, Port
Targets -> IP and port, protocol

Security Group:

Specify : Inbound and outbound rules

__________________________________-

Inbound
Custom TCP
Port: 3000
Source: ALB Security Group

This is the important part.

Don't use

Source = 0.0.0.0/0

because that would allow anyone who can reach the instance to access the application directly. Restricting the source to the ALB's security group means only the load balancer can send traffic to your app.


**Who gets security group?**

A Security Group is attached to network interfaces (ENIs), not to VPCs or subnets. Resources that have an IP address (through an ENI) can have a Security Group.

VPC
│
├── Subnet
│     │
│     ├── EC2  -------- Security Group ✅
│     ├── ALB  -------- Security Group ✅
│     ├── RDS  -------- Security Group ✅
│     └── Lambda ENI -- Security Group ✅
│
├── Route Table ------- No Security Group ❌
├── Internet Gateway -- No Security Group ❌
├── NAT Gateway ------- No Security Group ❌
└── Network ACL ------- Not a Security Group ❌

"Does this resource have an Elastic Network Interface (ENI) or receive/send traffic using an IP address?"

Yes → It usually supports a Security Group.
No → It usually does not.