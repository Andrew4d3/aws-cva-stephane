What's Amazon EBS?

---

An Amazon EBS volume is a durable, block-level storage device that you can attach to your instances. After you attach a volume to an instance, you can use it as you would use a physical hard drive.

===

Three major features of EBS

---

-  It uses the network to communicate the instance, which means there might be a bit of
   latency
-  It’s locked to an Availability Zone (AZ)
-  You get billed for all the provisioned capacity

===

Types of EBS (4)

---

EBS Volumes come in 4 types

-  GP2 (SSD): General purpose SSD volume that balances price and performance for a
   wide variety of workloads
-  IO1 (SSD): Highest-performance SSD volume for mission-critical low-latency or high- throughput workloads
-  ST1 (HDD): Low cost HDD volume designed for frequently accessed, throughput- intensive workloads
-  SC1 (HDD): Lowest cost HDD volume designed for less frequently accessed workloads

===

Should our EBS be available to use right after we attach it?

---

No, we still need to mount it.

===

When to use GP2 volumes? (2)

---

-  Low-latency interactive apps
-  Development and test environments

===

When to use IO1 volumes? (2)

---

-  Workloads that require sustained IOPS performance or more than 16,000 IOPS
-  I/O-intensive database workloads

===

When to use ST1 volumes? (2)

---

-  Big data
-  Data warehouses
-  Log processing

===

When to use SC1 volumes? (2)

---

-  Throughput-oriented storage for data that is infrequently accessed
-  Scenarios where the lowest storage cost is important

===

Whats an "Instance Store" in AWS?

---

An instance store provides temporary block-level storage for your instance. This storage is located on disks that are physically attached to the host computer.

===

Some pros for Instance Stores (3)

---

-  Better I/O performance
-  Good for buffer / cache / scratch data / temporary content
-  Data survives reboots

===

Some cons for Instance Stores (3)

---

-  On stop or termination, the instance store is lost
-  You can’t resize the instance store
-  Backups must be operated by the user

===

What's AWS EFS?

---

Amazon Elastic File System (Amazon EFS) provides a simple, scalable, fully managed elastic NFS file system for use with AWS Cloud services and on-premises resources. It is built to scale on demand to petabytes without disrupting applications, growing and shrinking automatically as you add and remove files, eliminating the need to provision and manage capacity to accommodate growth.

===

Features for EFS (3)

---

-  Managed NFS (network file system) that can be mounted on many EC2
-  EFS works with EC2 instances in multi-AZ
-  Highly available, scalable, expensive (3x gp2), pay per use

===

When to use both performance modes in EFS?

---

-  General purpose (default): latency-sensitive use cases (web server, CMS, etc…)
-  Max I/O – higher latency, throughput, highly parallel (big data, media processing)

===

Explain Storage Tiers in EFS

---

-  Standard: for frequently accessed files
-  Infrequent access (EFS-IA): cost to retrieve files, lower price to store

===

Steps for creating and attaching an EFS (8)

---

1. Create your EC2 instances. Set a specific security group to them.
2. Create a Security group for your EFS. Set the inbound rules to accept NFS traffic from the security group assinged to your EC2 instances
3. Create your EFS. Remember to set the SG you created at the previous step
4. Once you have created your EFS, It's time to attach it. Click on "Attach" to follow the instructions.
5. Open SSH terminals in your EC2 instances
6. Install the helper (following "Attach" instructions)
7. Create a directory to mount your EFS. Run the helper, refering the directory you just created
8. Your EFS is mounted! You can create a file in the directory and check its content from the other instace to verify that everything worked just well.

===

What should we do if we want to migrate an EBS to a different AZ?

---

-  Take a snapshot
-  Restore the snapshot to another AZ

===

When it comes to cost, what's more convenient? EBS or EFS?

---

EFS has a higher price point than EBS but you can leverage EFS-IA (Instant Access) for cost savings

===

EFS vs EBS vs Instance Store. When to use which?

---

EFS: When you want to share files accross multiple instances
EBS: When you want to have one volume attached to one single instance
Instance Store: When you want to get the max IOPS possible but you don't have concerns on your data being lost after a termination/shutdown
