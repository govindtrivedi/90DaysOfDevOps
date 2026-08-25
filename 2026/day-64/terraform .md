Day 64 — Short Answer

Task 1 — State:
Terraform state maps my .tf configuration to real AWS resources. I used terraform show, terraform state list, and terraform state show to inspect resources and their attributes.

Task 2 — Remote Backend:
I migrated local state to an S3 backend with versioning and DynamoDB locking. This protects state and allows safer team collaboration.

Task 3 — State Locking:
Locking prevents multiple Terraform operations from modifying state simultaneously, reducing the risk of state corruption. force-unlock should only be used when I’m certain no operation is running.

Task 4 — Import:
terraform import brings an existing AWS resource under Terraform state management without recreating it.

Task 5 — State Surgery:

state mv → rename/move a resource in state without destroying it.
state rm → remove a resource from Terraform state while keeping it in AWS.
import → bring it back under Terraform management.

Task 6 — Drift:
I simulated drift by changing an EC2 resource manually in AWS. terraform plan detected the difference, and terraform apply restored the configuration. Teams can reduce drift by restricting manual changes and using CI/CD for infrastructure changes.

Key takeaway: Remote state, locking, imports, and drift management are essential for reliable Terraform in team and production environments.
