# (Optional) Serverless containers

Goal: get rid of the EC2 infrastructure completely, host the app of a "serverless" container service.

**Note that this chapter will incur some minor costs, as AWS Fargate is NOT part of the free tier. Revert the IaC to the previous chapter's state and deploy once you're done to not incur unnecessary costs.**

## Required Reading
- [What is AWS Fargate](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/AWS_Fargate.html)

## Online Shop
Migrate to fargate
- Adjust the task definition IaC to specify a [taskExecutionRole](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_execution_IAM_role.html) that has at least the permissions to pull the docker images from ECR. 
- Adjust the task definition IaC to use the awsvpc network mode.
- Adjust the task definition IaC to allow the FARGATE launch type.
- Remove the EC2 auto-scaling group from the ECS cluster.
- Deploy. Check that the application still works.

## Further Resources
 - [Migrating Your Amazon ECS Containers to AWS Fargate](https://aws.amazon.com/blogs/compute/migrating-your-amazon-ecs-containers-to-aws-fargate/)