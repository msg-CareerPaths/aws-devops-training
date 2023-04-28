# Running apps on "bare" servers

Goal: Run our application on cloud infrastructure, using basic, bare-bones Linux servers.

## Required Reading

- [Introduction to Amazon EC2](https://www.youtube.com/watch?v=KpVNEzpvaY0)
- [Get started with Amazon VPC - Amazon Virtual Private Cloud](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-getting-started.html)

## Online Shop

![Application Diagram](https://raw.githubusercontent.com/msg-CareerPaths/aws-devops-training/master/chapters/diagrams/100.drawio.svg)

Setting up the network:

 - [Create a new VPC](https://docs.aws.amazon.com/vpc/latest/userguide/working-with-vpcs.html#create-vpc-and-other-resources) with a 10.0.0.0/16 CIDR, then create 2 "public" subnets (10.0.1.0/24 & 10.0.2.0/24) and 2 "private" subnets (10.0.3.0/24 & 10.0.4.0/24). 
 - [Launch an internet gateway](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html#Add_IGW_Attach_Gateway) in the VPC 
- and [configure the public subnets to use it](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html#Add_IGW_Routing). Do not launch any NAT Gateways or VPC Endpoints.

Setting up the backend:

 - [Create a new "online-shop-backend" security group](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/working-with-security-groups.html#creating-security-group) that allows SSH traffic from your IP inside your VPC. 
 - [Create a new SSH key pair](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/get-set-up-for-amazon-ec2.html#create-a-key-pair) and [launch a new E2 instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EC2_GetStarted.html#ec2-launch-instance) using that key pair:
  - Use the Amazon Linux 2 AMI,
  - Use a [free tier eligible](https://aws.amazon.com/free/?all-free-tier.sort-by=item.additionalFields.SortRank&all-free-tier.sort-order=asc&awsf.Free%20Tier%20Types=*all&awsf.Free%20Tier%20Categories=*all&all-free-tier.q=ec2&all-free-tier.q_operator=AND) type (currently t2.micro),
  - Enable "Auto-assign public IP",
  - Place it inside one of the public subnets of the VPC you created above,
  - Use the  "online-shop-backend" security group.

Installing the application:

- Once the instance is launched, [connect to it via SSH](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EC2_GetStarted.html#ec2-connect-to-instance-linux).
- Perform the following (write down the bash commands that you used; you'll need them later):
  - [Install Java 11](https://docs.aws.amazon.com/corretto/latest/corretto-11-ug/amazon-linux-install.html).
  - [Install PostgreSQL 10](http://howto.philippkeller.com/2022/05/03/How-to-install-postgres-on-amazon-linux-2/) or newer, set a password and ensure you can connect via username and password to the database.
  - Download the JAR of our application locally (e.g. using wget).
  - [Set up a systemd entry](https://computingforgeeks.com/how-to-run-java-jar-application-with-systemd-on-linux/) such that the JAR is automatically started when the instance is rebooted. [Configure in this systemd file](https://serverfault.com/a/413408) the following environment variables:

```
SPRING_DATASOURCE_USERNAME=postgres
SPRING_DATASOURCE_PASSWORD=whatever value you used
SPRING_DATASOURCE_URL=jdbc:postgresql://localhost:5432/postgres
```

Testing the application:

 - Modify the security group of your instance to allow any TPC traffic over port 8080. 
 - Verify that opening this port shows the application login screen, logging in works and creating a new order also works. 
 - Reboot the EC2 instance and ensure that the application still works.
 - Allocate a new [Elastic IP address and attach it to your EC2 instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/elastic-ip-addresses-eip.html). 
 - Check that the application is now accessible via the Elastic IP address.

Replace the backend instance:

 - Create a new AMI out of your instance. 
 - Now login back to the application and create a new order. 
 - Write down the URL of the order.
 - Spin up a new instance out of this AMI (same settings as before, just different AMI). 
 - Move the Elastic IP to this new instance by disassociating it from the old instance and immediately associating it to the new instance.
 - Shut down the old instance. 
 - Check that the application is still accessible through the Elastic IP. Notice that the application user got signed out and the order that you created previously does not exist anymore.

## Further Resources

- [Tutorial: Get started with Amazon EC2 Linux instances - Amazon Elastic Compute Cloud](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EC2_GetStarted.html)
- [AWS Skill Builder: EC2 Basics](https://explore.skillbuilder.aws/learn/course/external/view/elearning/12471/amazon-ec2-basics)
- [How to set environment variables in systemd](https://serverfault.com/questions/413397/how-to-set-environment-variable-in-systemd-service/413408#413408)
- [Downloading a JAR from GitHub](https://stackoverflow.com/questions/62014168/downloading-jar-file-from-github)
- [Troubleshooting authentication errors with Postgres](https://stackoverflow.com/questions/2942485/psql-fatal-ident-authentication-failed-for-user-postgres)
- [Listing databases in PostgreSQL using psql command](https://www.postgresqltutorial.com/postgresql-administration/postgresql-show-databases/)

Useful commands:
- Viewing logs of a systemd service: `journalctl _SYSTEMD_UNIT=myapp.service`
- Reload systemd configurations: `sudo systemctl daemon-reload`
- Checking the status of a systemd service: `sudo systemctl status myapp.service`

