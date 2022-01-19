What attributes does AWS SQS have? (abouth throughput, retention, latency, size, etc) (6)

---

-  Unlimited throughput, unlimited number of messages in queue
-  Default retention of messages: 4 days, maximum of 14 days
-  Low latency (<10 ms on publish and receive)
-  Limitation of 256KB per message sent
-  Can have duplicate messages (at least once delivery, occasionally)
-  Can have out of order messages (best effort ordering)

===

In AWS SQS how producing and consuming messages work?

---

-  Produced to SQS using the SDK (SendMessage API)
-  The message is persisted in SQS until a consumer deletes it

![image](https://user-images.githubusercontent.com/1868409/147746247-17246668-7512-476d-ae7f-83cab402b1b1.png)

-  Consumers (running on EC2 instances, servers, or AWS Lambda) poll SQS for messages
-  Process the messages (example: insert the message into an RDS database)
-  Delete the messages using the DeleteMessage API

![image](https://user-images.githubusercontent.com/1868409/147746291-40b8a2a4-2bd2-487c-9f7a-68b17be996fd.png)

===

What's the reason for SQS being a good scalibility option?

---

Consumers can receive and process messages in parallel so we can scale consumers
horizontally to improve throughput of processing

===

How can we use SQS with ASG?

---

Our ASG can use a specifc SQS Cloudwatch metric (queue length) to trigger an alarm that will scale in or out our EC2 instances.

![image](https://user-images.githubusercontent.com/1868409/147746663-ba3b2794-7cbd-49d6-b6b0-26dc7afa967f.png)

===

One example of decoupling applications using SQS

---

Let's say you have an application that processes videos (long running operation). With SQS you can separate your application into one (frontend) that receives your requests and then from there you can push those requests to SQS so that they can be polled by a backend application. Then this application can process this video and (if you want) push them to S3.

![image](https://user-images.githubusercontent.com/1868409/147747106-b8efcf73-b37a-4eef-9469-74d4304f4526.png)

===

What SQS encryption options do we have? (in-flight, at-rest and other) (3)

---

-  In-flight encryption using HTTPS API
-  At-rest encryption using KMS keys
-  Client-side encryption if the client wants to perform encryption/decryption itself

===

For accessing SQS, what options do we have? (2)

---

-  IAM policies to regulate access to the SQS API
-  SQS Access Policies (similar to S3 bucket policies) useful for allowing other services (SNS, S3…) to write to an SQS queue and for cross-account access.

===

What do we need to configure if we wanted to capture S3 related events with SQS?

---

We need to configure a SQS access policy like this (no need to memorize, just check docs):

```json
{
   "Version": "2012-10-17",
   "Id": "example-ID",
   "Statement": [
      {
         "Sid": "example-statement-ID",
         "Effect": "Allow",
         "Principal": {
            "Service": "s3.amazonaws.com"
         },
         "Action": ["SQS:SendMessage"],
         "Resource": "SQS-queue-ARN",
         "Condition": {
            "ArnLike": {
               "aws:SourceArn": "arn:aws:s3:*:*:awsexamplebucket1"
            },
            "StringEquals": {
               "aws:SourceAccount": "bucket-owner-account-id"
            }
         }
      }
   ]
}
```

===

Explain SQS Message Visibility Timeouts

---

When a message is polled by a consumer, it becomes invisible to other consumers for 30 seconds (default time). This means the message has 30 seconds to be processed and deleted from the queue. Once the visibility timeout is over (and if the msg hasn't been deleted from the queue), the message becomes visible to other consumers again.

![image](https://user-images.githubusercontent.com/1868409/147820554-e72cdc5d-7ec1-4733-a9f3-28c6d033419e.png)

===

How to deal with cases when we have SQS messages than take more time than the visibility timeout to process?

---

To avoid duplicates, a consumer could call the ChangeMessageVisibility API to get more time.

===

What trade-offs do we need to understand when changing the visibility timeout in SQS to a lower or higher value? (2)

---

-  If visibility timeout is high (hours), and consumer crashes, re-processing will take time
-  If visibility timeout is too low (seconds), we may get duplicates

===

Explain SQS Dead Letter Queues

---

We know that if a consumer fails to process a message within the Visibility Timeout the message goes back to the queue. Sometimes this message has something wrong so it will never be processed, no matter how many times we try. To resolve this, in SQS we can set a threshold of how many times a message can go back to the queue so when this MaximumReceives threshold is exceeded, we send the errored msg into another SQS queue called dead letter queue (DLQ) so that it can be debugged by a person or processed by a different application.

===

What's important to consider when defining configurations of a Dead Letter Queue in SNS?

---

We need to make sure to process the messages in the DLQ before they expire. Sometimes this involves some manual work. So that's why it's important to set an appropriate retention period of 14 days so we have enough time to do it.

===

In SQS, what do we need to do if we don't want to deliver a message into a queue right away? (IDH)

---

We need to configure a "Delivery delay" time. By doing this, any message will be available only after that time is over. You can overwrite this value at any time by using the DelaySeconds parameter.

===

If you want to avoid making too many API calls to a SQS queue when it's empty. What options do we have? (2) (IDH)

---

-  We can configure something called "Long Polling". When a consumer requests messages from the
   queue, it can optionally “wait” for a configurable time that can be between 1 sec to 20 sec (20 sec preferable)) for messages to arrive if there are none in the queue.
-  Long polling can be enabled at the queue level or at the API level using WaitTimeSeconds

===

In SQS, message size limit is 256KB, how can we send large messages, e.g. 1GB? (Explain implementation)

---

We can use the SQS Extended Client (Java Library and other languages). With this library, the producer will first upload the file (msg body) to S3. Then the producer will only send the reference for getting such a object into SQS. So the consumer can retrieve it from there.

![image](https://user-images.githubusercontent.com/1868409/147852681-6fb8fb5d-5050-4f3b-92a2-0291d8c23710.png)

===

SQS API calls for managing queues (and one important attribute) (3)

---

-  CreateQueue (MessageRetentionPeriod)
-  DeleteQueue
-  PurgeQueue: delete all the messages in queue

===

SQS API calls for managing messages (and some important attributes). (4)

---

-  SendMessage (DelaySeconds), ReceiveMessage, DeleteMessage
-  MaxNumberOfMessages (attr): default 1, max 10 (for ReceiveMessage API)
-  ReceiveMessageWaitTimeSeconds: Long Polling
-  ChangeMessageVisibility: change the message timeout

===

How to send multiple calls at once? To decrease costs

---

We can use Batch APIs for SendMessage, DeleteMessage, ChangeMessageVisibility, if you want to decrease your costs

===

In SQS, if we want to guarantee the messages ordering, what option do we have? What limitions do we have with such a option?

---

We can create a FIFO Queue, with this type of queue, the order of messages is guaranteed just like a traditional queue (First in, first out). Bear in mind, this option comes with a throughput limitation that Standard queues don't have: 300 msg/s without batching, 3000 msg/s with batching.

===

Explain SQS FIFO Deduplication (interval, and methods (2))

---

-  De-duplication interval is 5 minutes, so no message sharing the same deduplication id will be sent during this interval
-  Two de-duplication methods:
   -  Content-based deduplication: will do a SHA-256 hash of the message body
   -  Explicitly provide a Message Deduplication ID

===

How to organize "namespaces" or "subsets" of messages with SQS FIFO queues? (IDH)

---

To get ordering at the level of a subset of messages, specify different values for MessageGroupID. Messages that share a common Message Group ID will be in order within the group. Bear in mind that ordering across groups is not guaranteed

![image](https://user-images.githubusercontent.com/1868409/147853565-0733b82b-a321-4f6e-84a5-81bef459c8ca.png)

===

When to use AWS SNS?

---

We use SNS for event-driven architectures where we want to send one message to many receivers (called subscribers). In short words, a pub/sub pattern

===

What ways do we have in SNS for publishing messages? (2)

---

-  Topic Publish: where we create a topic, create a subscription (or many) and publish messages into the topic
-  Direct Publish (for mobile apps SDK): where we create a platform application, create a platform endpoint and then publish a message into the platform endpoint

===

In SNS, what options do we have for encryption and access?

---

For both, the same as SQS

===

Explain the Fan Out pattern (SNS + SQS)

---

The fan out pattern comes to resolve the issue when we need one event to be pushed into multiple SQS queues. If we dind't have SNS, we would need to individually push such a message into each queue, but with SNS we can simply send the message once into one common SNS topic, and then each targeted SQS queue would subscribe to this topic.

![image](https://user-images.githubusercontent.com/1868409/147862201-611a54d1-0393-40a2-bb97-179da419fd18.png)

===

Think about this scenario: For the same combination of: event type (e.g. object create) and prefix
(e.g. images/) you can only have one S3 Event rule. If we wanted to send the same S3 event to many SQS queues, what would we need to do?

---

We would need to implement the fan out pattern with SNS+SQS. So we would need to have the event rule to send to a specific SNS topic, then each queue would need to subscribe to such a topic to process the given message.

![image](https://user-images.githubusercontent.com/1868409/147862296-92d92d9e-ab50-424e-8148-484de23f6939.png)

===

How can we guarantee the message ordering in SNS? (do not confuse with SQS) (IDH)

---

We would need to use a SNS FIFO, it has similar features as SQS FIFO queues (group ordering, deduplication, etc) and it can only be subscribed by them.

===

Let's say we are subscribed to a given SNS topic, but we only care about a subset of the messages sent into that topic. What option do we have for dealing with this sceneario? (IDH)

---

We can create a JSON filter policy, so the subscription will filter out each message that doesn't conform to the given policy. Bear in mind that if a subscription doesn't have a filter policy, it will receive each message.

===

Type of subscribers for SNS (6)

---

Subscribers can be:

-  SQS
-  HTTP / HTTPS (with delivery retries – how many times)
-  Lambda
-  Emails
-  SMS messages
-  Mobile Notifications

===

What's the purpose of AWS Kinesis?

---

Makes it easy to collect, process, and analyze streaming data in real-time.

===

AWS Kinesis is sub-divided in what services? (4)

---

-  Kinesis Data Streams
-  Kinesis Data Firehose
-  Kinesis Data Analytics
-  Kinesis Video Streams

===

How does Kinesis Data Streams work? (4)

---

-  A Kinesis data stream is divided in shards. If you want to scale up your data stream, you need to add more shards.
-  To send data into a shard, you need to send a "record". This record will contain a partition key (to indicate which shard will go) and the data blob (up to 1 mb).
-  Kinesis can store the record for a retention period between 1 day and 365 days.
-  Once a record is sent to a consumer (app, lambda, and others), it will contain the same information sent by the client (partition key, data blob) and a sequence number indicating the record location in the shard.

![image](https://user-images.githubusercontent.com/1868409/147890241-a12a086c-ca98-4793-802d-a7a4f1cf4543.png)

===

Some other characteristics of Kinesis (billing, features, etc) (3)

---

-  Billing is per shard provisioned, can have as many shards as you want
-  Ability to reprocess (replay) data
-  Once data is inserted in Kinesis, it can’t be deleted (immutability)

===

Produces for Kinesis (3)

---

AWS SDK, Kinesis Producer Library (KPL), Kinesis Agent

===

Consumers for Kinesis (5) (2 groups)

---

• Custom implementation: Kinesis Client Library (KCL), AWS SDK
• Managed: AWS Lambda, Kinesis Data Firehose, Kinesis Data Analytics

===

How does Kinesis manage security? (access, encryption (3), network, audit) (6)

---

-  For access: IAM policies
-  In Fligh encryption: HTTPS
-  At rest encryption: KMS
-  You also have the option to implement client encryption
-  Network: VPC endpoints
-  Audit: you can monitor API calls using CloudTrail

===

What's the Write throughput for Kinesis?

---

1 MB/sec or 1000 records/sec per shard

===

What API call do we use for sending records into a Kinesis shard?

---

PutRecord API

===

How can we reduce costs when sending records to Kinesis?

---

Use batching with PutRecords API to reduce costs & increase throughput

===

Explain how Kinesis producers work with an example

---

Let's say we have a series of IoT devices as our Kinesis producers. We can have each device (or group of devices) to have a distint partition id. Then Kinesis will apply a hash function to this partition id to indicate which shard the record will go.

![image](https://user-images.githubusercontent.com/1868409/147890654-9e812aea-6389-498f-a569-aa07eecd04ae.png)

===

Explain the ProvisionedThroughputExceeded error in Kinesis and how to resolve it (3)

---

When you send more than 1MB/s or 1000 records/sec into a shard, you will be exceeding the max throughput for a shard and a ProvisionedThroughputExceeded will apper. To resolve it, there are the following solutions:

-  Use highly distributed partition key
-  Retries with exponential backoff
-  Increase shards (scaling)

===

Explain Kinesis consumers (shared and enhanced)

---

-  Shared (Classic) Fan-out Consumer: all consumers will share the same through for one shard (2 MB/s). In this case, the consumer will pull data from the shard using the API call GetRecords.
-  Enhanced Fan-out Consumer: each consumer will have its own throughput of 2MB/s. In this case, the shard will push data into the consumer when the API call SubscribeToShard is used.

![image](https://user-images.githubusercontent.com/1868409/147922912-da760b38-16a3-4452-86b7-9c6c71eb5f0a.png)

===

Shared (Classic) Fan-out Consumer vs Enhanced Fan-out Consumer, when to use which in Kinesis?

---

-  Use classic when we have a low number of consuming applications that don't require a low latency (200ms or more) and we want to minimize cost.
-  Use enhanced when we have Multiple consuming applications for the same stream that require a low latency of 70ms and we are willing pay more (higher cost)

===

Characteristics of AWS Lambda consumers for Kinesis (3)

---

-  Supports Classic & Enhanced fan-out consumers
-  Read records in batches where you can configure batch size and batch window. You can process up to 10 batches per shard simultaneously
-  If error occurs, Lambda retries until succeeds or data expired

===

Explain how the Kinesis Client Library (KCL) works. Explain scaling.

---

KCL is a Java library that helps read records from a Kinesis Data Stream when distributed applications are sharing the read workload. With KCL each shard is to be read by only one KCL instance and progress is checkpointed into a DynamoDB. By doing this, each instance can know where the read ponter is.
To scale KCL, you only need to add more KCL instances (max one per shard)

![image](https://user-images.githubusercontent.com/1868409/147925772-7baf67ea-487f-4c8b-872e-1b3faf29f179.png)
![image](https://user-images.githubusercontent.com/1868409/147925815-9f6c35d6-53dc-469d-89c1-480890729379.png)

===

In Kinesis, what operations can we perform to **increase** our data stream capacity?

---

For increasing capacity we can do a "shard splitting", what this actually do is to create two new shard and delete the old one once the data is expired. So our throughput will increase in 1MB/s but all also the cost. Bear in mind that this is a manual operation and you can't split to generate more than two shards.

![image](https://user-images.githubusercontent.com/1868409/148048161-3c2ea328-a594-42df-a010-8a20d188bb10.png)

===

In Kinesis, what operations can we perform to **decrease** our data stream capacity?

---

For decreasing capacity we can do a "shard merge", what this actually do is to create one new shard and delete the two old ones when their data gets expired. So our throughput will decrease in 1MB/s wich also reduces cost. Bear in mind that this is a manual operation and you can't merge more than two shards.

![image](https://user-images.githubusercontent.com/1868409/148048262-cb2ab715-4867-4ab2-851f-ce2bc27de0d8.png)

===

What is Kinesis Data Firehose

---

Kinesis Data Firehose is a fully managed service that can load (and optionally transform) streaming data comming from different consumers (apps, clients, or event a kinesis data streams) into a given destintation (AWS or 3rd party)

![image](https://user-images.githubusercontent.com/1868409/148049530-5397d521-ee0b-43fc-8559-31522be1c2be.png)

===

List Kinesis Data Firehose destinations (AWS (3), 3rd party, custom (1))

---

-  AWS Destinations (mandatory to remember):
   -  S3
   -  Amazon Redshift
   -  Amazon OpenSearch Service (ElasticSearch fork)
-  3r-party partners (no need to remember all)
   -  Datadog
   -  Splunk
   -  NewRelic
   -  MongoDB
-  Custom Destinations: HTTP Endpoint

===

Some Kinesis Data Firehose characteristics to remember about cost, latency, features, backups (4):

---

-  Pay for data going through Firehose
-  Near Real Time: 60 seconds latency minimum for non full batches or minimum 32 MB of data at a time
-  Supports many data formats, conversions, transformations, compression and supports custom data transformations using AWS Lambda
-  It can send failed or all data to a backup S3 bucket

===

Differences between Kinesis DataStream and Kinesis Data Firehose (6)

---

![image](https://user-images.githubusercontent.com/1868409/148050424-3f6eda0a-377d-4ca7-9bfa-61661ed591d9.png)

===

What Kinesis feature would you use to analyze and query streaming data?

---

Kinesis Data Analytics

===

Major characteristics of Kinesis Data Analytics (features, management, scaling, pricing) (5)

---

-  Perform real-time analytics on Kinesis Streams using SQL
-  Fully managed, no servers to provision
-  Automatic scaling
-  Pay for actual consumption rate
-  Can create streams out of the real-time queries

===

Use cases for Kinesis Data Analytics (3)

---

-  Time-series analytics
-  Real-time dashboards
-  Real-time metrics

===

Imagine you have 100 trucks (truck_1, truck_2, … truck_100) on the road sending their GPS positions regularly into AWS.You want to consume the data in order for each truck, so that you can track their movement accurately.How should you send that data into Kinesis?

---

You need to send the “truck_id” as the Partition Key value. By doing this, the same key will always go to the same shard.
