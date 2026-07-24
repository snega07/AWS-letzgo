### EKS
EKS is a container orchestration solution built on kubernetes managed by AWS. When we create an EKS cluster, the control plane is always managed by AWS. For the worker nodes, we can choose one of the following options:

### Features:

**Security:** AAWS manages the EKS control plane, including security patches, OS maintenance, and Kubernetes control plane upgrades. Worker node maintenance depends on whether you use Fargate, Managed Node Groups, or Self-managed Node Groups.
**Easy Setup:** AWS will install all the necessary control plane components.
**Availability:** AWS runs the EKS control plane across multiple Availability Zones and manages its high availability independently of your worker nodes.


1. **AWS Fargate**

- AWS provisions compute on demand for your pods.
- There are no EC2 worker nodes for you to manage.
- You don't manage scaling, patching, or OS updates.
- You simply create a Fargate profile, and eligible pods run on Fargate.
- Each pod gets its own ENI and IP address.

**When we have requirement like our node must use rhel distrubution any specific requirement on the node configuration we mjst go with EC2 worker node**

2. **Managed Node Groups**

- AWS provisions and manages the EC2 worker nodes.
AWS handles:
- Creating and managing the Auto Scaling Group
- Installing and configuring worker node components (kubelet, containerd, etc.)
- Automatically joining nodes to the EKS cluster
- Replacing unhealthy nodes
- AWS provides managed mechanisms for node upgrades and updates, but you decide when to perform them.
- You still have access to the EC2 instances, so you can SSH (if enabled), install additional software, and apply Kubernetes concepts like labels, taints, and tolerations.

AWS provides managed update mechanisms for managed node groups, but you are still responsible for initiating node updates and choosing when to apply them. AWS doesn't silently patch your worker nodes whenever it wants.

3. **Self-managed Node Groups**

- You provision and manage the EC2 instances yourself.
- You are responsible for:
Creating the Auto Scaling Group
Installing worker node components
Joining nodes to the cluster
Patching the OS
Upgrading Kubernetes components
Replacing failed nodes
You have complete control and flexibility over the worker nodes.
One important correction

**Steps:**

### Pre requisites:
Install Kubectl, aws cli, eksctl

### Create cluster

Below command creates a cloudformation(AWS Iac) stack. Which creates below resources

VPC, private subnet, public subnet, RTB, NAT Gateway, IGW

eksctl creates a VPC with both public and private subnets by default. The EKS control plane is managed by AWS, and worker nodes/Fargate pods are typically placed in the private subnets.

``` hcl
eksctl create cluster --name demo-cluster --region us-east-1 --fargate
```

### Create Fargate profile and namespace

In EKS with Fargate launch mode we don't have control over the worke node and where to deploy based on labels, selector or taint toleration. 

Here we need to map fargate profile with the namespace or labels. A Fargate profile defines which pods are eligible to run on Fargate by matching their namespace (and optionally labels). Pods in matching namespaces are scheduled onto Fargate.

Namespace is mandatory.

**Flow**

The scheduler checks:

Does a Fargate profile exist?
Does its selector match this pod's namespace?
If yes, the pod is scheduled on Fargate.

``` hcl

**Create Profile**

eksctl create fargateprofile \
  --cluster demo-cluster \
  --name game-profile \
  --namespace game-2048 \
  --region ap-south-1

Labels/namespace:

eksctl create fargateprofile \
  --cluster demo-cluster \
  --name production-profile \
  --namespace production \
  --labels app=nginx,env=prod
**Create Namespace**

kubectl create namespace <namespace-name>

**One fargate profile for multiple namespace**

eksctl create fargateprofile \
  --cluster demo-cluster \
  --name apps-profile \
  --namespace dev \
  --namespace test \
  --namespace prod

```

**Note:**

If you created namespace, but haven't created or mapped fargate profile to the namepsace. Deployment or pod using the namepsace will get into pending state.

If no namespace mentioned in deployment yaml. Then deployment will be created in default namespace.


### Enable OIDC connector and Iam roles for SA(IRSA)

Associating an IAM OIDC provider allows AWS IAM to trust Kubernetes ServiceAccount tokens issued by the EKS cluster. This enables IAM Roles for Service Accounts (IRSA), where a pod using a specific ServiceAccount can assume an IAM role and obtain temporary AWS credentials.


``` hcl
eksctl utils associate-iam-oidc-provider --cluster $cluster_name --approve
```

**Use Case:**
- When we create ALB Ingress Controller. ALB Controller pod need to access ALB resource to create loadbalancer that will be used by our ingress resources.

### IAM role and SA setup

Create policy which will help to interact with other AWS services with necessary permissions and attach it to role. Associate the IAM role with the Kubernetes ServiceAccount using IRSA. Pod use this SA.

What's actually happening is:

Create IAM Role
Attach IAM Policy
Associate the IAM Role with the Kubernetes ServiceAccount using IRSA

``` hcl

curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.11.0/docs/install/iam_policy.json

aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam_policy.json

Create IAM role and SA

eksctl create iamserviceaccount \
  --cluster=<your-cluster-name> \
  --namespace=kube-system \
  --name=aws-load-balancer-controller \
  --role-name AmazonEKSLoadBalancerControllerRole \
  --attach-policy-arn=arn:aws:iam::<your-aws-account-id>:policy/AWSLoadBalancerControllerIAMPolicy \
  --approve
```

### Ingress Controller

Kubernetes provides the Ingress API but does not include an Ingress Controller by default. To make use of ingress resources, we need to install ingress controller. 

Available options:

nginx
ALB
F5
HAProxy

**Installing ALB Ingress controller**

When the AWS Load Balancer Controller detects an Ingress resource, it creates an ALB, Target Groups, Listeners, and Listener Rules, then registers the backend targets.

Each Ingress resource creates it own ALB. To make multiple ingress use same ALB.

``` hcl
you must configure Ingress Groups using annotations such as:

alb.ingress.kubernetes.io/group.name: my-app
```

We can use Ingress classname to specify which ingress controller to use.

``` hcl

Add helm repo

helm repo add eks https://aws.github.io/eks-charts

Update Repo

helm repo update eks

Install ALB Ingress Controller

helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system \
  --set clusterName=<your-cluster-name> \
  --set serviceAccount.create=false \
  --set serviceAccount.name=aws-load-balancer-controller \
  --set region=<your-region> \
  --set vpcId=<your-vpc-id>

kubectl get deployment -n kube-system aws-load-balancer-controller

```

**Ingress**

Annotation scheme and target-type tells which ALB to create either public or internal and register POD IP in the traget taking tit from service endpoint slices. 

apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  namespace: game-2048
  name: ingress-2048
  annotations:
    alb.ingress.kubernetes.io/scheme: internet-facing
    alb.ingress.kubernetes.io/target-type: ip
spec:
  ingressClassName: alb
  rules:
    - http:
        paths:
        - path: /
          pathType: Prefix
          backend:
            service:
              name: service-2048
              port:
                number: 80

### Ingress Target Type

ip -> The AWS Load Balancer Controller reads the backend Service referenced by the Ingress, retrieves the Service's Endpoints/EndpointSlices, and registers the pod IPs in the ALB Target Group when target-type: ip is used

instance ->The Target Group registers EC2 instances. The ALB sends traffic to the NodePort exposed on each node, and Kubernetes routes the request to one of the backend pods.

**Why use target-type: ip?**

It's required for AWS Fargate, because there are no EC2 worker nodes to register.

It's also commonly used with EC2 worker nodes because it lets the ALB send traffic directly to the pods, avoiding an extra hop through a node.

**Note:**

| Compute | Recommended                                                                 |
| ------- | --------------------------------------------------------------------------- |
| Fargate | `target-type: ip` + `ClusterIP`                                             |
| EC2     | `target-type: ip` + `ClusterIP` **or** `target-type: instance` + `NodePort` |

EC2, both modes are supported. In fact, many deployments prefer target-type: ip because it routes directly to pods and avoids the extra hop through the node.
