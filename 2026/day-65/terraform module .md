Day 65 — Short Answer

Task 1 — Modules:
Terraform modules package reusable infrastructure. The root module calls and connects modules, while child modules contain reusable resource definitions.

Task 2 — EC2 Module:
Created a reusable EC2 module with variables for AMI, instance type, subnet, security groups, name, and tags. Outputs include instance ID, public IP, and private IP.

Task 3 — Security Group Module:
Created a reusable security group module using a dynamic ingress block to generate rules from a list of ports.

Task 4 — Module Reuse:
Called the same EC2 module twice to create web and API servers with different names while sharing the same security group.

Task 5 — Registry Module:
Replaced the manually created VPC with the Terraform AWS VPC registry module, making the configuration shorter and reusable. Registry modules are downloaded under .terraform/modules/.

Task 6 — Best Practices:

Pin module versions.
Keep modules focused.
Use variables instead of hardcoding.
Define useful outputs.
Add a README to each custom module.

Key takeaway: Modules allow me to write infrastructure once and reuse it across multiple environments and projects, making Terraform easier to maintain and scale.
