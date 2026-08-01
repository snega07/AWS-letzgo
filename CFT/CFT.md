### Cloud Formation template

CloudFormation is an Infrastructure as Code (IaC) service that allows us to define AWS resources using YAML or JSON templates. Instead of manually creating resources through the AWS Management Console or AWS CLI, we declare the desired infrastructure in a template. AWS CloudFormation then provisions and manages those resources as a stack.

A stack is a collection of AWS resources that are created, updated, or deleted together based on a CloudFormation template. CloudFormation maintains the stack state internally, so there is no separate state file like Terraform. It also supports drift detection to identify manual changes made outside of CloudFormation.

**Template Creation**

- Create a new template
- Use an existing template
- Use CloudFormation Designer (drag-and-drop interface)

**Advantages**

- Simple infrastructure definition using YAML or JSON
- Declarative approach (define the desired state; CloudFormation creates it)
- Supports drift detection to identify manual changes
- Supports stack updates and rollback on deployment failures
- No separate state file to manage
- Templates can be deployed using the AWS Console, AWS CLI, SDKs, or CI/CD tools such as Jenkins
- Templates can be uploaded from the local system or stored in an S3 bucket
- Supports IAM service roles to control the permissions used during stack operations

**Disadvantages**

- AWS-specific (not multi-cloud)
- Some complex configurations can be verbose compared to higher-level tools like CDK
- Fewer reusable constructs than Terraform modules (though nested stacks and modules are available)

**Template Creation**

- create teamplte
- use existing template
- use template designer -> drag and drop

Templates can be provided from a local file or an S3 bucket. For large templates, deployment tools such as the AWS CLI or CDK may automatically upload the template to S3 before creating or updating the stack."

If you upload templates directly through the AWS Management Console or AWS CLI, an S3 bucket is automatically created for you.

| Feature            | CloudFormation                     | Terraform                             |
| ------------------ | ---------------------------------- | ------------------------------------- |
| Cloud support      | AWS only                           | Multi-cloud                           |
| Language           | YAML / JSON                        | HCL                                   |
| State file         | No (managed by CloudFormation)     | Yes (`terraform.tfstate`)             |
| State locking      | No separate lock file              | Yes (DynamoDB, Terraform Cloud, etc.) |
| Concurrent updates | Only one stack operation at a time | Prevented by state lock               |
| Drift detection    | Supported                          | `terraform plan` detects differences  |


### Why Yaml? 

- Easier to read than JSON
- Supports comments
- Less verbose
- Uses indentation instead of braces and commas

**Structure:**

AWSTemplateFormatVersion
Description
Metadata
Parameters
Rules
Mappings
Conditions
Resources
Outputs

**Resources** is the only mandatory section. All other sections are optional.


**Does every resource get rolled back?**

"CloudFormation performs deployments at the stack level. During stack creation, if any resource fails, CloudFormation automatically rolls back by deleting the resources it created during that operation, provided rollback is enabled. During stack updates, CloudFormation only rolls back the resources involved in the update and restores the stack to its last successful state. Unchanged resources are left as they are."

**Why disable rollback?**

Mainly for troubleshooting:

Inspect partially created resources
Debug configuration or IAM permission issues
Avoid recreating resources repeatedly while testing

**During stack creation**

Yes. If rollback is enabled, CloudFormation deletes all resources that were created as part of that failed stack creation.

**During stack update**

CloudFormation rollback only the resources affected by that update. Resources that weren't changed remain untouched.

For example:

Existing Stack
├── VPC
├── Subnet
├── EC2
└── S3 Bucket

You only modify the EC2 configuration and add an RDS instance.

If RDS creation fails:

VPC → unchanged
Subnet → unchanged
S3 Bucket → unchanged
EC2 → restored to its previous configuration (if possible)
RDS → deleted if it was partially created

**Plugin:**

yaml -> RedHat
aws toolkit
