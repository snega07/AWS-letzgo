**Root Stack & Nested Stack**

A **Root Stack** is the main CloudFormation template that orchestrates the deployment.
A **Nested Stack** is a reusable CloudFormation template that is called from the root stack using AWS::CloudFormation::Stack.
Nested stacks help organize infrastructure into reusable components, similar to Terraform modules.

Example:

Root Stack (main.yaml)
│
├── Network Stack (network.yaml)
└── Compute Stack (ec2.yaml)

The child templates (network.yaml, ec2.yaml) are typically stored in Amazon S3 and referenced using TemplateURL.

**Rule to remember**

!Ref → Returns the resource's default identifier (ID, ARN, or name depending on the resource type).
!GetAtt → Returns a specific attribute (such as PublicIp, DNSName, or Arn).

Outputs:

  PublicIp:
    Value: !GetAtt MyEC2.PublicIp

**Example**

Root Stack (main.yaml)
```yaml
# Root stack

AWSTemplateFormatVersion: '2010-09-09'

Resources:

  NetworkStack:
    Type: AWS::CloudFormation::Stack
    Properties:
      TemplateURL: https://my-bucket.s3.amazonaws.com/network.yaml

  ComputeStack:
    Type: AWS::CloudFormation::Stack
    DependsOn: NetworkStack
    Properties:
      TemplateURL: https://my-bucket.s3.amazonaws.com/ec2.yaml

      Parameters:
        VpcId: !GetAtt NetworkStack.Outputs.VpcId
        SubnetId: !GetAtt NetworkStack.Outputs.SubnetId

```

**Nested Stack**

``` yaml
# Network stack
AWSTemplateFormatVersion: '2010-09-09'

Resources:

  MyVPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 10.0.0.0/16

  PublicSubnet:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref MyVPC
      CidrBlock: 10.0.1.0/24

Outputs:

  VpcId:
    Description: VPC ID
    Value: !Ref MyVPC

  SubnetId:
    Description: Public Subnet ID
    Value: !Ref PublicSubnet

# Compute stack

 AWSTemplateFormatVersion: '2010-09-09'

Parameters:

  VpcId:
    Type: String

  SubnetId:
    Type: String

Resources:

  MySG:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: Web SG
      VpcId: !Ref VpcId

  MyEC2:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: t2.micro
      ImageId: ami-xxxxxxxx
      SubnetId: !Ref SubnetId
      SecurityGroupIds:
        - !Ref MySG   
```
