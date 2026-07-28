### Managed or Self Managed Node groups
- In Amazon EKS, we can create either managed or self-managed node groups.
Managed node groups are managed by AWS.
- Self-managed node groups are created using eksctl with --managed=false, and we are responsible for their lifecycle, upgrades, and maintenance.
- Once node groups are added pods will get scheduled on the assigned EC2 nodes automatically.

### No nodegroup(Fargate)
- If we create an EKS cluster using --without-nodegroup, only the control plane is created. If there's no matching Fargate profile, the Pods(coredns, kube metrics) stay Pending. Once a suitable Fargate profile exists and is active, Kubernetes can schedule them (sometimes you may need to recreate existing Pending Pods, as you experienced).
- To run workloads on AWS Fargate, we must create one or more Fargate profiles. A Fargate profile acts as a scheduling rule based on namespace and optional label selectors. It does not create worker nodes. Once Pods matching the profile are scheduled, AWS provisions the required Fargate infrastructure automatically. kubectl get nodes will then show virtual Fargate nodes instead of EC2 worker nodes.
- AWS Fargate does not support privileged containers or workloads that require modifying host kernel parameters (for example, changing vm.max_map_count). Therefore, applications such as the default Elasticsearch Helm chart are better suited to EC2 worker nodes.

### EBS Volume
- To dynamically provision Amazon EBS-backed Persistent Volumes, we need to install the Amazon EBS CSI Driver add-on. The CSI driver watches for PersistentVolumeClaims (PVCs), creates Amazon EBS volumes, creates the corresponding PersistentVolumes (PVs), and binds them to the PVCs.
- Amazon EBS-backed persistent storage is primarily intended for EC2-based EKS worker nodes. Although EKS Fargate supports EBS volumes for compatible workloads, many stateful applications (such as the default Elasticsearch deployment) are not suitable for Fargate because of its runtime restrictions

- Give current cluster register with kubectl context
>kubectl config get-contexts
CURRENT   NAME                                                 CLUSTER                             AUTHINFO                                             NAMESPACE
*         iam-root-account@observability.us-east-1.eksctl.io   observability.us-east-1.eksctl.io   iam-root-account@observability.us-east-1.eksctl.io

Kube config will update when we do cretae cluster
