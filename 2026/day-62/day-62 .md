Task 1 — IaC

IaC means managing infrastructure using code instead of manually using the AWS Console. It makes infrastructure repeatable, automated, version-controlled, and easier to maintain.

Terraform: Multi-cloud IaC using HCL.
CloudFormation: AWS-focused IaC using YAML/JSON.
Ansible: Mainly configuration management and automation.
Pulumi: IaC using programming languages like Python, Go, or TypeScript.
Declarative: I describe what I want, and Terraform determines how to create it.
Cloud-agnostic: Terraform can manage resources across different cloud platforms.
Task 2 — Setup
terraform -version
aws configure
aws sts get-caller-identity
Task 3 — S3

Create main.tf with the AWS provider and S3 bucket, then run:

terraform fmt
terraform validate
terraform init
terraform plan
terraform apply

terraform init downloads the required provider. .terraform/ stores Terraform's local working files and providers.

Task 4 — EC2

Add an EC2 resource with the correct regional AMI and t2.micro.

terraform plan
terraform apply

Terraform knows the S3 bucket already exists because it tracks resources in the state file.

Task 5 — State
terraform show
terraform state list
terraform state show aws_s3_bucket.demo
terraform state show aws_instance.web

The state stores resource mappings, IDs, attributes, and metadata. Don't manually edit or commit state files because they can contain sensitive information and cause state-management problems.

Task 6 — Modify & Destroy

Change the EC2 tag:

TerraWeek-Day1 → TerraWeek-Modified

+ = create, ~ = modify, - = destroy.

A tag change is normally an in-place update (~).

Finally:

terraform apply
terraform destroy
.gitignore
.terraform/
*.tfstate
*.tfstate.backup

Keep .terraform.lock.hcl in Git so provider versions remain reproducible.
