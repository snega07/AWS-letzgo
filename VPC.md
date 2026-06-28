**Virtual Private Cloud(VPC)**

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


**Subnet:**

A subnet is a smaller network created within a VPC by dividing the VPC CIDR range into smaller IP ranges 

- VPC is broad
- subnet helps organize resources
- subnet provides logical isolation
- subnet helps routing and management


### Private Subnet:

Private subnet is secured within the VPC. It is not exposed to internet directly. But, internet access can be done through NAT gateway present in public subnet. Where internal Ips are masked.



### Public Subnet:

Resource in public subnet could access internet directly. When route table of the subnet forwards traffic to internet through internet gateway it is public subnet. 

### Internet Gateway

An Internet Gateway is an AWS-managed gateway attached to a VPC that enables communication between the VPC and the internet. It acts as the entry and exit point for internet traffic and enables public IP communication for resources inside the VPC

### NAT.

A NAT Gateway (Network Address Translation Gateway) allows resources in a private subnet to access the internet outbound by translating their private IP addresses to the NAT Gateway’s public Elastic IP. It enables outbound internet communication while preventing direct inbound internet access to the private resources. 

Allows outbound internet-initiated connections from private resources and permits only the corresponding return traffic 
Even though same public IP is used, NAT Gateway tracks connections using:

Source IP
Source Port
Destination IP
Destination Port
Protocol
AWS performs Port Address Translation (PAT).

**Example:**
10.0.2.10:5001 -> 44.x.x.x:30001
10.0.2.11:5001 -> 44.x.x.x:30002
So many private resources can share one public IP simultaneously
Nat Gateway is stateful:

Response packets are allowed back
completely new inbound internet requests are blocked

**Route table:**

A route table contains routing rules that determine where network traffic from a subnet should be forwarded based on the destination IP address 

✔ Route table handles traffic routing
✔ Associated with subnet
✔ Decides traffic flow/path
✔ Sends traffic to target destination 

Destination  Target
10.0.0.0/16  local
0.0.0.0/0    Internet Gateway

Meaning:
internal VPC traffic stays local
internet traffic goes to IGW
Common Route Targets
Target
Purpose
- local - Internal VPC communication
- Internet Gateway - Public internet
- NAT Gateway - Private subnet outbound internet
- VPN Gateway - On-premises connection
- Peering Connection - Another VPC
