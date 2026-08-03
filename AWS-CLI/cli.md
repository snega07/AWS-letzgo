### AWS CLI

AWS resources can be created using the AWS Management Console (UI). While the UI is suitable for creating a few resources, it becomes time-consuming and error-prone when creating multiple resources or repeating the same setup.

To automate resource creation, AWS provides several Infrastructure as Code (IaC) and automation tools:

AWS CLI
Terraform
AWS CloudFormation (CFT)
AWS Cloud Development Kit (CDK)

**AWS CLI** is a Python-based command-line utility that allows users to interact with AWS services.

It acts as an intermediary between the user and AWS APIs.

User
   │
AWS CLI Command
   │
AWS API
   │
AWS Service (EC2, S3, IAM, etc.)

When you execute an AWS CLI command, the CLI sends an authenticated request to the corresponding AWS API and returns the response, usually in JSON format.

**Advantages**
Automates repetitive tasks
Faster than using the AWS Management Console
Can be used inside shell scripts, PowerShell scripts, or CI/CD pipelines
Supports all AWS services through their APIs

**Installation**

Install the AWS CLI on your machine.

Configure credentials using:

aws configure

It prompts for:

AWS Access Key ID
AWS Secret Access Key
Default Region
Output format (json, yaml, text)
Profiles

Multiple AWS accounts can be configured using named profiles.

Example:

aws configure --profile dev
aws configure --profile prod

Use a specific profile:

aws s3 ls --profile dev

**AWS CLI Reference**

AWS provides an official CLI command reference for every AWS service.

Each command specifies:

Mandatory parameters
Optional parameters
Examples
Output format

If a required parameter is missing, the CLI returns an appropriate error message. On successful execution, the CLI typically returns the resource details in JSON format.



| AWS CLI                                 | Terraform / CloudFormation                    |
| --------------------------------------- | --------------------------------------------- |
| Imperative (run individual commands)    | Declarative (define desired state)            |
| Best for quick operations and scripting | Best for provisioning complete infrastructure |
| No state management                     | Maintains infrastructure state                |
| Limited reusability                     | Highly reusable through modules/templates     |
| Good for automation scripts             | Good for production Infrastructure as Code    |

**When to use**
AWS CLI: Quick tasks, scripting, troubleshooting, automation, CI/CD pipelines.
Terraform: Provisioning complete infrastructure with reusable modules across multiple cloud providers.
CloudFormation: AWS-native Infrastructure as Code.
CDK: Define infrastructure using programming languages (TypeScript, Python, Java, C#, etc.), which is then synthesized into CloudFormation templates.