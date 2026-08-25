Day 67 — Short Answer

Task 1 — Workspaces:
Terraform workspaces allow one codebase to manage separate environments. terraform.workspace returns the current workspace, and each workspace maintains its own state.

Task 2 — Structure:
Created separate files for providers, variables, outputs, locals, and resources, with reusable VPC, security-group, and EC2 modules.

Task 3 — Modules:
Built three focused modules:

VPC → VPC, subnet, routing, and gateway
Security Group → dynamic ingress rules
EC2 → reusable instance configuration

Task 4 — Multi-Environment:
Used terraform.workspace and separate .tfvars files for dev, staging, and prod. Each environment has different CIDRs, instance sizes, and allowed ports.

Task 5 — Deployment:
Deployed all three environments using the same Terraform code:

terraform workspace select dev
terraform apply -var-file="dev.tfvars"

terraform workspace select staging
terraform apply -var-file="staging.tfvars"

terraform workspace select prod
terraform apply -var-file="prod.tfvars"

This provides isolated VPCs, security groups, and EC2 instances for each environment.

Task 6 — Best Practices:
Used remote state, variables, reusable modules, version pinning, consistent tagging/naming, fmt, validate, and plan before apply.

Task 7 — Cleanup:
Destroyed dev, staging, and prod separately, then removed the workspaces.

Key takeaway: One Terraform codebase can manage multiple isolated environments consistently and efficiently, reducing duplication and manual configuration.
