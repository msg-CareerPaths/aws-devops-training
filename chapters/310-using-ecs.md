# Using cloud-native services for running containers

Goal: Run the container via a managed service, instead of manually installing docker and pulling the image on a bare-bones EC2 server.

## Required Reading

- [What is Amazon Elastic Container Service? - Amazon Elastic Container Service](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/Welcome.html)

## Online Shop

Replace bare EC2s with ECS
- Adjust the IaC stack to replace the ASG and EC2 instances with (note that for CDK, you can directly use the ApplicationLoadBalancedEc2Service higher-order construct):
  - An EC2-backed ECS cluster. Make sure that the EC2 instance(s) have the [ecsInstanceRole](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/instance_IAM_role.html) role attached to them, such that they can pull images from ECR.
  - The cluster must have at least 1 ECS-optimized Amazon Linux2 EC2 instance beneath it.
  - A task definition that defines the container, image tag, environment variables, the port (8080) that is exposed and the resources needed by the container (at least 0.5 vCPU and 768 RAM).
  - Store the DB password in a SSM Parameter or Secret Manager Secret (trial is just 30 days) and reference it inside the task definition (instead of hardcoding the password). 
  - An ECS service that uses this task definition inside your ECS cluster.
- Register the ECS service as the target for the existing ALB.
- Open port 8080 on the load balancer's hostname and ensure that the application still works.

Deploy the IaC via CI/CD
- Manually adjust the OnlineShopCiCdPipeline IAM role to:
  - *CDK*: Be able to assume the `cdk*` roles created during bootstrap (see https://stackoverflow.com/a/61102280).
  - *Terraform*: Have the `AdminAccess` policy attached to it.
- Adjust the CI/CD pipeline to deploy the IAC as well (i.e. terraform apply or cdk deploy), after pushing the image to ECR.
- Adjust the CI/CD to pass the value of the parameter for the image tag to the latest commit hash.
- Open port 8080 on the load balancer's hostname and ensure that the application still works.

## Further Resources

- [Gentle Introduction to How AWS ECS Works with Example Tutorial | by Tung Nguyen | BoltOps | Medium](https://medium.com/boltops/gentle-introduction-to-how-aws-ecs-works-with-example-tutorial-cea3d27ce63d)
- [Deploying an AWS ECS Cluster of EC2 Instances With Terraform](https://medium.com/swlh/creating-an-aws-ecs-cluster-of-ec2-instances-with-terraform-85a10b5cfbe3)
- [ECS with EC2 + ALB example for AWS CDK](https://github.com/aws-samples/aws-cdk-examples/tree/master/typescript/ecs/ecs-service-with-advanced-alb-config)
- [How can I pass secrets or sensitive information securely to containers in an Amazon ECS task?](https://aws.amazon.com/premiumsupport/knowledge-center/ecs-data-security-container-task/)