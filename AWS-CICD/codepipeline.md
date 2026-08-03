### AWS Codepipeline

AWS CodePipeline is a managed CI/CD orchestration service. It automates the entire software delivery process by connecting different stages such as Source → Build → Test → Approval → Deploy.

CodeBuild performs the build (CI).
CodePipeline orchestrates the complete CI/CD workflow.

**Jenkins**

Need to install and maintain Jenkins.
Master-Agent (Master-Slave) architecture.
Agents may run on VMs or Docker containers.
As build agents increase, infrastructure management becomes difficult.
Responsible for:
OS patching
Jenkins upgrades
Plugin management
Scaling workers
High availability

**CodePipeline**

Fully managed by AWS.
AWS manages the underlying infrastructure.
No servers to install or maintain.
Easy integration with AWS services.

**Limitation**

AWS vendor lock-in.
Costs can increase if many pipelines execute frequently.

### Relationship between CodePipeline and CodeBuild

GitHub
   │
   ▼
CodePipeline
   │
   ├── Source Stage
   ├── Build Stage (CodeBuild/Jenkins)
   ├── Test Stage
   ├── Approval Stage
   └── Deploy Stage

Only executes build commands.
Reads buildspec.yml.
Produces build artifacts.
Runs tests.
Builds Docker images.
Pushes images to ECR/Docker Hub.

It does not provide complete pipeline orchestration.

### Why not only CodeBuild?

CodeBuild is mainly a build service (CI).

Although CodeBuild can be started:

manually
by webhooks
by EventBridge schedules
by CodePipeline

it does not provide:

Multi-stage pipelines
Manual approval gates
Sequential stage execution
Artifact flow between stages
Deployment orchestration
Multiple deployment targets

These are responsibilities of CodePipeline.

#### Configurations:

**Pipeline Categories:**

Superseded(Always run the build for latest commit, even if earlier commit running it stops)
Queued(Executes commits one by one)
Parallel(Multiple executions run simultaneously)

**Artifact Store:**

Pipeline artifacts are stored in an S3 bucket.

Artifacts include

Source code
Build output
Deployment package

Can configure

S3 bucket
Encryption (KMS)
Pipeline variables

**Source Stage**

Supported sources include

GitHub
CodeCommit
Bitbucket
S3

Configuration

Repository
Branch
Authentication
Trigger settings
Output artifact format

**Build Stage**

Usually integrates with

CodeBuild
Jenkins
Other supported build providers

You can override the project's default buildspec.yml by specifying a Buildspec override in the pipeline action.

**Deploy Stage**

Can deploy using

CodeDeploy
ECS
EKS
CloudFormation
Elastic Beanstalk
Lambda

**Approval Stage**

Supports manual approval before deployment.

**Triggers**

Pipeline can start from

GitHub Push
Pull Request
CodeCommit
EventBridge
Manual execution


**With only CodeBuild:**

You would write one large buildspec.yml containing all these steps.
If deployment fails, rerunning just the deployment isn't straightforward—you often rerun the entire build.
No built-in manual approval.
No visual stage-by-stage pipeline.
Harder to reuse stages and artifacts.

**With CodePipeline:**

Each stage is separate.
Artifacts are passed between stages.
You can add approval gates.
You can see exactly which stage failed.
Easier to modify or replace individual stages.
Better integration with deployment services like CodeDeploy.


**Note:**

Few projects with different build requirements: Keep buildspec.yml in each repository.
Many projects with identical build steps: Use a shared buildspec (via BuildSpec override) or inline buildspec if the logic is simple.
Enterprise environments: A shared, version-controlled buildspec is usually preferred over inline YAML because it is easier to audit, review, and maintain.








How CICD works??

GitHub(code commited) -> Triggers pipeline using webhook(Jenkins or tekton) -> clone code -> run build -> unit test ->Build container Image -> SBOM and CVM report -> Push to artifactory -> Deployment trigger(Harness, ArgoCD, Udeploy) or end of CI.

CD -> ansible, shell scripts
advanced -> argocd,fluxcd, spinaker -> GitOps

Code pipeline -> AWS code build helps to define CI stages

why code pipeline??

install jenkins -> master slave architecture -> as worker nodes increases we need take care of these servers nodes and manage them. Either the slave is docker or VMs

code pipeline underlying infras and instances are maintainer by AWS.

vendor specific
Cost may increase if not used properly


choose category: 

Deployment, CI, automation -> helps to provide default available template for AWS.(No config needed)
Custom pipeline -> 

Execution mode : Superseded, queue, parallel

Configure artifact store location, encryption settings, and pipeline variables for your pipeline.

Add source provider -> github auth, repo, Output artifact format, trigger of pipeline

Config build provider, Configure build spec override to override the file configured in codebuild

Build stage, test stage, deploy stage

supports triggers
Exactly. The trust policy (AssumeRole policy) defines who is allowed to assume the role.

{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "codebuild.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}