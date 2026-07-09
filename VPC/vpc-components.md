VPC
subnet private public
IGW -> attach/deattach to VPC
RTB -> public->local and internet->IGW
       private->local and internet->instance, NAT-GW
RTB -> Subnet association
NAT Gateway-> Create NAT in the VPC and choose regional and give it as target for internet traffic from priavte route table this will automatically allocate the EIP for this NAT. 
Regional vs Zonal
NAT in public subnet
EIP get allocated when we associate NAT in the route table.(Automatic vs mannual EIP allocation)
       

| Target                            | Purpose                                                                                            |
| --------------------------------- | -------------------------------------------------------------------------------------------------- |
| **local**                         | Route traffic within the same VPC. Created automatically by AWS.                                   |
| **Internet Gateway (IGW)**        | Provides internet access for public subnets.                                                       |
| **NAT Gateway**                   | Allows private subnets to access the internet without accepting inbound connections.               |
| **Egress-only Internet Gateway**  | IPv6-only outbound internet access.                                                                |
| **Virtual Private Gateway (VGW)** | Connects the VPC to an on-premises network using a VPN or Direct Connect.                          |
| **Transit Gateway (TGW)**         | Connects multiple VPCs and on-premises networks through a central hub.                             |
| **VPC Peering**                   | Enables direct communication between two peered VPCs.                                              |
| **Gateway Endpoint**              | Provides private access to supported AWS services like S3 and DynamoDB without using the internet. |

