Task 1 — Understand Infrastructure as Code
1. What is IaC and why does it matter?

Infrastructure as Code (IaC) means managing infrastructure through configuration files instead of manually creating resources through a cloud console.

For example, instead of manually creating an S3 bucket, EC2 instance, and networking through AWS Console, I can describe them in Terraform files and let Terraform create them.

IaC matters because infrastructure becomes repeatable, version-controlled, reviewable, and automated. It also makes it easier for a team to reproduce the same environment. AWS also recommends treating infrastructure definitions as code with version control, reviews, and automated testing.

2. Problems IaC solves

Manual AWS Console management can lead to:

Configuration mistakes
Different environments having different settings
Difficult-to-repeat deployments
No clear history of infrastructure changes
Time-consuming manual work
Problems when another engineer needs to recreate the environment

With IaC, the desired infrastructure is written down and can be reviewed, reused, and recreated.

3. Terraform vs CloudFormation, Ansible, and Pulumi
Tool	Main difference
Terraform	IaC tool using HCL and providers to manage resources across many clouds/services.
CloudFormation	AWS-native IaC service using YAML/JSON templates and primarily designed for AWS resources.
Ansible	Mainly focused on configuration management and automation—configuring software and systems after they exist.
Pulumi	Like Terraform, but infrastructure can be written using general-purpose languages such as Python, TypeScript, Go, Java, etc.
4. What do "declarative" and "cloud-agnostic" mean?

Declarative:
I describe what I want, not every step needed to create it.

For example:

"I want one EC2 instance of this type."

Terraform figures out the required API operations and changes. Terraform's configuration language is explicitly designed around describing the intended goal rather than the sequence of steps.

Cloud-agnostic:
Terraform isn't limited to AWS. Through providers, it can manage AWS, Azure, Google Cloud, Kubernetes, SaaS services, and other platforms.

Task 2 — Install Terraform and Configure AWS

Use the installation commands from your assignment for your operating system.

Then verify:

terraform -version

Configure AWS:

aws configure

Verify authentication:

aws sts get-caller-identity

Important: don't put AWS access keys directly inside main.tf or commit them to Git. Let the AWS CLI credentials/environment configuration handle authentication. Terraform's AWS provider supports external authentication mechanisms.

Task 3 — First Terraform Configuration

Create the project:

mkdir terraform-basics
cd terraform-basics

Example main.tf:

terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 6.0"
    }
  }
}

provider "aws" {
  region = "ap-south-1"
}

resource "aws_s3_bucket" "demo" {
  bucket = "terraweek-govind-2026-unique"
}

Note: S3 bucket names must be globally unique, so change the bucket name if it is already taken.

Run:

terraform fmt
terraform validate
terraform init
terraform plan
terraform apply
What does terraform init download?

It downloads the AWS provider plugin required by your configuration and creates the local Terraform working structure. Terraform providers are separate plugins that Terraform installs during initialization.

What is .terraform/?

It is Terraform's working directory. It can contain downloaded providers and modules, plus Terraform's local working information. You should not commit it to Git.

Task 4 — Add EC2

Add an EC2 resource:

resource "aws_instance" "web" {
  ami           = "YOUR_REGION_AMI_ID"
  instance_type = "t2.micro"

  tags = {
    Name = "TerraWeek-Day1"
  }
}

Use the correct Amazon Linux AMI for your AWS region rather than blindly using the AMI from the example.

Then:

terraform plan
terraform apply
How does Terraform know the S3 bucket already exists?

Terraform maintains a state file that maps resources in the configuration to real infrastructure.

For example:

aws_s3_bucket.demo
        ↓
actual S3 bucket

When Terraform runs again, it compares the configuration and current state and determines what needs to change.

Task 5 — Understand the State File

Run:

terraform show
terraform state list

You'll see resources such as:

aws_s3_bucket.demo
aws_instance.web

Then:

terraform state show aws_s3_bucket.demo
terraform state show aws_instance.web
What does terraform.tfstate contain?

It contains information Terraform needs to manage your infrastructure, including:

Resource-to-real-world mappings
Resource attributes
Resource IDs
Dependencies and metadata
Cached information used during planning

Terraform describes state as the mapping between Terraform resources and real infrastructure objects.

Why shouldn't I manually edit it?

Because Terraform manages the state structure itself. Manual changes can break the mapping between your configuration and real infrastructure. Terraform provides terraform state commands for controlled state operations.

Why shouldn't it be committed to Git?

State can contain sensitive information, and sharing a state file through Git also creates collaboration and locking problems. Terraform recommends remote state with appropriate access controls for team environments.

Task 6 — Modify, Plan and Destroy

Change:

Name = "TerraWeek-Day1"

to:

Name = "TerraWeek-Modified"

Run:

terraform plan

Terraform's symbols mean:

+  create
~  modify/update
-  destroy

For this tag change, Terraform should normally show an in-place update (~), not destroy and recreate the EC2 instance.

Apply:

terraform apply

Finally:

terraform destroy

Confirm with:

yes

Then verify that both the S3 bucket and EC2 instance have been removed.

.gitignore

Create .gitignore:

.terraform/
*.tfstate
*.tfstate.backup

Then your basic project structure will look like:

terraform-basics/
├── main.tf
├── .gitignore
├── .terraform/
├── .terraform.lock.hcl
└── terraform.tfstate

For a real project, .terraform/ and state files should not be committed to Git; the lock file, however, is normally committed so provider versions can be reproduced consistently.
