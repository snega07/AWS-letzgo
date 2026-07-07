### Cluster

An ECS cluster is a logical grouping of task and services. While creating ECS cluster we can decide on the underlying compute type resource(host used by our application) where our ECS task runs. It supports Fargate, EC2(AWS managed & self-managed)

**Features:**

Service connect:

- Enables secure service-to-service communication.
- Uses a namespace for service discovery, so services can communicate using names instead of IP addresses.

Container insights:

- Collects CPU, memory, network, and task metrics.
- Integrates with Amazon CloudWatch for monitoring and logging.

ECS exec:

- Allows you to securely execute commands inside a running container without SSH.
Storage encrytion(KMS key)

Logging Encryption:

- Encrypts ECS logs stored in CloudWatch Logs using a KMS key.

Storage Encryption:

- Encrypts ECS-managed storage or attached storage (such as EBS/EFS where applicable) using AWS KMS.

