# Docker basics & usage

Goal: Containerize the application such that its runtime environment is consistent across machines (i.e. same Java version, file system contents, etc).

## Required Reading

- [Getting started with Docker](https://docs.docker.com/get-started/)
- [Get started with Docker Compose | Docker Documentation](https://docs.docker.com/compose/gettingstarted/)
- [Topical Guide | Spring Boot Docker](https://spring.io/guides/topicals/spring-boot-docker/)
- [What is Amazon Elastic Container Registry? - Amazon ECR](https://docs.aws.amazon.com/AmazonECR/latest/userguide/what-is-ecr.html)

## Online Shop

![Application Diagram](./diagrams/300.drawio.svg)

Wrapping the app in a Docker image:

- Create a Dockerfile that can build an image for running the backend:
  - First just create a Docker image that copies the JAR from the outside,
  - The adjust it to build the Jar inside the Dockerfile,
  - Lastly, use a multi-stage Dockerfile to keep the final image as small as possible (and e.g. not include maven).
- Adjust the IaC stack to create a new Elastic Container Repository to store your image and [push your image there](https://docs.aws.amazon.com/AmazonECR/latest/userguide/docker-push-ecr-image.html). Allow Docker tags to be mutated in this repository.

Running the app locally with Docker compose:

- Create a Docker compose file that includes the following services:
  - The backend, which is built locally using the Dockerfile generated above,
  - A Postgres database, which uses the publicly available [postgres](https://hub.docker.com/_/postgres) Docker image,
  - A Redis cache, which uses the publicly available [redis](https://hub.docker.com/_/redis) Docker image.
- Connect the backend to the database and cache using the appropriate network links and environment variables. Adjust the backend Docker image to include the [docker-compose-wait](https://github.com/ufoscout/docker-compose-wait) script and  wait for the database and cache to be online before starting the backend JAR.
- Spin up the docker compose and check that the application is running normally.

Creating and publishing the image via a GitHub workflow:

- Build a GitHub Actions workflow from your fork of the repository to also perform a Docker image build after building the JAR.
- Manually a new "OnlineShopCiCdPipeline" IAM role that is allowed to push images to ECR. 
- Establish a [trust relationship that allows GitHub actions to assume this role](https://github.com/aws-actions/configure-aws-credentials#sample-iam-role-cloudformation-template). 
- Adjust the GitHub workflow to assume this role via the configure-aws-credentials action and the publish the image to ECR with the tag equal to the commit hash.

Using the docker image on the EC2 servers:

- Add a new input to the IaC for the docker tag to use.
- Allow the EC2 instance to pull from the ECR repository via an IAM role attached to the instances.
- Adjust the "user data" from the auto-scaling group launch template to automatically perform the following when EC2 instances are spawned:
  - No longer install java and the JAR.
  - Install docker.
  - Login to ECR via the AWS CLI. 
  - Start a container on docker for our application; use the "host" networking mode.

## Further Resources

- [Introduction to Containers and Docker | endjin](https://endjin.com/blog/2022/01/introduction-to-containers-and-docker)
- [Run a Simple .jar Application in a Docker Container - DZone DevOps](https://dzone.com/articles/run-simple-jar-application-in-docker-container-1)
- [A Docker Tutorial for Beginners (docker-curriculum.com)](https://docker-curriculum.com/)
- [How Amazon Elastic Container Registry works with IAM](https://docs.aws.amazon.com/AmazonECR/latest/userguide/security_iam_service-with-iam.html#security_iam_service-with-iam-id-based-policies)
- [Using Amazon ECR with the AWS CLI](https://docs.aws.amazon.com/AmazonECR/latest/userguide/getting-started-cli.html)
- [Attach IAM role to Amazon EC2 instance using Terraform](https://skundunotes.com/2021/11/16/attach-iam-role-to-aws-ec2-instance-using-terraform/) / [Attach IAM role to Amazon EC2 instance using CDK](https://bobbyhadz.com/blog/aws-cdk-ec2-instance-example)
- [GitHub Actions: Using OpenID Connect (OIDC) to deploy securely to AWS](https://www.youtube.com/watch?v=k2Tv-EJl7V4&t=185s)
