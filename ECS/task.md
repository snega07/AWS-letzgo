### Task

An ECS Task is the smallest runnable unit in Amazon ECS. It is created from a Task Definition and consists of one or more containers that are scheduled and run together.

It is conceptually similar to a Pod in Kubernetes because the containers in a task share the same lifecycle and (depending on the network mode) networking resources.

**Features:**

- Launch Type Compatibility: Can run on EC2, Fargate, or External compute (depending on the task definition and service configuration).
- Task Execution Role: Allows the ECS agent to perform actions on behalf of the task, such as:
Pulling images from Amazon ECR
Sending logs to CloudWatch
Retrieving secrets
- Task Role (IAM Role): Permissions used by the application running inside the container.
Example: Access Amazon S3 or DynamoDB.
- Container Definitions: Container image, Environment variables, Secrets, Entry point and command, CPU and memory, Essential container configuration
- Port Mappings: Maps container ports to the host (for bridge or host modes) or exposes container ports using the task's IP (awsvpc).
- Health Checks: Checks whether the application inside the container is healthy.
- Resource Limits: CPU, Memory
- Volumes: EFS, Ephemeral storage, Other supported volume types
- Logging: Configure log drivers (for example, CloudWatch Logs).


Cluster
    │
    ├── Provides available compute (Fargate/EC2/etc.)
    │
Task Definition
    ├── Defines the application
    ├── CPU, Memory
    ├── Volumes
    ├── Networking
    ├── Containers
    │
Service
    ├── Chooses how to run the task
    ├── Launch Type or Capacity Provider
    ├── Desired Count
    ├── Load Balancer
    └── Auto Scaling