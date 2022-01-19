What's a requirement to run a Lambda Container Image? What's the recommended option?

---

The container image must implement the Lambda Runtime API, but ECS / Fargate is preferred for running arbitrary Docker images.

===

Explain how can we use Lambda for creating thumbnails of images

---

We can have the source imaged to be stored in S3 and generate an event when that happens. This event can trigger a Lambda function that will contain code to generate the corresponding thumbnail. Then we can just store that thumbnail into another (or the same) bucket in S3 and additionally persist some metadata in DynamoDB

![image](https://user-images.githubusercontent.com/1868409/148535282-57823949-daca-440b-9262-3a09f9687f5c.png)

===

Explain how can we use Lambda for CRON jobs

---

We can configure our CRON job using Cloudwatch EventBridge. A lambda function can listen to this event and execute the corresponding job task.

![image](https://user-images.githubusercontent.com/1868409/148535722-a4e09f54-3c8d-4f40-b8b5-c03d17092267.png)

===

Explain pricing with AWS lambda (for calls and duration)

---

-  For calls: first 1,000,000 requests are free then you pay \$0.20 per 1 million requests thereafter
-  For duration: 400,000 GB/seconds of compute time per month for free. This is equals to 400,000 seconds if function is 1GB RAM. Then you pay \$1.00 for 600,000 GB-seconds.

===

In AWS Lambda synchronous invocations, which side is responsible of the error handling?

---

Error handling must happen client side (retries, exponential backoff, etc…)

===

Synchronous Invocations Services in Lambda (2 groups)

---

-  User Invoked:
   -  ELB
   -  Amazon API Gateway
   -  Amazon CloudFront (Lambda@Edge)
   -  Amazon S3 Batch
-  Service Invoked:
   -  Amazon Cognito
   -  AWS Step Functions

===

How to integrate Lambda functions with ELB (ALB)?

---

To expose a Lambda function as an HTTP(S) endpoint through an ALB, the Lambda function must be registered in a target group

===

How is the information handled from an ALB to a Lambda function and vice-versa?

---

From an ALB to Lambda: The HTTP request is transformed into a JSON object containing all relevant data (headers, querystring, patch, method, etc)

![image](https://user-images.githubusercontent.com/1868409/148699990-a9e2ee2a-4eb6-449a-a31e-2e6dff32e412.png)

From Lambda back to ALB: response is transformed into a JSON object containing the HTTP response data (payload, response headers, status codes, etc)

![image](https://user-images.githubusercontent.com/1868409/148700061-b0c07da3-a1d0-4252-a846-aca117b28b29.png)

===

How does AWS Lambda handle Multi-header values? (multiple values for one single header or query string key)

---

When you enable multi-value headers, HTTP headers and query string parameters that are sent with multiple values are shown as arrays within the AWS Lambda event and response objects

===

You have deployed a CDN using CloudFront, What if you wanted to run a global AWS Lambda alongside? Or how to implement request filtering before reaching your application?

---

For this, you can use Lambda@Edge, where you deploy Lambda functions alongside your CloudFront CDN.

===

What can you do with Lambda@Edge? About transformation (5) (IDH)

---

You can use Lambda to change CloudFront requests and responses:

-  After CloudFront receives a request from a viewer (viewer request)
-  Before CloudFront forwards the request to the origin (origin request)
-  After CloudFront receives the response from the origin (origin response)
-  Before CloudFront forwards the response to the viewer (viewer response)
-  You can also generate responses to viewers without ever sending the request to the origin

![image](https://user-images.githubusercontent.com/1868409/148752624-64abc6e7-a870-4ba2-9642-dea5a3fdac6d.png)

===

Some Lambda@Edge use Cases (3)

---

-  Dynamic Web Application at the Edge
-  Intelligently Route Across Origins and Data Centers
-  A/B Testing

===

Explain how Lambda Asynchronous Invocations works

---

Let's say some event is placed in an Event Queue. You can have a Lambda function to poll from this queue. If some error happens. The Lambda function will retry a maximum of 3 times (1 minute wait after 1st , then 2 minutes wait)

![image](https://user-images.githubusercontent.com/1868409/148754187-dde25535-67d1-4d0c-aa49-c756f901879a.png)

===

When using Asynchronous invocations with Lambda functions. When retries happen, there will be times where end up processing one request multiple times (resulting in duplicated entries). How can we avoid this?

---

Make sure the processing is idempotent (in case of retries)

===

What services do Asynchronous invocations with Lambda functions have? (3) (IDH)

---

-  Amazon Simple Storage Service (S3)
-  Amazon Simple Notification Service (SNS)
-  Amazon CloudWatch Events / EventBridge

There are others but just focus on these 3

===

What can we do with our lambda function if we reached the maximum number of retries? (asynchronous invocations)

---

We can configure to send the errored requests to a DLQ.

===

Let's say we want to implement a Cron job with a Lambda function, how can we achieve this using asynchronous invocations?

---

We can have an Cloudwatch EventBridge rule to trigger each hour and send the event to a target Lambda function.

===

What might happen if two writes are made to a single non-versioned object at the same time in S3? How can we mitigate this issue? (S3 Events Notifications)

---

-  If two writes are made to a single non- versioned object at the same time, it is possible that only a single event notification will be sent
-  If you want to ensure that an event notification is sent for every successful write, you can enable versioning on your bucket.

===

What services you can use with Lambda Event Source Mapping (3)

---

-  Kinesis Data Streams
-  SQS & SQS FIFO queue
-  DynamoDB Streams

===

Explain how Event Source Mapping in Lambda works in general (what's the common denominator?)

---

In this trigger method, you have an internal component polling from the source, then this same component will invoke an synchronous call to the Lambda function. The common denominator is that records need to be polled from the source

![image](https://user-images.githubusercontent.com/1868409/148930404-e46acd9d-3e25-4851-ac70-924433cbf3be.png)

===

Explain how Lambda Event Source Mapping works for Streams (Kinesis & DynamoDB streams) (3) (IDH)

---

-  An event source mapping creates an iterator for each shard and processes items in order.
-  This iterator can start from the beginning or from a specific timestamp.
-  Bear in mind that processed items aren't removed from the stream so other consumers can read them

===

For Event Source Mapping in Stream, how can we work with low traffic? (2)

---

-  We can use a batch window to accumulate records before processing.
-  We can have up to 10 batches per shard (running in paralell)

![image](https://user-images.githubusercontent.com/1868409/148931717-5750721b-5bb6-4357-8706-cbde358ca146.png)

===

Explain Error Handling for Lambda Event Source Mapping with streams.

---

By default, if your function returns an error, the entire batch is reprocessed until the function succeeds, or the items in the batch expire. That's why to ensure in-order processing, processing for the affected shard is paused until the error is resolved.

===

How can you configure the error handling for Streams in Lambda? (event source mapping) (3)

---

You can configure the event source mapping to:

-  discard old events (and optionally send them to a destination)
-  restrict the number of retries
-  split the batch on error (to work around Lambda timeout issues)

===

Explain how Lambda Event Source Mapping works for SQS and SQS FIFO.

---

The Event Source Mapping will poll SQS (Long Polling) with a specific batch size (1-10 messages)

===

When working with SQS queues and Lambda Event Source Mapping, what's the recommended visibility time out we should set to?

---

It's recommended to set the queue visibility timeout to 6 times the timeout of your Lambda function

===

When it comes to DLQ in SQS + Lambda Event Source Mapping, what's important to remember?

---

You need to set your DLQs in SQS, not in Lambda (DLQ for Lambda is only for async invocations)

===

Explain the Lambda Event Mapper scaling for Streams (Kinesis/DynamoDB), SQS standard and SQS FIFO

---

-  Kinesis Data Streams & DynamoDB Streams: One Lambda invocation per stream shard
-  SQS Standard: Lambda adds 60 more instances per minute to scale up
-  SQS FIFO: The Lambda function scales to the number of active message groups

===

Explain how Asynchronous invocations work for Lambda destinations (remember services)

---

For Asynchronous invocations you can define destinations for successful and failed event to any of the following services

-  Amazon SQS
-  Amazon SNS
-  AWS Lambda
-  Amazon EventBridge bus

===

Explain how Event Source mapping works for Lambda destinations (remember services)

---

For Event Source mappings you can define destinations for discarded event batches to any of the following services:

-  Amazon SQS
-  Amazon SNS

===

After Nov 2019, what's the recommended way to handle failed events in AWS Lambda?

---

AWS recommends you use destinations instead of DLQ now (but both can be used at the same time)

===

What does Lambda function use to get permission to access AWS resources?

---

A Lambda Execution Role (IAM Role) grants a Lambda function permissions to AWS services or resources

===

What's a good practice for execution roles for Lambda?

---

It's a good practice to create one Lambda Execution Role per function

===

What do AWS services use to get access to Lambda functions?

---

Lambda Resource Based Policies policies are used to give other accounts and AWS services
permission to use your Lambda resources. They are similar to S3 bucket policies for S3 buckets

===

When can an IAM principal access Lambda? (2)

---

-  If the IAM policy attached to the principal authorizes it (e.g. user access)
-  Or if the resource-based policy authorizes (e.g. service access). For example: when an AWS service like Amazon S3 calls your Lambda function, the resource-based policy gives it access.

===

When using enviroment variables for Lambda functions, what security feature do we have at our disposal?

---

You can encrypt secret env vars using KMS. These can be encrypted by the Lambda service key, or your own CMK

===

What do we need to do to enable X-Ray in a Lambda function? (3)

---

-  Enable in the Lambda configuration (Active Tracing)
-  Ensure Lambda Function has a correct IAM Execution Role (there is a managed policy called AWSXRayDaemonWriteAccess)
-  Use the AWS X-Ray SDK in your Lambda Code

===

Some environment variables Lambda uses to communicate with X-Ray (3)

---

-  \_X_AMZN_TRACE_ID: contains the tracing header
-  AWS_XRAY_CONTEXT_MISSING: by default, LOG_ERROR
-  AWS_XRAY_DAEMON_ADDRESS: the X-Ray Daemon IP_ADDRESS:PORT

===

When it comes to networking, what's the default behaviour of a Lambda function? What can it acess? or What can not?

---

By default, your Lambda function is launched outside your own VPC (in an AWS owned VPC). From there it can acess the internet and any AWS service (S3, Dynamo, SQS, etc). But it cannot acess any service or resource within a private subnet (EC2 instances, RDS, etc)

![image](https://user-images.githubusercontent.com/1868409/149508194-7a6b8a41-81e4-4833-a3b4-98e54455510a.png)

===

How can you configure a Lambda function within a custom VPC so that it can access resources within a Private subnet? (IDH)

---

For this you should define the VPC IDs, subnets and security groups and Lambda will create an ENI (Elastic Network Interfaces) to acess any resource you might have inside a private subnet. Bear in mind that is required to have a role of AWSLambdaVPCAccessExecutionRole

===

Let's say we have a Lambda function launch in a VPC, do we have Internet acess? If not, is it possible to get?

---

Deploying a Lambda function in a public subnet in a VPC does not give it internet access or a public IP. If you want internet acess, you will need to deploy it in a private subnet, so you can configure a NAT Gateway or Instance to access the internet.

![image](https://user-images.githubusercontent.com/1868409/149509699-f8b92221-0b63-4365-af84-932b7a57cc9d.png)

===

Explain how increasing RAM in lambda functions work? What do we get with more RAM? (3)

---

-  You can increase RAM in lambda functions from 128MB to 10GB in 1MB increments
-  The more RAM you add, the more vCPU credits you get
-  At 1,792 MB, a function has the equivalent of one full vCPU. So after 1,792 MB, you get more than one CPU, and need to use multi-threading in your code to benefit from it (up to 6 vCPU)

===

What do we need to do to our Lambda function if we are dealing with a CPU-Bound application?

---

If your application is CPU-bound (computation heavy), increase RAM, so that you get more vCPU credits

===

When it comes to time of execution, What's a good case for Lambda functions and what's not?

---

Lambda functions have a default timeout time of 3 seconds, that can be changend up to 900 seconds (15 min). So any operations within that time lenght is a good case for working with Lambda functions. If you have long running operations that take more time than that, it's better to use other alternatives like ECS, EC2, Fargate, etc.

===

Explain how the Lambda Execution Context works, what's a good use case for it? (2)

---

-  The execution context is a temporary runtime environment that can initialize any external dependencies of your lambda code.
-  This is great for database connections, HTTP clients, SDK clients since this context is maintained for some time in anticipation of another Lambda function invocation. So any next function invocation can “re-use” the context to execution time and save time in initializing connections objects

===

How can you handle file dowloads (or disk space for operations) with Lambda? Are there any limitations to keep in mind?

---

You can use the /tmp directory. By using this, when the execution context is frozen, the content of this directory will remain there, which provides transient cache that can be used for multiple invocations ( helpful to checkpoint your work). Bear in mind the max size you can use is 512 MB, and this can only be used for temporary files or data. For permanent persistance, use something else, like S3.

===

What's the concurrency limit for Lambda functions? And what options do we have for its management?

---

In AWS Lambda you can have up to 1000 concurrent executions. But you can limit that value by setting a “reserved concurrency” at the function level.

===

What happens when we go over our concurrency limit for our Lambda functions? (for synchronous and asynchronous invocations)

---

Each invocation over the concurrency limit will trigger a “Throttle”.

-  For synchronous invocations: the function returns a ThrottleError (429)
-  For asynchronous invocations: the function retries automatically and then send to DLQ

===

What can we do when we need more concurrent invocations than AWS can accept by default?

---

If you need a higher limit (more than 1000), open a support ticket

===

If we are not careful by setting a reserve concurrency for a lambda function, what can happen?

---

If you don't reserve a concurrency limit, one lambda function might end up exhausting all of your concurrent invocations available (1000). And other lambda functions in your account might start to throttle. Since the concurrency limit is shared across all the lambda functions in your account.

![image](https://user-images.githubusercontent.com/1868409/149678735-dba92089-7ada-42aa-8a0d-424246b4cd66.png)

===

Explain in detail how concurrency for Asynchronous Invocations work (throttled requests) (2)

---

-  For throttling errors (429) and system errors (500-series), Lambda returns the event to the queue and attempts to run the function again for up to 6 hours.
-  The retry interval increases exponentially from 1 second after the first attempt to a maximum of 5 minutes.

===

What's a Cold start in AWS Lambda?

---

Every time a new Lambda function insatance is launche, it will initialize any code outside of the handler. If this initialization is large (code, dependencies, SDK, etc), your function invocation can experience some delay. This is called "Cold start" and the reason why first requests served by new instances has higher latency than the rest.

===

How can we avoid "Cold start" in AWS lambda functions? (IDH)

---

We can configure some "Provisioned Concurrency". By doing this, concurrency is allocated before the function is invoked (in advance) so the cold start never happens and all invocations will have low latency. Additionaly, we can have some Application Auto Scaling policy to manage concurrency (schedule or target utilization)

===

How can we work around dependecies when using AWS Lambda? (e.g: npm packages) (3)

---

-  If your Lambda function depends on external libraries you need to install the packages alongside your code and zip it together. You can upload the zip file directly into lambda if its size is less than 50. Otherwise, you need to upload it first into S3 and then reference it from Lambda.
-  For native libraries (binaries): they need to be compiled on Amazon Linux
-  AWS SDK comes by default with every Lambda function so there is no need to package it in your prod dependecies.

===

What two ways do we have in Cloudformation to define Lambda functions? (2) (IDH)

---

-  Inline: for functions that are very short, simple and don't require any dependecies
-  Through S3: by uploading your lambda function to S3 as a Zip file. You must include S3 full path bucket, object name and version in your Cloudformation template. Otherwise Cloudformation will not update your function if some change happens.

===

Let's say you have one Lambda function code store in one account S3 bucket and we would like to run that code in 2 different accounts, How can you do this using Cloudformation? (2)

---

-  You need to create corresponding cloudformation stacks for each target account (the ones responsible for running the Lambda code). Then you need to define an Execution role for these two accounts. This role should allow to get and list the bucket where the code is stored.
-  On the bucket side, you need to define a bucket policy for allowing both account principals (Account IDs) to have read access to the bucket.

![image](https://user-images.githubusercontent.com/1868409/149757914-0a2ea425-eed5-47dd-8b4b-ef0d81656dd7.png)

===

What problems can we resolve with Lambda layers? (2)

---

-  We can run custom runtimes for languages that Lamda doesn't have support. Example: C++, Rust.
-  We can externalize Dependencies to re-use them, so that we don't have to package them every time we upload a new version of our Lambda code. Aditionally this layer of dependecies, can be re-used by other lamda functions.

![image](https://user-images.githubusercontent.com/1868409/149761581-01c4bb06-de97-4178-8075-2480d0395699.png)

===

What optios do you have for running your Lambda functions as containers?

---

You can use Lambda container images, where you need to use one of the supported AWS Lamda images (node.js, go, ruby, etc) or build your own by implementing the Lambda Runtime API. Any image you use must be stored in ECR.

===

Explain how Lambda Versions work and its characteristics

---

When we’re ready to publish a Lambda function, we create a version. A version will have its own ARN name and be immutable. So you can reference any version of your function at any time.

===

How can we work with environments using Lamda function versions? (let's say we want one version to be used in production and one other version to be used in development)

---

For this we have Lambda Aliase, they are pretty much pointers to our versions. So we can define a “dev”, "test", “prod” aliases and have them point at different lambda versions. They are also immutable, so you can chenge an Alias to point to a different version at any time.

===

Can you point a Lambda alias to another alias?

---

Aliases cannot reference aliases

===

We know we can use Lambda aliases to shift the production traffic to a new version. But how can we automate this process? (3)

---

CodeDeploy can help you automate traffic shift for Lambda aliases. We can use three strategies for this:

-  Linear: grow traffic every N minutes until 100%
-  Canary: try X percent then go 100%
-  AllAtOnce: immediate (risky)

![image](https://user-images.githubusercontent.com/1868409/149927615-b9e27f6c-d6d1-462c-8f57-80fff9c531e1.png)

===

What limits do we have in Lambda for Environment Variables and deployment size (code + dependecies) compressed and uncompressed?

---

-  For environment variables: 4KB
-  For deployment size: Compresed: 50 mb, and Uncompressed: 250 mb.
