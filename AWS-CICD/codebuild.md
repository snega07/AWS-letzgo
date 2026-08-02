### AWS Codebuild

AWS CodeBuild is a fully managed Continuous Integration (CI) service. It is similar to Jenkins agents because it executes build steps and provides the required compute infrastructure automatically.

**Source Providers**

CodeBuild can fetch source code from:

AWS CodeCommit
GitHub
GitHub Enterprise
GitLab
GitLab Self-Managed
Bitbucket
Amazon S3
CodePipeline

| Trigger                          | Branch built                                         |
| -------------------------------- | ---------------------------------------------------- |
| Manual start, no override        | Repository's **default branch** (usually `main`)     |
| Manual start with source version | The specified branch/tag/commit                      |
| Webhook                          | The branch that triggered the webhook                |
| CodePipeline                     | The branch configured in the pipeline's source stage |


**Build Compute (Runner)**

CodeBuild provisions compute to execute your build.

1. On-demand (Most Common)

AWS provisions a temporary build environment.

Managed Image
       ↓
EC2-based Compute
       ↓
Docker Container
       ↓
Run buildspec.yml
       ↓
Destroy environment

Characteristics:

- No infrastructure management
- Fresh environment for every build
Cost: Pay only for build duration. No build no cost.

2. Reserved Capacity (Previously called Fleet)

Instead of AWS provisioning a new environment every time, you can reserve build capacity.

Useful when:

- Many builds run continuously
- Lower startup latency is required
- Build Images

You can use:

AWS managed images (Ubuntu, Amazon Linux, Windows)
Custom Docker images stored in Amazon ECR or Docker Hub
Cost: Pay for the reserved capacity even when no builds are running.

**Build Specification**

The build instructions are stored in:

buildspec.yml

This is similar to:

Jenkinsfile (Jenkins)
GitHub Actions workflow
GitLab CI YAML

The file can be:

Stored in the repository (recommended)
Written directly in the AWS Console

**Typical Build Flow**

Source Checkout
        ↓
Install -> Install required software java, python etc..
        ↓
Pre-build -> like install requirement.txt, maven build docker login. 
        ↓
Build -> Compile or package the application. mvn clean package, docker build
        ↓
Post-build -> Push Docker image to Amazon ECR, Upload artifacts, Generate reports
        ↓
Artifacts 

**Environment Variables and Secrets**

Sensitive values should not be hardcoded. 
**CodeBuild supports:** Systems Manager Parameter Store. Requires IAM permission: ssm:GetParameter, ssm:GetParameters
Examples: Docker username, Docker password, Registry URL, Environment-specific configuration

**AWS Secrets Manager:** Database passwords, API keys, Tokens, Certificates

**Service Role**

The service role allows CodeBuild to: Read from S3, Pull images from Amazon ECR, Read Parameter Store, Read Secrets Manager, Write CloudWatch Logs.

**Privileged Mode**

By default: Docker commands are NOT allowed.
To build Docker images: Enable Privileged Mode
This starts the Docker daemon inside the build environment. Otherwise, docker build fails.

### Important Project Configuration

**Project Type:** Default – Standard CodeBuild project, Runner project – Uses CodeBuild-managed runners for GitHub Actions or GitLab CI workflows
**Source Provider**

GitHub, GitHub Enterprise, GitLab, GitLab Self-Managed, Bitbucket, CodeCommit, Amazon S3, CodePipeline

**Clone Depth**

Options: 1, 5, 25, 50, 100 Full
Recommended: Clone Depth = 1

Advantages:

- Faster clone
- Less network traffic
- Sufficient for most CI builds

Use a full clone only if your build requires Git history (for example, generating changelogs or comparing commits).

**Build Type**
Single build
Batch build -> Batch builds allow multiple builds to execute together (parallel or matrix builds).

**Build Environment**
Choose:
On-demand
Reserved Capacity

Then select:
Managed Image
Custom Docker Image

Then choose the compute type (Small, Medium, Large, XLarge, etc.).

**Advanced Configuration**

Configure:

Build timeout
Retry settings
Environment variables
VPC configuration (if needed)
Privileged Mode (required for Docker builds)
Build Configuration

CodeBuild looks for: buildspec.yml
You can also specify a custom filename or path, for example: ci/buildspec-dev.yml


### Why CodePipeline?

CodeBuild is only responsible for executing one build.

CodePipeline orchestrates an end-to-end CI/CD workflow.

Example:

GitHub
    ↓
CodePipeline
    ↓
CodeBuild
    ↓
Manual Approval
    ↓
CodeDeploy
    ↓
Production

Benefits:

Automatically starts on source code changes
Supports multiple stages
Manual approvals
Parallel actions
Integrates with AWS deployment services

For a simple CI process, CodeBuild can also be triggered directly using GitHub or CodeCommit webhooks without CodePipeline.






