## Network mode in ECS Cluster:

Network mode decides how ECS task connect to the VPC network.

NOTE:

✅ ECS uses a container runtime (Docker historically, and now containerd under the hood on newer ECS-optimized AMIs and Fargate) to actually run containers.
✅ Fargate runs your containers on AWS-managed compute that is based on EC2 infrastructure internally.

| Launch Type               | Supported Network Modes            |
| ------------------------- | ---------------------------------- |
| **Fargate**               | `awsvpc` only                      |
| **EC2**                   | `awsvpc`, `bridge`, `host`, `none` |
| **ECS Managed Instances** | `awsvpc`, `host`                   |


### Host:

- The container uses the same IP address as the EC2 instance.
- No Docker bridge or NAT is involved.
- Applications are accessed using the EC2 instance's IP and the application's port.
- If two tasks use the same port, they cannot run on the same EC2 instance.

EC2 IP: 10.0.1.10

Task A → 10.0.1.10:8080 ✅
Task B → 10.0.1.10:8080 ❌

**Pros:**

- Fast networking
- No NAT
- Lowest latency
- Good for high performance workloads.

**Cons:**

- Port conflicts
- Container shares the same network. Less isolation from host.

### Bridge:

Docker creates a bridge network on the EC2 instance. Each container gets a Docker private IP (for example, 172.17.x.x).

EC2 IP : 10.0.1.10

Host Port : 32768

↓

Container : 172.17.0.2:8080

**Pros:**
- Efficient use of EC2 IPs.
- Can run many containers on one instance.
- Containers are isolated from the host network.

**Cons:**
- Port mapping required.
- Harder networking.
- No security group per container.

### Awsvpc:

AWS provides each task with its own ENI (Elastic Network Interface), private IP address, and security groups. The Amazon ECS CNI plugin configures this networking. Since each task has its own IP address, multiple tasks can listen on the same port (for example, 8080) without port conflicts.

Task 1
IP : 10.0.1.15:8080

Task 2
IP : 10.0.1.16:8080

**Pros:**
- Each task get its ownIP.
- Can reuse some port for multiple services. No port mapping no port conflicts.
- Task level security group.

**Cons:**
- Consume one ENI per task.
- ENI limits on EC2 can restrict nbumber of task you run.

### None

The container has no networking.

It:

Cannot receive network traffic
Cannot access the internet
Cannot communicate with other containers

Typically used for isolated or specialized batch workloads.

**Pros:**
Complete network isolation
**Cons:**
No network connectivity.

### Default

When you select Default, ECS doesn't explicitly set a Docker network mode. Instead, Docker uses its default networking behavior, which is the bridge network unless the Docker daemon has been configured differently.
