What's AWS RDS?

---

RDS stands for Relational Database Service. It’s a managed DB service for DBs that use SQL as a query language.

===

What are some of the major advantages of using RDS intead our own DB setup with EC2 instances?

---

RDS is a managed servide. This means that AWS would take care of the provisioning, OS patching, automatic backups, monitoring and other things that if we were using EC2 insances, it would require a lot of configuration an maintenance from ourself.

===

Which type of backup does RDS have? (2)

---

-  Automated Backups: Daily full backup of the database with a retention period of 7 days (can be increased to 35 days)
-  DB Snapshots: Manually triggered by the user with a retention period that could be as long as you want.

===

What are RDS Read replicas? (3)

---

Amazon RDS uses the DB engines' built-in replication functionality to create a special type of DB instance called a read replica from a source DB instance. The source DB instance becomes the primary DB instance. Updates made to the primary DB instance are asynchronously copied to the read replica. You can reduce the load on your primary DB instance by routing queries from your applications to the read replica.

===

Let's say you have a production database that is taking on normal load and now you want to run a reporting application to run some analytics. The problem is that you're afraid that the reporting application will affect the performance of the production databse since you'll be adding more reading operations (queries). How can you address this issue using AWS RDS? (3)

---

You create a Read Replica to run the new workload there. By doing this, the primary replica (used by the prod application) is unaffected. The reporting application will use the read replicas only to perform SELECT statements (not INSERT, UPDATES, DELETES etc)

===

When it comes to Network cost when using RDS read replicas, what should we be aware of? (2)

---

-  In AWS there’s a network cost when data goes from one AZ to another
-  To reduce the cost, you can have your Read Replicas in the same AZ (free)

![image](https://user-images.githubusercontent.com/1868409/107122689-f245f500-6877-11eb-9171-ee528c480cad.png)

===

How does RDS Multi AZ work? (3)

---

In a Multi-AZ deployment, Amazon RDS automatically provisions and maintains a synchronous standby replica in a different Availability Zone. The primary DB instance is synchronously replicated across Availability Zones to a standby replica to provide data redundancy. Bear in mind that by default the standby replica is not used as a read replica.

===

Can a Read replica be setup as a Multi AZ for Disaster recovery?

---

Yes, you can.

===

Types of RDS encryption (2). What's the difference between them?

---

-  At rest encryption: the encryption is defined at launch time. In this case, the data on disk is encrypted.
-  In-flight encryption: Encrypts communications between your application and your DB Instance using SSL/TLS. Amazon RDS creates an SSL certificate and installs the certificate on the DB instance when the instance is provisioned.

===

What do we need to remember when implementing encryption with read replicas?

---

If the master is not encrypted, the read replicas cannot be encrypted

===

When encrypting RDS backups, what do we need to remember? (3)

---

-  Snapshots of un-encrypted RDS databases are un-encrypted
-  Snapshots of encrypted RDS databases are encrypted
-  Can copy a snapshot into an encrypted one

===

Steps for encrypting an un-encrypted RDS database (4)

---

-  Create a snapshot of the un-encrypted database
-  Copy the snapshot and enable encryption for the snapshot
-  Restore the database from the encrypted snapshot
-  Migrate applications to the new database, and delete the old database

===

What two ways do we have to log into our RDS databases?

---

-  Traditional Username and Password
-  IAM-based authentication (MySQL and PostgreSQL)

===

What are the major differences of using Amazon Aurora instead MySQL or PostgresSQL? (5)

---

-  Aurora is “AWS cloud optimized” and claims 5x performance improvement
   over MySQL on RDS, over 3x the performance of Postgres on RDS
-  Aurora storage automatically grows in increments of 10GB, up to 64 TB.
-  Aurora can have 15 replicas while MySQL has 5, and the replication process
   is faster (sub 10 ms replica lag)
-  Failover in Aurora is instantaneous because it’s HA (High Availability) native.
-  Aurora costs more than RDS (20% more) – but is more efficient

===

How does an Aurora cluster work? (high-level)

---

Usually an Aurora cluster will have one master replica (writes and reads) and multiple read replicas (only reads). Write operations are performed through a write endpoint and read operations are perfomed through a read enpoint. A load balancer in the read endpoint will be responsible to evenly distribute the inbound queries across all the read replicas.

![image](https://user-images.githubusercontent.com/1868409/107855863-bffe3f80-6e03-11eb-96da-9de7adbda5b5.png)

===

What Aurora option should we use if we wanted an on-demand and auto-scaling DB?

---

Amazon Aurora Serverless is an on-demand, auto-scaling configuration for Amazon Aurora. It automatically starts up, shuts down, and scales capacity up or down based on your application's needs. It enables you to run your database in the cloud without managing any database capacity.

===

What Aurora option should we use if we wanted to protect ourself against region-wide outages?

---

Amazon Aurora Global Database is designed for globally distributed applications, allowing a single Amazon Aurora database to span multiple AWS regions. It replicates your data with no impact on database performance, enables fast local reads with low latency in each region, and provides disaster recovery from region-wide outages.

===

What AWS manage service do we have for in memory databases? (Redis, Memcache)

---

The same way we have RDS for self-managed Relational Databases, ElastiCache is for self-managed Redis or Memcached databases.

===

What solution architecture might we come up if we wanted to use ElastiCache alongside RDS? (caching) (3)

---

-  Applications queries ElastiCache, if not available, get from RDS and store in ElastiCache.
-  Helps relieve load in RDS
-  Cache must have an invalidation strategy to make sure only the most
   current data is used in there.

![image](https://user-images.githubusercontent.com/1868409/107867376-3ed19780-6e59-11eb-9669-5dd013baaded.png)

===

What solution architecture might we come up if we wanted to use ElastiCache for user's sesions?

---

Your application can writes the session data into ElastiCache then this data can be retrived by any other instance.

![image](https://user-images.githubusercontent.com/1868409/107867420-69235500-6e59-11eb-8a13-8988738e98a0.png)

===

Major diferences of Redis and Memcached

---

-  Redis uses read Replicas to scale reads while Memcached Multi-node for partitioning of data (sharding)
-  Redis has Data Durability using AOF persistence while Memcached is non persistent.
-  Redis has backup and restores features while Memcached does not.

===

What 3 questions do we have to ask ourself when deciding whether or not we want to apply a caching strategy?

---

-  Is it safe to use a cached value? The same piece of data can have different consistency requirements in different contexts. For example, during online checkout, you need the authoritative price of an item, so caching might not be appropriate.
-  Is caching effective for that data? Caching is good for datasets that change slowly but not so great for datasets that change frequently.
-  Is the data structured well for caching? Because caches are simple key-value stores, you might also need to cache a data record in multiple different formats, so you can access it by different attributes in the record.

===

Steps for Lazy caching / cache asude / lazy population / lazy loading (4)

---

1. Your app receives a query for data
2. Your app checks the cache to see if the object is in cache.
3. If so (a cache hit), the cached object is returned, and the call flow ends.
4. If not (a cache miss), then the database is queried for the object. The cache is populated, and the object is returned.

===

Pros for Lazy caching (2)

---

-  Only requested data is cached (the cache isn’t filled up with unused data)
-  Node failures (for caching) are not fatal (just increased latency to warm the cache)

===

Cons for lazy caching (2)

---

-  A Cache miss results in 3 network calls, with a noticeable delay for that request
-  The posibility to get stale data with a cache hit

===

Explain the Write-trough caching strategy

---

In a write-through cache, the cache is updated in real time when the database is updated. So, if a user updates his or her profile, the updated profile is also pushed into the cache. You can think of this as being proactive to avoid unnecessary cache misses, in the case that you have data that you absolutely know is going to be accessed.

===

Pros for Write-trough caching (2)

---

-  It avoids cache misses, which can help the application perform better and feel snappier.
-  It shifts any application delay to the user updating data, which maps better to user expectations.

===

Cons for Write-trough caching (2)

---

-  There will be missing Data until it is added / updated in the DB. You can mitigate this by implementing Lazy loading at the same time.
-  The cache can be filled with unnecessary objects that aren't actually being accessed.

===

What's cache eviction and how can it happen? (3)

---

Cache eviction means to free the memory of the old, unused data in the cache.
It can occur in three ways:

-  You delete the item explicitly in the cache
-  Item is evicted because the memory is full and it’s not recently used (LRU)
-  You set an item time-to-live (or TTL)

===

What to do if too many evictions are happening in your cluster?

---

if you are experiencing evictions with your cluster, it is usually a sign that you need to scale up (use a node that has a larger memory footprint) or scale out (add additional nodes to the cluster) in order to accommodate the additional data.

===

Things to take into account when applying caching in AWS (or in general) (4)

---

-  Start with Lazy Loading / Cache aside as a foundation. It's easy to implement and works for many situations.
-  Implement Write-through when Lazy loading is not enough. These two techniches are usually combined together.
-  Setting a TTL is usually not a bad idea, except when you’re using Write- through. Set it to a sensible value for your application.
-  Only cache the data that makes sense (user profiles, blogs, etc…)

===

You have a requirement to use TDE (Transparent Data Encryption) on top of KMS. Which database technology does NOT support TDE on RDS?

-  PostgreSQL
-  Oracle
-  MS SQL Server

---

PostgreSQL

===

You are managing a PostgreSQL database and for security reasons, you would like to ensure users are authenticated using short-lived credentials. What do you suggest doing?

---

Use PostgreSQL for RDS and authenticate using a token obtained through the RDS service.

===

Your organisation wants to enforce SSL connections on your MySQL database

---

Apply a 'REQUIRE SSL' statement to all your users in your SQL database
