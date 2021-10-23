How AWS Cloudwatch is divided in? (4)

---

-  Metrics: Collect and track key metrics
-  Logs: Collect, monitor, analyze and store log files
-  Events: Send notifications when certain events happen in your AWS
-  Alarms: React in real-time to metrics / events

===

In AWS Cloudwatch, what's a Dimension?

---

A dimension is a name/value pair that is part of the identity of a metric. You can assign up to 10 dimensions to a metric. Because dimensions are part of the unique identifier for a metric, whenever you add a unique name/value pair to one of your metrics, you are creating a new variation of that metric.

===

What do we monitor with Cloudwatch metrics? (2)

---

-  Metrics are data about the performance of your systems (CPUUtilization, NetworkIn).
-  By default, many services provide free metrics for resources (such as Amazon EC2 instances, Amazon EBS volumes, and Amazon RDS DB instances) but you can also publish your own custom application metrics

===

What's EC2 Detailed monitoring?

---

By default, your instance is enabled for basic monitoring (each 5 minutes). You can optionally enable detailed monitoring. After you enable detailed monitoring, the Amazon EC2 console displays monitoring graphs with a 1-minute period for the instance (with a major cost).

===

A good use case for using EC2 detailed monitoring

---

Use detailed monitoring if you want to scale faster for your ASG!

===

If we wanted to push a custom metric what API call should we use? (AWS Cloudwatch)

---

Use API call PutMetricData

===

How is the Logs storage architecture like? (AWS Cloudwatch) (2)

---

-  Log groups: arbitrary name, usually representing an application
-  Log stream: instances within application / log files / containers

===

If we wanted our EC2 instance to send logs to AWS Cloudwatch, what should we do? (IDH) (2)

---

-  By default, no logs from your EC2 machine will go to CloudWatch. You need to run a CloudWatch
   agent on EC2 (or on-premise machines) to push the log files you want
-  You need to make sure that IAM permissions are correct

===

What are the differences between the Cloudwatch Logs agent and the Unified Agent? (3)

---

-  CloudWatch Logs Agent is the old version of the agent that only sends logs to Cloudwatch
-  CloudWatch Unified agent is the new version and can collect additional system-level metrics such as RAM, processes, etc.
-  With the unified agent the configuration is centralized using SSM Parament store

===

What's the major difference between the Cloudwatch unified agent and Cloudwatch EC2 metrics? (2)

---

-  EC2 metrics are high level (CPU, network, etc)
-  Metrics collected by the unified agent are more granular and they can range from CPU (active, guest, idle etc) to Swap Space (free space, used) as well as others.

===

If we wanted to filter some data using Cloudwatch logs, what should we use? (2)

---

-  You need to create a Metric filter. Metric filters define the terms and patterns to look for in log data as it is sent to CloudWatch Logs.
-  CloudWatch Logs uses these metric filters to turn log data into numerical CloudWatch metrics that you can graph or set an alarm on.

===

What does Cloudwatch use to trigger notifications?

---

Alarms are used to trigger notifications for any metric

===

What are some Alarm states in Cloudwatch? (3)

---

-  OK
-  INSUFFICIENT_DATA
-  ALARM

===

What's "Period" in Cloudwatch alarms?

---

It is the length of time to evaluate the metric or expression to create each individual data point for an alarm. It is expressed in seconds. If you choose one minute as the period, the alarm evaluates the metric once per minute.

===

Explain CloudWatch Alarm Targets (3)

---

-  Stop, Terminate, Reboot, or Recover an EC2 Instance
-  Trigger Auto Scaling Action
-  Send notification to SNS (from which you can do pretty much anything)

===

How can you use Cloudwatch alarms in case an EC2 instance suffers some hardware failure? (2)

---

-  You can create an Amazon CloudWatch alarm that monitors an Amazon EC2 instance and automatically recovers the instance if it becomes impaired due to an underlying hardware failure or a problem that requires AWS involvement to repair
-  A recovered instance is identical to the original instance, including the instance ID, private IP addresses, Elastic IP addresses, and all instance metadata. If the impaired instance has a public IPv4 address, the instance retains the public IPv4 address after recovery. If the impaired instance is in a placement group, the recovered instance runs in the placement group.

![image](https://user-images.githubusercontent.com/1868409/136862856-ec63ac6f-a7a1-4a95-ad7c-1d89780bc3c1.png)

===

If we wanted to test a Cloudwatch alarm without having to force the condition that generates such a alarm, what should we do?

---

We can use a command like this;

```sh
aws cloudwatch set-alarm-state --alarm-name "myalarm" --state-value
ALARM --state-reason "testing purposes"
```

===

What cloudwatch feature can we use to notify changes in our AWS services?

---

AWS CloudWatch Events. Example: EC2 Instance Start, CodeBuild Failure, S3 new object, etc

===

Type of Targets for Cloudwatch events (4)

---

-  Compute: Lambda, Batch, ECS task
-  Integration: SQS, SNS, Kinesis Data Streams, Kinesis Data Firehose
-  Orchestration: Step Functions, CodePipeline, CodeBuild
-  Maintenance: SSM, EC2 Actions

===

How is Cloudwatch extended with EventBridge?

---

-  In Cloudwatch you only have a default event bus generated by AWS services
-  But in Event Bridge you can have additional buses:
   -  Partner event bus: receive events from SaaS service or applications
      (Zendesk, DataDog, Segment, Auth0…)
   -  Custom Event buses: for your own applications

===

How schema registries work with EventBridge?

---

EventBridge can analyze the events in your bus and infer the schema. This schemas can be versioned as well

===

So are EventBridge and CloudWatch Events the same thing?

---

In some way, yes. They both build upon the same infraestructure. At this moment, Event Bridge extends capabilities from Cloudwatch events but eventually both services will merge to become just one.

===

What's the purpose of AWS X-Ray?

---

X-Ray helps you trace requests from your application to several supported AWS Resources. For any traced request to your application, you can see detailed information not only about the request and response, but also about calls that your application makes to downstream AWS resources, microservices, databases, and web APIs.

===

Some AWS X Ray advantages (3)

---

-  Troubleshooting performance by identifying bottlenecks
-  Understand dependencies in a microservice architecture
-  Identify other service issues

===

Service compatible with X-Ray (6)

---

-  AWS Lambda
-  Elastic Beanstalk
-  ECS
-  ELB
-  API Gateway
-  EC2 Instances or any application server (even on premise)

===

Steps for enabling AWS X-Ray (2) (IDH)

---

1. First, your code (Java, Python, Go, Node.js, .NET) must import the AWS
   X-Ray SDK. The application SDK will then capture: Calls to AWS services, HTTP / HTTPS requests, Database Calls (MySQL, PostgreSQL, DynamoDB), Queue calls (SQS)
2. Then you need to install the X-Ray daemon or enable X-Ray AWS Integration (already included in
   Lambda). This daemon works as a low level UDP packet interceptor. Bear in mind Each application must have the IAM rights to write data to X-Ray.

===

Some common problem if X-Ray is not working in EC2 (2)

---

-  Ensure the EC2 IAM Role has the proper permissions
-  Ensure the EC2 instance is running the X-Ray Daemon

===

Some common problem if X-Ray is not working in Lambda (2)

---

-  Ensure it has an IAM execution role with proper policy (AWSX-RayWriteOnlyAccess)
-  Ensure that X-Ray is imported in the code

===

How to enable X-Ray in Elastic Beanstalk

---

You need to create a configuration file named: xray-daemon.config inside of the .ebextensions directory

![image](https://user-images.githubusercontent.com/1868409/137417832-42c43904-8ffb-4ae7-b953-f83732503b0d.png)

===

X-Ray Concepts (6)

---

-  Segments: each application / service will send them
-  Subsegments: if you need more details in your segment
-  Trace: segments collected together to form an end-to-end trace
-  Sampling: decrease the amount of requests sent to X-Ray, reduce cost
-  Annotations: Key Value pairs used to index traces and use with filters
-  Metadata: Key Value pairs, not indexed, not used for searching

===

Explain defaul sampling rules in X-Ray. (Reservoir and Rate) (4) (IDH)

---

-  By default, the X-Ray SDK records the first request each second, and
   five percent of any additional requests.
-  One request per second is the reservoir, which ensures that at least one
   trace is recorded each second as long the service is serving requests.
-  Five percent is the rate at which additional requests beyond the
   reservoir size are sampled.
-  You can change the default behavior and configure your own sampling rules (ID)

![image](https://user-images.githubusercontent.com/1868409/137420541-c2feb00a-efc4-4a3a-a928-97f57dd24137.png)

===

If we had a service that's presenting issues, what would we do if we wanted to debug such a service in-depth using AWS X-Ray?

---

We could create a custom Sample rule with appropriate reservor and fixed rate values for such a service. For example, we can set the fixed rate to 100% so that we don't miss anything.

===

What X-Ray API call uploads segment documents to AWS?

---

PutTraceSegments

===

What API call is used by the AWS X-Ray to upload telemetry?

---

PutTelemetryRecords

===

What API call is used by the AWS X-Ray to retrieve all sampling rules?

---

GetSamplingRules

===

Important X-Ray API calls for reading (4)

---

-  GetServiceGraph: main graph
-  BatchGetTraces: Retrieves a list of traces specified by ID. Each trace is a collection of segment documents that originates from a single request.
-  GetTraceSummaries: Retrieves IDs and annotations for traces available for a specified time frame using an optional filter. To get the full traces, pass the trace IDs to BatchGetTraces.
-  GetTraceGraph: Retrieves a service graph for one or more specific trace IDs.

===

How to enable X-Ray in Elastic Beanstalk? (2 options)

---

You can run the daemon by setting an option in the Elastic Beanstalk console
or with a configuration file (in .ebextensions/xray-daemon.config)

===

What options do we have for integrating ECS containers (classic and Fargate) with X-Ray? (3)

---

-  For ECS classic (Cluster) you have two options:
   -  X-Ray Daemon container: where you launch X-Ray as a separate task service that multiple containers (inside the same EC2 instance) can communicate through the corresponding port.
   -  X-Ray Container as a “Side Car”: where the X-Ray container is launch alongside the target App container as part of one single service. In this case, you will need one X-ray container per App being monitored.
-  For ECS Fargate (serverless): you only have the option to launch X-Ray as a side car

===

What is the purpose of AWS CloudTrail?

---

AWS CloudTrail is an AWS service that helps you enable governance, compliance, and operational and risk auditing of your AWS account. Actions taken by a user, role, or an AWS service are recorded as events in CloudTrail. Events include actions taken in the AWS Management Console, AWS Command Line Interface, and AWS SDKs and APIs.

===

Explain CloudTrail Events (3)

---

-  Management Events: Operations that are performed on resources in your AWS account. They are enabled by default.
-  Data Events: provide visibility into the resource operations performed on or within a resource. Example: Amazon S3 object-level activity (ex: GetObject, DeleteObject, PutObject). They are disabled by default
-  CloudTrail Insights Events: helps you detect unusual activity in your account. Example: hitting service limits.

===

How is the process of CloudTrail insights when detecting unusual patterns? (3)

---

-  Anomalies appear in the CloudTrail console
-  Event is sent to Amazon S3
-  An EventBridge event is generated (for automation needs)

![image](https://user-images.githubusercontent.com/1868409/137593423-2204d3ce-1dc8-4e4c-a1c2-26705541302c.png)

===

What's the retention period of AWS CloudTrail and how can it be extended?

---

-  Events are stored for 90 days in CloudTrail
-  To keep events beyond this period, log them to S3 and use Athena

===

So in summary, when do we use CloudTrail, Cloudwatch (3), X-Ray?

---

CloudTrail:

-  Audit API calls made by users / services / AWS console. This is useful to detect unauthorized calls or root cause of changes

CloudWatch:

-  CloudWatch Metrics over time for monitoring
-  CloudWatch Logs for storing application log
-  CloudWatch Alarms to send notifications in case of unexpected metrics

X-Ray:

-  Use it for automated Trace Analysis & Central Service Map Visualization. This can help you identify and analyze latency issues and errors.

===

High-Resolution Custom Metrics can have a minimum resolution of ...

---

1 second

===

You have an application hosted on a fleet of EC2 instances managed by an Auto Scaling Group that you configured its minimum capacity to 2. Also, you have created a CloudWatch Alarm that is configured to scale in your ASG when CPU Utilization is below 60%. Currently, your application runs on 2 EC2 instances and has low traffic and the CloudWatch Alarm is in the ALARM state. What will happen?

---

The CloudWatch Alarm will remain in ALARM state but never decrease the number of EC2 instances in the ASG. The number of EC2 instances in an ASG can not go below the minimum capacity, even if the CloudWatch alarm would in theory trigger an EC2 instance termination.

===

How would you monitor your EC2 instance memory usage in CloudWatch?

---

Use Unified CloudWatch Agent to push memory usage as a custom metric to CloudWatch

===

A CloudWatch Alarm set on a High-Resolution Custom Metric can be triggered as often as.

---

If you set an alarm on a high-resolution metric, you can specify a high-resolution alarm with a period of **10 seconds** or 30 seconds, or you can set a regular alarm with a period of any multiple of 60 seconds.

===

What should you do to configure X-Ray Daemon to send traces from multiple AWS accounts to a central AWS account?

---

Create an IAM role in the central account, then create IAM roles in the other accounts to assume this IAM role
