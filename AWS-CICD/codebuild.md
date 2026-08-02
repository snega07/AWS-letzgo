### AWS Codebuild

AWS codebuild is like jenkins which helps to run build stages for a application and provides required underlying runner infrastructure.

We need to connect it with source provider like code commit, guthub, bitbucket

Worker node (Compute for builds):

It will provision on demand or we can specify required capacity.

On demand -> Managed image ->compute (EC2)-> Container(running mode)

Custom -> Create required capacity -> Use your own image -> Lambda(Compute) -> running mode EC2

Config file -> Build spec file(Push from github or write directly in aws console)(Common)

code checkout happens automatically
install required software in the container image base.
pre build like pip install
Build: build docker image

aws system manager -> jenkins jenkins creds, vault

AWS system Manager: -> Parameter Store(senstive info in secured location)(docker username, password, registry URL and other required parameters required for the build) ->(Need service role) role used to communicate btw service to service

Post build: No deployment only CI no CD

why AWS cpde pipeline -> This act as aorchestration and when ever a changes made in Source provider repo this will trigger the code build no mannual intervention required.


By default -> The runner won't allow image build we need to enable previledged


Configs as below:

Project type: default -> codebuild project, Runner project -> use codebuild runner for workflows in gitlab, github.
Source provider -> Github, Gitlab, Githuh enterprise.
Clone depth -> 1,25, 100 or full commits. For build it is better to keep depth 1 since we always we build the content is latest commit.
Build type -> Single Build, batch build
Build environment -> On demand, Reserver Capacity.

On demand -> Managed image ->compute (EC2)-> Container(running mode)
Custom -> Create required capacity -> Use your own image -> Lambda(Compute) -> running mode EC2
Advanced config for build env Build retry and timeout, Enbale flag to do docker build this not enbaled by default, enbale this previledged


Service role -> with System manager permission for build

Insert or detect build config file: buildspec.yaml -> scan the availability for this file to initiate build.

We can give our custom build config file name


