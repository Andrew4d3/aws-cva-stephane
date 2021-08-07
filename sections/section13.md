When you have S3 MFA Delete enabled, when will you need it and when will you not?

---

-  You will need it for permanently delete an object version or suspend versioning on the bucket
-  You won't need it for enabling versioning or listing deleted versions

===

When using S3 Access logs, Why should you not set your logging bucket to be the monitored bucket?

---

If you do this, you will generate a loop in your logs. Since every log will generate an object in your bucket that will again generate another log. So your bucket size will grow exponentially.

===

If we wanted to audit any request made to S3, from any account, authorized or
denied, what tool would we use?

---

S3 Access Logs

===

What would the best way be to replicate content from one bucket to another? (Same region or cross region) What would be necessary?

---

We can use S3 Replication (CRR & SRR). CRR for cross-region replication and SRR for same-region replication. It's necessary to set correct IAM permissions.

===

Some important notes to remember when using S3 replaction (4)

---

-  It will not replicate your old objects. Only the new ones you add.
-  It can replicate delete markers from source to target
-  Deletions with a version ID are not replicated
-  It doesn't work in chain. So Not replication with a third bucket that has replication enabled with the destination bucket.

===

What do we need to do in order to use S3 Replication?

---

-  First we need to create our destination Bucket (remember to enable versioning)
-  Then, in our source bucket, we need to go to the "Management section" and create a replication rule.

===

What do we need to do if we wanted to replicate our deletions using S3 Replication?

---

In "Additional replication options" we need to enable the "Delete marker replication" option.

===

Let's say, that temporarily we want to allow some users to have write or read access to a S3 bucket? How can we adress this scenario? (2)

---

-  We can use Pre-signed urls to allow list of users to download files by generating URLs dynamically.
-  We can also allow temporarily a user to upload a file to a precise location in our bucket.

===

What AWS cli command should we use if we want to presign a S3 object for temporarilly access?

---

The following:

```sh
aws s3 presign s3://[BUCKET NAME]/[BUCKET OBJECT] --region [REGION] --expires-in [EXP TIME IN S]
```

===

List of S3 storage classes

---

-  Amazon S3 Standard - General Purpose
-  Amazon S3 Standard-Infrequent Access (IA)
-  Amazon S3 One Zone-Infrequent Access
-  Amazon S3 Intelligent Tiering
-  Amazon Glacier
-  Amazon Glacier Deep Archive

===

When to use S3 Standard – General Purpose

---

Use Cases: Applications that require frequently access to data. Example: Big Data analytics, mobile & gaming applications, content distribution, etc.

===

When to use S3 Standard – Infrequent Access (IA) (some use cases)

---

Suitable for data that is less frequently accessed, but requires rapid
access when needed. Use Cases: Ideal for long-term storage, backups, and as a data store for disaster recovery files.

===

When to use S3 One Zone - Infrequent Access (IA) (some use cases)

---

-  S3 One Zone-IA is ideal for customers who want a lower-cost option for infrequently accessed data but do not require the availability and resilience of S3 Standard or S3 Standard-IA.
-  It’s a good choice for storing secondary backup copies of on-premises data or easily re-creatable data. You can also use it as cost-effective storage for data that is replicated from another AWS Region using S3 Cross-Region Replication.

===

How does S3 Intelligent Tiering work?

---

-  S3 Intelligent-Tiering works by storing objects in four access tiers.
-  It works by monitoring access patterns and then moving the objects that have not been accessed in a specific number of consecutive days to the corresponding access tier. (there is a small monitor fee)
-  New Objects uploaded are automatically stored in the Frequent Access tier.
-  If the object has not been accessed in 30 consecutive days then it's moved to the Infrequent Access tier.
-  If the object has not been accessed in 90 consecutive days then it's moved to the Archive Access tier (tier needs to enabled).
-  If the object has not been accessed in 180 consecutive days then it's moved to the Deep Archive Access tier (tier needs to enabled).
-  If the objects are accessed later, S3 Intelligent-Tiering moves the objects back to the Frequent Access tier.

===

Use cases for S3 Intelligent Tiering

---

It is the ideal storage class for data sets with unknown storage access patterns, like new applications, or unpredictable access patterns, like data lakes.

===

When should we use S3 Glacier?

---

We use it for files that we need to archive or back up for a long period of time (10 or more years, min 90 days) and we don't require a rapid access.

===

Compare retrieval times in S3 Glacier and S3 Glacier Deep Archive:

---

-  Expedited (1 to 5 minutes) (only available in S3 Glacier)
-  Standard (3 to 5 hours in S3 Glacier, 12 hours in deep Archive)
-  Bulk (5 to 12 hours, 48 hours in deep archive)

===

Your application on EC2 creates images thumbnails after profile photos
are uploaded to Amazon S3. These thumbnails can be easily recreated,
and only need to be kept for 45 days. The source images should be able
to be immediately retrieved for these 45 days, and afterwards, the user
can wait up to 6 hours. How would you design this? (life cycle rules) (2)

---

-  S3 source images can be on STANDARD, with a lifecycle configuration
   to transition them to GLACIER after 45 days.
-  S3 thumbnails can be on ONEZONE_IA, with a lifecycle configuration
   to expire them (delete them) after 45 days.

===

A rule in your company states that you should be able to recover your
deleted S3 objects immediately for 15 days, although this may happen rarely.
After this time, and for up to 365 days, deleted objects should be recoverable
within 48 hours. (Design life cycle rules) (3)

---

-  You need to enable S3 versioning in order to have object versions, so that
   “deleted objects” are in fact hidden by a “delete marker” and can be
   recovered
-  You can transition these “noncurrent versions” of the object to S3_IA
-  You can transition afterwards these “noncurrent versions” to
   DEEP_ARCHIVE

===

How many requests per second can S3 achieve? (read and write ops)

---

Your application can achieve at least 3,500 PUT/COPY/POST/DELETE and
5,500 GET/HEAD requests per second per prefix in a bucket. (prexix is everything between the bucket name and the object id itself)

===

When using KMS encryption with S3, what limitation do we have to take into account?

---

There is a service quota while using the KMS API ((5500, 10000, 30000 req/s based on region)). But you can request a quota increase using the Service Quotas Console.

===

What S3 performace techniques do we have for uploads? (2)

---

-  Multi-Part upload: you split the file in multiple parts and perform paralell uploads.
-  S3 Transfer Acceleration: Increase transfer speed by transferring file to an AWS edge location which will forward the data to the S3 bucket in the target region (compatible with multi part upload)

===

What S3 performace techniques do we have for downloads?

---

You can parallelize GETs by requesting specific byte ranges.

![image](https://user-images.githubusercontent.com/1868409/127788884-b17d4f1a-0a80-44e5-a257-fad70f1ba61c.png)

===

What AWS S3 feature could we use if we wanted to retrive or filter certain amount of data from a CSV or JSON file? (without having to download the whole object)

---

With S3 Select & Glacier Select you retrieve data using SQL which will perform server side filtering. It Can filter by rows & columns (simple SQL statements) which leads to have less network transfer and less CPU cost client-side-

===

Let's say you want to generate a thumbnail for each S3 image uploaded in your bucket, What S3 feature would you use to achive this?

---

You can use S3 Event Notifications so every time you upload an image a notification will be sent, indicating to the subscriber that a new image is available.

===

What S3 service will you use if you want to run complex queries across multiple objects in order to perform analytics or reporting?

---

AWS Athena is a serverless service to perform analytics directly against S3 files using SQL language for querying such files.
