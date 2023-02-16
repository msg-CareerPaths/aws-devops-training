# Autoscaling and load balancing

Goal: Make the application self-heal (i.e. automatically replace crashed servers) and able to respond to changes in load.

## Required Reading

- [What is Amazon EC2 Auto Scaling? - Amazon EC2 Auto Scaling](https://docs.aws.amazon.com/autoscaling/ec2/userguide/what-is-amazon-ec2-auto-scaling.html)
- [Launch templates - Amazon EC2 Auto Scaling](https://docs.aws.amazon.com/autoscaling/ec2/userguide/launch-templates.html)
- [What is an Application Load Balancer? - Elastic Load Balancing (amazon.com)](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/introduction.html)
- [Target tracking scaling policies](https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-scaling-target-tracking.html)

## Online Shop

Replace the static backend with an ASG:

- Create a new AMI from your latest backend instance, stop the instance and release the Elastic IP. 
- Use this AMI to define [a new launch template](https://docs.aws.amazon.com/autoscaling/ec2/userguide/create-launch-template.html) mirroring the instance settings (same instance type, storage, etc). 
- Now use this launch template to [create a new autoscaling group](https://docs.aws.amazon.com/autoscaling/ec2/userguide/create-asg-launch-template.html):
  - Deploy it in our VPC's public subnets, using the same security group as we used for the backend before.
  - Configure group size and scaling policies: Max capacity = 3, Desired = 1, Min capacity = 1
- [Create a new load balancer in front of the auto-scaling group](https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-create-load-balancer-console.html):
  - Load balancer scheme: public internet-facing load balancer
  - Availability Zones and subnets: all the public subnets
  - Listeners and routing: update the port number to 8080.
  - Health check: check that the /health endpoint of the app returns a 200 status code.
  - Ensure that the load balancer security group can "talk" to the backend via TCP port 8080.
- Open port 8080 on the load balancer's hostname and ensure that the application still works.

Test the application resilience:
- Update the auto-scaling group to increase the desired count to 2. 
- Check that a new EC2 instance is started by the auto-scaling group (can take a minute or so) and that it is eventually registered as a healthy target in the load balancer. 
- Open port 8080 on the load balancer's hostname and ensure that the application still works.
- Open the EC2 console and terminate one of the two running instances. 
- Open port 8080 on the load balancer's hostname and ensure that the application still works. 
- Notice that the auto-scaling group has also automatically launched a replacement instance. 
- Update the auto-scaling group to decrease the desired count back to 1.
- Attach a CPU-based target tracking policy to your auto-scaling group. This would automatically scale your instances appropriately based on the load on the instances. (Generating artificial load to test this is not part of this training, as it would like generate costs; but if you want to try it anyway, you can use tools such as Gatling, JMeter or Artillery to simulate users).

Updating the application version via AMI:

- Update the JAR running in our application by:
  - SSH-ing into one of the instances,
  - Stopping the backend service,
  - Downloading the new JAR into the instance and replacing the old JAR,
  - Start the service again,
  - Creating a new AMI from the instance,
  - Creating a new launch template version to use the new AMI
  - Updating the auto-scaling group to use the new launch template.

Updating the application version via user data:
- The above procedure is painful. A better version is to use [user data](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/user-data.html). Create a new launch template from the our latest template, but adjust it:
  - Replace the AMI with the regular Amazon Linux AMI.
  - Adjust the user-data section to include a small bash script that does the Java and application installation (similar to the steps done in chapter IaaS.1, but without installing PostgreSQL locally).
  - Update the ASG to use this launch template and ensure that the application still works.

## Further Resources

- [AWS Elastic Load Balancing Introduction](https://www.youtube.com/watch?v=qpHLRc4Qt1E)
- [Amazon EC2 Auto Scaling benefits - Amazon EC2 Auto Scaling](https://docs.aws.amazon.com/autoscaling/ec2/userguide/auto-scaling-benefits.html)
