**Security Group:**

Route table just say how the traffic must flow to destination. But it won’t specify which protocol or port to allow for inbound or outbound traffic flow.
Support allow rules only.
Works at resource level.

**Network ACLs:**

A Network ACL (NACL) acts as a stateless firewall at the subnet level and controls inbound and outbound traffic by allowing or denying traffic based on protocol, port, and IP ranges.

- NACL is stateless:
    - return traffic is NOT automatically allowed
    - you must create both inbound and outbound rules
 
