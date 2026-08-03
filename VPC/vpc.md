### Virtual Private Cloud (VPC)

A VPC (Virtual Private Cloud) is an isolated virtual network in the cloud with a configurable private IP range where a company can deploy its resources securely .
This keeps resources logically isolated from other networks and from direct public internet access unless explicitly exposed.

✔ VPC uses private IP ranges
✔ Resources inside VPC are isolated
✔ Different VPCs can use same private IP ranges
✔ Private IPs do not clash on the public internet 

These are the standard IPv4 private IP ranges reserved by RFC 1918:

Private IP Range

10.0.0.0 – 10.255.255.255
10.0.0.0/8
Very large

172.16.0.0 – 172.31.255.255
172.16.0.0/12
Medium

192.168.0.0 – 192.168.255.255
192.168.0.0/16
Smaller


**AWS VPC features:**

- Create a private network inside a public network.
- Assign private IP addresses to resources.
- Create subnets to organize resources.
- Create Route table to control traffic flow.

1. **CIDR Block:**

This specifies our VPC size. The number of resources our VPC can accomodate.

**IPV4**

Mannaul input: We can give the CIDR range mannualy

IPAM: AWS automatically assigns the CIDR range

- AWS IPAM (IP Address Manager) is a service that centrally manages IP address allocation across AWS accounts and Regions.
- We create an IPAM, define a scope, and then create one or more IPAM pools with a large CIDR range (for example, 10.0.0.0/8). When creating a VPC or another supported network resource, instead of manually specifying a CIDR block, we can select an IPAM pool. 
- IPAM automatically allocates a non-overlapping CIDR block of the required size from the pool. This simplifies IP address management and prevents CIDR overlap in large organizations. 

**IPV6** 

- AWS provides an option to assign an IPv6 CIDR block to a VPC.
- We can choose from either IPAM pool, IPV6 pool owned by us, AWS IPV6 CIDR block.

2. **Tenancy:**

Default -> Resources like EC2 are created in shared physical servers.
Dedicated -> Resources like EC2 are created in dedicated physical servers. Costlier then default since we are getting our own dedicated underlying infrastructure.

3. **VPC Encrytion:**

1. None

No encryption policy is applied.
You can create both encrypted and unencrypted resources.
AWS does not monitor or enforce encryption.

Example:

Create EC2 with encrypted EBS      ✔ Allowed
Create EC2 with unencrypted EBS    ✔ Allowed

Use case: No organization-wide encryption requirement.

2. Monitor

AWS checks whether supported resources are created with encryption.

Encrypted resources are allowed.
Unencrypted resources are also allowed.
AWS reports or flags unencrypted resources so you know they are non-compliant.

Example:

Create EC2 with encrypted EBS      ✔ Allowed
Create EC2 with unencrypted EBS    ✔ Allowed

AWS reports the unencrypted EBS as non-compliant.

Use case: You're planning to enforce encryption later and first want to identify resources that would be affected.

3. Enforce

AWS requires supported resources to be encryption-capable and created with encryption enabled.

Encrypted resources are allowed.
Unencrypted resources are blocked.
You can define exclusions if necessary.

Example:

Create EC2 with encrypted EBS      ✔ Allowed
Create EC2 with unencrypted EBS    ✖ Blocked

Create encrypted RDS               ✔ Allowed
Create unencrypted RDS             ✖ Blocked

Use case: Organizations with strict security or compliance requirements.