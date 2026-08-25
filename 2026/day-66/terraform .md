Day 66 — Short Answer

Task 1 — Setup:
Created a Terraform EKS project with separate files for providers, VPC, EKS, variables, and outputs.

Task 2 — VPC:
Used the Terraform VPC module with 2 public and 2 private subnets, DNS enabled, and a single NAT Gateway. Public/private subnet tags help EKS identify subnets for external and internal load balancers.

Task 3 — EKS:
Used the terraform-aws-modules/eks/aws module to create the EKS cluster, IAM roles, security groups, and managed node group with Terraform.

Task 4 — kubectl:
Connected the cluster using:

aws eks update-kubeconfig --name terraweek-eks --region <region>
kubectl get nodes

Verified the nodes were in Ready state.

Task 5 — Nginx:
Deployed 3 Nginx replicas with a LoadBalancer service and verified the application through the external endpoint.

Task 6 — Cleanup:
Deleted the Kubernetes workload first and then ran:

terraform destroy

Verified that the EKS cluster, EC2 nodes, VPC, NAT Gateway, and related resources were removed.

Key takeaway: Terraform made the entire EKS environment repeatable, automated, and destroyable, instead of requiring manual AWS setup.
