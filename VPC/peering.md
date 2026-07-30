### VPC Peering

VPC peering enables private communication between two VPCs, whether they are in the same AWS Region, different Regions (inter-region), or different AWS accounts, without using the public internet. The CIDR blocks of the two VPCs must not overlap.

### Why?

- Enables private communication between VPCs using private IP addresses.
- Traffic stays on the AWS network and does not traverse the public internet.
- No Internet Gateway, NAT Gateway, or public IP is required for communication between the peered VPCs.

### VPC peering connection
Create a VPC peering connection by specifying:

Requester VPC ID
Accepter (Peer) VPC ID
Peer Region (for inter-region peering)
By default, auto_accept = false.
If the peer VPC belongs to another AWS account, specify the peer owner account ID (peer_owner_id).

### VPC Peering Accepter

The accepter VPC must accept the peering request.
For Terraform, create an aws_vpc_peering_connection_accepter resource in the peer region (or peer account) with:
auto_accept = true
This automatically accepts the pending peering request..

### Route

VPC peering does not automatically update route tables.
Add routes on both sides:

Requester VPC route table
Destination: Peer VPC CIDR
Target: VPC Peering Connection

Accepter VPC route table
Destination: Requester VPC CIDR
Target: VPC Peering Connection

### Security Group:

To communicate between instances:

Allow SSH (TCP 22) if you want to log in.
Allow ICMP if you want to use ping.
The source should be the peer VPC CIDR (recommended), or a more specific IP range if required.

### Why is it called bidirectional?

The same peering connection is used for traffic in both directions.

EC2-A  ---------> EC2-B
        pcx-12345

EC2-A  <--------- EC2-B
        pcx-12345

You do not create two peering connections (A → B and B → A). A single peering connection supports traffic both ways. The only requirement is that both VPCs have routes to each other and their security groups permit the traffic.

### Note:

- VPC peering is non-transitive. If VPC A is peered with VPC B, and VPC B is peered with VPC C, VPC A cannot communicate with VPC C through VPC B.
- Private DNS resolution across peered VPCs is not enabled by default and can be enabled if needed.
Security groups from one VPC cannot generally be referenced in another VPC (except in supported same-region scenarios), so using the peer VPC CIDR as the source is the common approach.
- VPC peering is a one-to-one relationship. For many interconnected VPCs, AWS Transit Gateway is usually a better choice because it avoids managing a large number of peering connections

