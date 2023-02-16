# (Optional) Defining infrastructure with Terraform

Goal: Manage the Cloud infrastructure using code, such that the infrastructure can be easily re-created, teared down, etc.

## Required Reading

- [Introduction to Terraform | Baeldung](https://www.baeldung.com/ops/terraform-intro)
- [Get Started - AWS | Terraform - HashiCorp Learn](https://learn.hashicorp.com/collections/terraform/aws-get-started)
- [3 Steps To Migrate Existing Infrastructure To Terraform (openupthecloud.com)](https://openupthecloud.com/refactor-existing-infrastructure-with-terraform/)

## Online Shop

Migrating resources:

- Initialize an empty terraform project with local state management.
- Start progressively defining the infrastructure that you built in the previous chapters using terraform resources. 
- At each step, [import](https://developer.hashicorp.com/terraform/cli/import) the resource into the terraform state and run a terraform plan to ensure that nothing major is would be changed by terraform (especially that the load balancer, cache and database instances are NOT replaced).
- Add the resources in the following order:
  - VPC + Security groups
  - RDS database
  - ElastiCache cache cluster
  - Launch template (build the user data using a [terraform template](https://www.terraform.io/language/functions/templatefile) file)
  - Auto-scaling group
  - Load balancer
- Perform a terraform apply and check that the application still works.

Defining inputs & outputs

- Define parameters for passing in:
  - The SSH key pair name,
  - The instance types for EC2+RDS+ElastiCache,
  - The application version.
- Define outputs for:
  - The database hostname + port
  - The cache hostname + port
  - The application base URL (including port, protocol)
- Perform a terraform apply and check that the application still works and that you can still SSH into the EC2 instance(s).

Using data sources

- Use a terraform data source to find the latest Amazon Linux 2 AMI to use in the launch template instead of hard coding it.
- Perform a terraform apply and check that the application still works.

Preventing data loss

- Use the terraform [lifecycle meta argument](https://www.terraform.io/language/meta-arguments/lifecycle) to prevent destroying the RDS instance, load balancer and VPC.
- Temporarily comment out the database resource and perform a terraform plan. Check that the RDS instance would not be destroyed.

## Further Resources

- [What Is Terraform Used For? The 3 Main Use Cases. (openupthecloud.com)](https://openupthecloud.com/what-is-terraform-used-for/)
- [Docs overview | hashicorp/aws | Terraform Registry](https://registry.terraform.io/providers/hashicorp/aws/latest/docs)
