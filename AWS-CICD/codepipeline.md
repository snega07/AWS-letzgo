
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