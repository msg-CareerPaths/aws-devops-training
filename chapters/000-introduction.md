# Introduction

The road-map consists of several steps. In each step, a set of theoretical concepts are explored, supported by reference documentation, book chapters, tutorials and videos. In parallel, a simple existing application will be deployed to AWS and enhanced with the learned concepts: the *Online Shop* application.

Note that this training assumes some basic Linux and general programming knowledge. The existing application is built with Spring and ReactJS, you might want to skim through the [Spring](https://github.com/msg-CareerPaths/spring-training) and [React](https://github.com/msg-CareerPaths/react-training) trainings before attempting this training.

Target audience: Devs that want to get into the Ops side of things as well. Note that DevOps requires a specific paradigm/culture inside your project/team/company. This training merely aims to aid developers in getting practical know-how to enable DevOps practices on AWS. The "story" of the training is that "we built this app, now re-platform, run, enhance and operate it on AWS". 

The training also assumes a certain degree of independence - i.e. you'll need to perform some research and googling on your own to get some things right. 

## Required Reading
 - [What is DevOps? - Amazon Web Services (AWS)](https://aws.amazon.com/devops/what-is-devops/)
 - [Getting Started with AWS Cloud Essentials (amazon.com)](https://aws.amazon.com/getting-started/cloud-essentials/)
 - [Global Infrastructure Overview (amazon.com)](https://aws.amazon.com/about-aws/global-infrastructure/)
 - [Global Infrastructure Regions & AZs (amazon.com)](https://aws.amazon.com/about-aws/global-infrastructure/regions_az/)

## Online Shop

You first need to get familiar with the application that we will deploy on AWS:

- [Register a new free AWS account](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/get-set-up-for-amazon-ec2.html#sign-up-for-aws). You can also use an existing account or a company-provided one.
- Use the "us-east-1" region.
- Later in the training, when you're launching a EC2 instance, RDS database or Elasticache cluster, first check if the instance type you're choosing is included in the Free Tier:
  - [EC2](https://aws.amazon.com/free/?all-free-tier.sort-by=item.additionalFields.SortRank&all-free-tier.sort-order=asc&awsf.Free%20Tier%20Types=*all&awsf.Free%20Tier%20Categories=*all&all-free-tier.q=ec2&all-free-tier.q_operator=AND) - currently t2.micro or t3.micro (if t2 is not available).
  - [RDS](https://aws.amazon.com/free/?all-free-tier.sort-by=item.additionalFields.SortRank&all-free-tier.sort-order=asc&awsf.Free%20Tier%20Types=*all&awsf.Free%20Tier%20Categories=*all&all-free-tier.q=rds&all-free-tier.q_operator=AND) - currently db.t2.micro, db.t3.micro
  - [ElastiCache](https://aws.amazon.com/free/?all-free-tier.sort-by=item.additionalFields.SortRank&all-free-tier.sort-order=asc&awsf.Free%20Tier%20Types=*all&awsf.Free%20Tier%20Categories=*all&all-free-tier.q=elasticache&all-free-tier.q_operator=AND) - currently cache.t2micro, cache.t3.micro
- Install Java 11 or newer, node 14 or newer and docker on your local machine.
- Fork the repository [msg-CareerPaths/aws-devops-demo-app (github.com)](https://github.com/msg-CareerPaths/aws-devops-demo-app) into a personal copy for yourself. It contains the application that we will move to AWS.
- Go to [releases](https://github.com/msg-CareerPaths/aws-devops-demo-app/releases) --> Download the JAR file for the latest release.
- Start a local postgres database as shown in the repo's readme.
- Start the application locally as shown in the repo's readme and open its URL in your browser.
  - Login with the following credentials: `user` / `password`
  - Select some products.
  - Place an order.
  - Refresh the page and check that the order is still displayed.