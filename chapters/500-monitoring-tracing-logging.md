# Logging, tracing, metrics

Goal: Instrument the backend to easily obtain insights about the application health and usage patterns.

## Required Reading

- [Working with log groups and log streams - Amazon CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/Working-with-log-groups-and-streams.html#ViewingLogData)
- [Using the awslogs log driver - Amazon Elastic Container Service](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/using_awslogs.html)
- [Using Amazon CloudWatch metrics - Amazon CloudWatch](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/working_with_metrics.html)
- [Manual Instrumentation for Traces and Metrics with the Java SDK](https://aws-otel.github.io/docs/getting-started/java-sdk/trace-manual-instr#creating-metrics)
- [What is AWS X-Ray? - AWS X-Ray (amazon.com)](https://docs.aws.amazon.com/xray/latest/devguide/aws-xray.html)
- [Integrating AWS X-Ray with other AWS services - AWS X-Ray (amazon.com)](https://docs.aws.amazon.com/xray/latest/devguide/xray-services.html)

## Online Shop

Logging to CloudWatch
- Adjust the IaC stack to use the aws-logs logging driver for the ECS task of the backend. 
- Deploy and check that the backend logs are now visible in CloudWatch logs.

Creating a dashboard
- Add new constructs to the IaC stack to automatically create a CloudWatch dashboard with the following metrics (Hint: You can first create a small part of the dashboard by hand in the AWS Console, then go to Actions --> View/Edit source to get an idea of how the widgets should be configured via IaC):
  - SUM ApplicationELB --> Per AppELB, per TG Metrics --> RequestCount
  - P99 ApplicationELB --> Per AppELB, per TG Metrics --> TargetResponseTime
  - SUM ApplicationELB --> Per AppELB, per TG Metrics --> HTTPCode_Target_4XX_Count
  - SUM ApplicationELB --> Per AppELB, per TG Metrics --> HTTPCode_Target_5XX_Count
  - SUM SQS --> Queue Metrics --> NumberOfMessagesSent
  - MAX SQS --> Queue Metrics --> ApproximateAgeOfOldestMessage
  - AVG ECS --> ClusterName, ServiceName --> CPUUtilization
  - AVG ECS --> ClusterName, ServiceName --> MemoryUtilization
  - SUM Lambda --> By Function Name --> Invocations
  - SUM Lambda --> By Function Name --> Errors
  - P99 Lambda --> By Function Name --> Duration
  - AVG RDS --> Per-Database Metrics --> CPUUtilization
  - SUM CloudFront --> Per-Distribution Metrics --> Requests
  - AVG CloudFront --> Per-Distribution Metrics --> TotalErrorRate

Raising alarms
- Add an SNS topic in the IaC stack for receiving alarms.
- Manually subscribe your email address to this SNS topic.
- Add an alarm that triggers whenever there SUM Lambda --> By Function Name --> Errors > 0. 
- Temporarily "break" your Lambda's code to always throw an error.
- Deploy, login to the app and place an order.
- Check that you got an alarm via email. 

Starting the Open Telemetry Collector sidecar
- Adjust the task definition for the backend to include an extra container, running the Open Telemetry Collector
- Adjust the task role permissions to allow creating log groups, pushing logs, pushing metrics and creating traces.

Publishing custom metrics
- Configure AWS Distro for OpenTelemetry in the backend and publish a new custom metric counter each time a new order is created. 
- Make sure to grant the backend the right to push metric data ("cloudwatch:PutMetricData"). 
- Add this new custom metric to the dashboard above. Deploy, login to the app and place an order. Check that the custom metric is populated.

Recording traces
- Lastly, enable Open Telemetry tracing for both the Lambda and the ECS service and ensure that the trace header is propagated via SQS. 
- Deploy, login to the app and place an order. Check that the traces in X-Ray are populated.

## Further Resources

- [Working with Java - AWS X-Ray (amazon.com)](https://docs.aws.amazon.com/xray/latest/devguide/xray-java.html)
- [Amazon CloudWatch Embedded Metric Format Client Library](https://github.com/awslabs/aws-embedded-metrics-java)
- [Documentation | OpenTelemetry](https://opentelemetry.io/docs/)
- [Setting up Amazon SNS notifications](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/US_SetupSNS.html)
- [Create a CloudWatch alarm based on a static threshold](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/ConsoleAlarms.html)