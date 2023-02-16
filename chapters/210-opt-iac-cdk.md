# (Optional) Defining infrastructure with AWS CDK 

Goal: Manage the Cloud infrastructure using code, such that the infrastructure can be easily re-created, teared down, etc.

## Required Reading

- [What is the AWS CDK? - AWS Cloud Development Kit (CDK) v2 (amazon.com)](https://docs.aws.amazon.com/cdk/v2/guide/home.html)
- [Your first AWS CDK app - AWS Cloud Development Kit (CDK) v2 (amazon.com)](https://docs.aws.amazon.com/cdk/v2/guide/hello_world.html)

## Online Shop

Destroy all the infrastructure from the previous chapter(s) by doing terraform destroy and/or manually deleting the resources (database, load balancer, VPC).

Defining resources

- Bootstrap the cdk into the AWS account and initialize an empty cdk project.
- Start progressively defining the infrastructure that you built in the previous chapters using CDK constructs. 
- Create your own constructs for:
  - The network layer, including the VPC, internet gateway, subnets, security groups.
  - The persistence layer, including the database and the cache.
  - The application layer, including the load balancer and auto-scaling group.
- Perform a cdk deploy and check that the application works.

Defining inputs & outputs
- Define stack props for passing in:
  - The SSH key pair name,
  - The instance types for EC2+RDS+ElastiCache,
  - The application version.
- Fill them in based on environment variables when instantiating the stack (with sensible defaults).
- Define CfnOutputs for:
  - The database hostname + port
  - The cache hostname + port
  - The application base URL (including port, protocol)
- Perform a cdk deploy and check that the application still works.

Using lookups

- Use [a lookup](https://docs.aws.amazon.com/cdk/api/v2/docs/aws-cdk-lib.aws_ec2-readme.html#machine-images-amis) to find the latest Amazon Linux 2 AMI to use in the launch template instead of hard coding it.
- Perform a cdk deploy and check that the application still works.

Preventing data loss

- Use the cdk [RemovalPolicy](https://docs.aws.amazon.com/cdk/api/v2/docs/aws-cdk-lib.RemovalPolicy.html) to prevent destroying the RDS instance, load balancer and VPC.
- Temporarily comment out the database resource and perform a cdk diff. Check that the RDS instance would not be destroyed.

## Further Resources

- [API Reference - AWS CDK (amazon.com)](https://docs.aws.amazon.com/cdk/api/v2/docs/aws-construct-library.html)
- [GitHub - aws-samples/aws-cdk-examples: Example projects using the AWS CDK](https://github.com/aws-samples/aws-cdk-examples)
