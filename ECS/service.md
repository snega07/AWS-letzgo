### Service

An ECS service is a long running resources that helps to maintain the desired number of replicas, service discovery and loadbalancing between healthy replicas.

This helps with the combined functionality of deployment and service in kubernetes.

**Features**

1. Compute configuration:

We can distribute the replicas of task across the different compute type to ensure high availability and to reduce cost.

**Launch Type:**
- We can launch on single compute type. EC2, Fargate and managed EC2.
Capacity Provider Strategy:
- We can distribute the task weight among multiple compute type. Fargate, Fargate Spot.

2. Deployment, Debugging and AZ distribution for high availability

- **ECS exec:** We can enable this to debug inside the task.
- **AZ rebalancing:** To distrubute task across all AZs in a region
- **Deployment:** Rolling update, Blue green, Canary and linear.
- **Deployment failure detection:** Deployment failure detection monitors whether the new tasks become healthy and the deployment progresses successfully. ECS can stop or roll back failed deployments based on the configured deployment strategy. The "bounded/unbounded desired count" terminology isn't something you typically use to describe this feature.

3. Networking, Service connect and discovery, Load balancing.

- Assign security groups (when using awsvpc)
- Configure subnets
- Public or private IP assignment
- Service Connect
- Service Discovery
- Load Balancer integration (ALB/NLB)

4. Autoscaling and Volume:

Automatically adjusts the number of running tasks based on metrics such as:

- CPU utilization
- Memory utilization
- Custom CloudWatch metrics

Configure and mount persistent storage such as:

- Amazon EFS
- Ephemeral storage (Fargate)
- EBS volumes (where supported)
