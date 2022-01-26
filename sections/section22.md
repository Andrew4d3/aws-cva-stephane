Explain the structure of a DynamoDB database? (3)

---

-  DynamoDB is made of Tables, each table has a Primary Key that must be decided at creation time.
-  At the samne time each table can have an infinite number of items (called rows) wich contain attributes.
-  The maximum size of an item is 400KB.

===

Data types supported in DynamoDB (3)

---

-  Scalar Types – String, Number, Binary, Boolean, Null
-  Document Types – List, Map
-  Set Types – String Set, Number Set, Binary Set

===

What options do we have for Primary Keys in DynamoDB? (2)

---

-  **Partition Key (HASH)**: this has to be diverse enough so data is distributed
-  **Partition Key + Sort Key (HASH + RANGE)**: where the combination must be unique for each item. Example: users-games table, “User_ID” for Partition Key and “Game_ID” for Sort Key

===

Let's say we’re building a movie database? For each movie we have the following data:

-  movie_id
-  producer_name
-  leader_actor_name
-  movie_language

What would it be the best Partition Key to maximize data distribution? (and what is not a good option?)

---

In this case, “movie_id” has the highest cardinality so it’s a good candidate. Other options like “movie_language” doesn’t take many values and may be skewed towards English so it’s not a great choice for the Partition Key

_NOTE: High-cardinality refers to columns with values that are very uncommon or unique. High-cardinality column values are typically identification numbers, email addresses, or user names._

===

Explain Read/Write Capacity Modes in DynamoDB (throughput, planning, pricing, and when can you switch?) (2)

---

-  Provisioned Mode (default)
   -  You specify the number of reads/writes per second
   -  You need to plan capacity beforehand
   -  Pay for provisioned read & write capacity units (no matther consumption)
-  On-Demand Mode
   -  Read/writes automatically scale up/down with your workloads
   -  No capacity planning needed
   -  Pay for what you use, and it's more expensive (2.5 more than provisioned).

You can switch between different modes once every 24 hours

===

In DynamoDB provisioned capacity? What's RCU and WCU?

---

Both of them specify the capacity units for reads and writes (throughput):

-  Read Capacity Units (RCU) – throughput for reads
-  Write Capacity Units (WCU) – throughput for writes

===

If we're not sure how to set our RCU and WCU, but we still want to use provisioned capacity for DynamoDB, what options do we have? (IDH) (2)

---

-  Whe have the Option to setup auto-scaling of throughput to meet demand
-  The throughput can also be exceeded temporarily using “Burst Capacity”. If Burst Capacity has been consumed, you’ll get a “ProvisionedThroughputExceededException” that you can retry using an exponential backoff strategy,

===

How to calculate the Write Capacity Units you might need? Things you need to take into account (3)

---

-  One Write Capacity Unit (WCU) represents one write per second for an item up to 1 KB in size
-  If the item size is not integer you need to round up.
-  Formula is: (number of items per second) \* (size of item)

Example: We write 10 items per second, with item size 2 KB. Solution is: 10 \* (2 KB) = 20 WCUs

===

We write 120 items per minute, with item size 2 KB. Calculate WCU (IDH)

---

Solution: (120 / 60) \* 2 = 4 WCU

We need "number of items **per second**" that's we dive 120 by 60.

===

Explain types of reads in DynamoDB (2) (IDH)

---

-  Eventually Consistent Read: where we can get stale data if we fetch from a node that hasn't been updated
-  Strongly Consistent Read: where we are guaranteed to get the most updated data after each read if we set the “ConsistentRead” parameter to True. This consumes twice of RCU so it's more expensive

===

Explain how to calculate Read Capacity Units in DynamoDB. Things you need to take into account? (3)

---

-  One Read Capacity Unit (RCU) represents one Strongly Consistent Read per second, or two Eventually Consistent Reads per second, for an item up to 4 KB in size
-  If the item size is not multiple of four, you need to round it up to the next multiple.
-  Formula is: (number of items) \* (size of item / 4) for Strongly consistent reads and: (number of items / 2) \* (size of item / 4) for eventually consistent reads

Examples:

![image](https://user-images.githubusercontent.com/1868409/150517410-2a43c56e-5fd5-4749-a01b-d33e4083b187.png)

===

We need 10 Strongly Consistent Reads per second, with item size 6 KB. Calculate RCUs of DynamoDB (IDH)

---

Solution: 10 \* (8 / 4) = 20

Why 8 and not 6? Because 6 is not multiple of 4, so we need to round it up to 8.

===

How does DynamoDB work internally? What happen with RCU and WCU? (2)

---

-  Data is store in different partions. For determining wich partition a record should go. DynamoDB applies a hash function to the partition key.
-  WCUs and RCUs are spread evenly across partitions

===

What might the reasons be of throttling happening in DynamoDB? (3)

---

-  Hot Keys – one partition key is being read too many times (e.g., popular item)
-  Hot Partitions: maybe because we're using a partition key with low cardinality
-  Very large items, remember RCU and WCU depends on size of items

===

Possible solutions to resolve throttling issues in DynamoDB (3)

---

-  Exponential backoff
-  Use a good parition key (highly distributed with the high cardinality)
-  If RCU issue, we can use DynamoDB Accelerator (DAX)

===

List of DynamoDB API calls for Writing Data (create or update) (3)

---

-  PutItem: creates a new item or fully replace an old item (same Primary Key)
-  UpdateItem: edits an existing item’s attributes or adds a new item if it doesn’t exist
-  Conditional Writes: accept a write/update/delete only if conditions are met, otherwise returns an error

===

List of DynamoDB API calls for Reading Data (3)

---

-  GetItem: retrieves a single item from a table. This is the most efficient way to read a single item because it provides direct access to the physical location of the item.
-  Query: retrieves all of the items that have a specific partition key. Within those items, you can apply a condition to the sort key and retrieve only a subset of the data.
-  Scan: retrieves all of the items in the specified table. (This operation should not be used with large tables because it can consume large amounts of system resources or RCUs)

===

List of DynamoDB API calls for Deleting Data (2) (IDH)

---

-  DeleteItem: deletes an individual item, you can use conditionally
-  DeleteTable: deletes a whole table and all its items, much quicker deletion than calling DeleteItem on all items

===

List of DynamoDB API calls for Batch writes and reads (2)

---

-  BatchWriteItem: you can execute up to 25 PutItem and/or DeleteItem (not UpdateItem) in one call with limits of up to 16 MB of data written andup to 400 KB of data per item.
-  BatchGetItem: can return items in paralell from one or more tables with limits of up to 100 items or up to 16 MB of data.

===

Explain what a Local Secondary Index (LSI) in DynamoDB does. (2)

---

-  It's an Alternative Sort Key for your table that must be only defined at table creation time.
-  You can have up to 5 LSI per tamble and they can only be a scalar attribute (string, bool, number, etc)

===

Explain what a Global Secondary Index (GSI) in DynamoDB does? (2)

---

-  It's an Alternative Parition Key for your table that can be defined at any time (even though after table creation).
-  When defining a GSI, DynamoDB will create a full copy of the target table that will be kept in sync with the original copy. So your RCU will double

===

With GSI in DynamoDB, what might happen when writte calls throtlle in your index table? When do you need to be careful at?

---

If the writes are throttled on the GSI, then the main table will be throttled even if the WCU on the main tables are fine. So you have to be careful when choosing your GSI partition key and WCU for your index table.

===

Explain DynamoDB Optimistic Locking with an Example (2)

---

-  Optimistic locking is a strategy to ensure that the item that you are updating (or deleting) is the same as the item in Amazon DynamoDB. If you use this strategy, your database writes are protected from being overwritten by the writes of others, and vice versa.
-  DynamoDB achieves this by using a version number attribute. So only the first call that arrives will update such a version number, the second call will just notice the version number doesn't match with the intended one, so will not perform any update

![image](https://user-images.githubusercontent.com/1868409/150648396-9f618dad-5cbf-4e0e-a1fb-c665fa16f0a9.png)

===

What's a good option if you want to add caching in you DynamoDB database?

---

DynamoDB Accelerator (DAX) is a fully-managed, highly available, seamless in-memory cache for DynamoDB

===

In DynamoDB, How can you use ElastiCache in combination with DAX?

---

You can use DAX to cache individual objects and the result of your Query and Scan calls whereas you can use ElastiCache to cache agregation results (calculations of query results)

![image](https://user-images.githubusercontent.com/1868409/150700163-e70e0855-1d00-4f37-8865-c70a2b710d55.png)

===

Explain what DynamoDB streams are.

---

DynamoDB streams allow you to capture events of actions perfomermed upon a DynamoDB table (create, update, delete). Then you can send this event to multiple destinations like Kinesis Data Streams, Lambda functions or Kinesis Client Library Applications.

===

What are some use cases for DynamoDB streams. (3)

---

-  Updating real time dashboards for analytics purposes
-  Data replication across other DB engines (ElasticSearch, Mysql, etc)
-  Data replication across other DynamoDB tables for cross-region replication

===

What information can we choose to push into a DynamoDB stream? (4)

---

-  KEYS_ONLY – only the key attributes of the modified item
-  NEW_IMAGE – the entire item, as it appears after it was modified
-  OLD_IMAGE – the entire item, as it appeared before it was modified
-  NEW_AND_OLD_IMAGES – both the new and the old images of the item

===

Where are DynamoDB streams made of? (IDH)

---

DynamoDB Streams are made of shards, just like Kinesis Data Streams, but unlike them, you don’t provision shards, this is automated by AWS.

===

In DynamoDB what happens with previous events once you enable streams on a table?

---

Nothing, records are not retroactively populated in a stream after enabling it

===

Explain how DynamoDB TTL works (IDH)

---

When you set a TTL to your items in a table (unix epoch time), DynamoDB will automatically mark them for deletion once they are expired. But bear in mind this opperation doesn't happen right away (it could take up to 48 hours). So you would need to filter them out in your queries if you didn't want them to be visible.

===

Some important DynamoDB CLI attributes to know when retrieving items (2)

---

-  --projection-expression: one or more attributes to retrieve
-  --filter-expression: filter items before returned to you

===

Some AWS CLI Pagination options for DynamoDB (the same applies to S3) (3)

---

-  --page-size: to specify that the AWS CLI request a smaller number of items from each call (it will make multiple calls in the background)
-  --max-items: max. number of items to show in the CLI
-  --starting-token: specify the last NextToken to retrieve the next set of items

===

In DynamoDB, what operations do we have for performing transactions? How do they internally work? (2)

---

• TransactGetItems: one or more GetItem operations, if some other thread modifies one of the items being read during the transaction, the whole transaction will fail
• TransactWriteItems: one or more PutItem, UpdateItem, and DeleteItem operations (max 25) if some other thread modifies one of the items being written during the transaction, the whole transaction will fail

===

When it comes to cost with DynamoDB transactions, what do you need to keep in mind?

---

It will consumes twiche the WCUs & RCUs. In short words, the cost will be double

![image](https://user-images.githubusercontent.com/1868409/150776028-948c542d-8963-45fd-a9e0-07edccce795c.png)

===

When it comes to storing Session state, how DynamoDb compares to ElastiCache?

---

DynamoDB is serverless and store session on disk. ElastiCache is not serverless and session is stored in memory.

===

Imagine we have a voting application with two candidates, candidate A and candidate B. If Partition Key is “Candidate_ID”, this results into two partitions, which will generate issues if one cadidate has way more votes than the other one (e.g., Hot Partition). How can we resolve this?

---

A strategy that allows better distribution of items evenly across partitions is to add a suffix to Partition Key value

===

List of all the write types in DynamoDB (4)

---

-  Concurrent Writes: each write happens independently and one can overwrite the other
-  Conditional writes: you only write if the given condition is met
-  Atomic writes: it happens atomically
-  Batch writes: multiple writes in sequence

![image](https://user-images.githubusercontent.com/1868409/150965095-36d842cc-44f5-43df-b4c4-81f1be8d456f.png)

===

How can we use DynamoDB in combination with S3? (2) (it works for any DB)

---

-  We can use a DynamoDB to store S3 object paths of different files (pictures, zip, etc)
-  We can use a DynamoDB table to store the S3 object's metadata so that we can perform efficient queries on it:

![image](https://user-images.githubusercontent.com/1868409/150966218-c21d38d5-09b4-42e2-80de-08fbf1aa8e1b.png)

===

In DynamoDB, what options do we have for doing a Table Cleanup? (2) (not recommended and recommended)

---

-  Scan + DeleteItem (not recommended): consumes lots of RCU and WCU
-  Drop Table + Recreate table (recommended): fast, efficient, cheap

===

For copying a DynamoDB Table into another table, what options do we have? (3)

---

-  Using AWS Data Pipeline, this service will use an AWS EMR cluster to write into a S3 bucket the data to be transfered to the new DynamoDB table

![image](https://user-images.githubusercontent.com/1868409/150967690-8b861c1b-2e76-4d2b-80b5-3aabd6d5804f.png)

-  Backup and restore into a new table: Takes some time but it's easier to use and doesn't depend on other services
-  Scan + PutItem or BatchWriteItem: it's inneficient and you need to write your own code

===

What security features do we have in DynamoDB? (network, access, at rest and in-transit encryption) (3)

---

-  VPC Endpoints available to access DynamoDB without using the Internet
-  Access fully controlled by IAM
-  Encryption at rest using AWS KMS and in-transit using SSL/TLS

===

Addtional features in DynamoDB to remember (backups, replication, development, migration) (4)

---

-  Backup and Restore feature available: Point-in-time Recovery (PITR) like RDS
-  Global Tables: Multi-region and fully replicated.
-  DynamoDB Local: Develop and test apps locally without accessing the DynamoDB web service
-  AWS Database Migration Service (AWS DMS): tgis can be used to migrate to DynamoDB from MongoDB, Oracle, MySQL, S3, and others.

===

How can we define fine-grained access control to a DynamoDB table? (2)

---

-  Using Web Identity Federation or cognito Identity Pools, each user
   gets AWS credentials then you can assign an IAM Role to these users with a Condition that
   limits their API access to DynamoDB.
-  In addition to granting permissions for specific DynamoDB actions (GetItem, Query, PutItem, etc), the Condition element uses the following condition keys to limit the permissions as follows:
   -  LeadingKeys: limit row-level access for users on the Primary Key.
   -  Attributes: limit specific attributes the user can see

===

You have an e-commerce website where you are using DynamoDB as your database. You are about to enter the Christmas sale and you have a few items which are very popular and you expect that they will be read often. Unfortunately, last year due to the huge traffic you had the ProvisionedThroughputExceededException exception. What would you do to prevent this error from happening again?

---

Create DAX cluster. DynamoDB Accelerator (DAX) is a fully managed, highly available, in-memory cache for DynamoDB that delivers up to 10x performance improvement. It caches the most frequently used data, thus offloading the heavy reads on hot keys of your DynamoDB table, hence preventing the "ProvisionedThroughputExceededException" exception.

===

You want to perform a Scan operation on a DynamoDB table to retrieve all the items. What should you do to increase the performance of your scan operation?

---

Use Parallel Scans

===

You would like to paginate the results of a DynamoDB Scan operation in order to minimize the number of items returned in the CLI's output. Which of the following AWS CLI options should you use?

---

--max-items and --starting-token
