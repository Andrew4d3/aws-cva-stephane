What should we do if we want to allow our AWS users to have access to the billing dashboard? (2)

---

-  First, we should log into our root account and go to the My Account section.
-  Then, we should scroll down until we reach the "Access to Billing information" section, click on "edit" and activate "IAM acess"

===

How can we prevent our AWS account to exceed a target budget?

---

Using the billing dashboard, we can create a budget and set alerts to be triggered once our target budget goes beyond a specific threshold.

===

In AWS, How do EC2, EBS, ELB and ASG relate to each other?

---

You can rent and lauch several EC2 instances with one EBS atached to each one of them, you can load balance inbound requests to these instances using an ELB and for scaling in or out you can have one or more ASG policies.

===

Explain the AWS EC2 naming convention for this instance type: m5.2xlarge

---

-  m: instance class
-  5: generation
-  2xlarge: size within the instance class

===

List of the top-4 most important EC2 instance types

---

-  General Purpose
-  Compute Optimized
-  Memory Optimized
-  Storage Optimized

===

Explain General Purpose EC2 instances and one of their use cases (2)

---

General purpose instances provide a balance of compute, memory and networking resources. These instances are ideal for applications that use these resources in equal proportions such as web servers and code repositories.

===

Explain Compute Optimized EC2 instances and one of their use cases (6)

---

Compute Optimized instances are ideal for compute bound applications that benefit from high performance processors.

Some use cases:

-  Batch processing workloads
-  Media transcoding
-  High performance web servers
-  High performance computing (HPC)
-  Scientific modeling & machine learning
-  Dedicated gaming servers

===

Explain Memory Optimized EC2 instances and one of their use cases (3)

---

Memory optimized instances are designed to deliver fast performance for workloads that process large data sets in memory. (3)

Some use cases:

-  Memory-intensive applications such as open-source databases
-  In-memory caches
-  and real time big data analytics

===

Explain Memory Optimized EC2 instances and one of their use cases (5)

---

Storage optimized instances are designed for workloads that require high, sequential read and write access to very large data sets on local storage (5).

Some use cases:

-  NoSQL databases (e.g. Cassandra, MongoDB, Redis)
-  Cache for in-memory databases (for example, Redis)
-  Scale-out transactional databases
-  Data warehousing applications
-  Distributed file systems

===

In AWS, what are security groups? and what do they regulate?

---

The act as “firewalls” which regulate the following:

-  Access to Ports
-  Authorised IP ranges – IPv4 and IPv6
-  Control of inbound network (from other to the instance)
-  Control of outbound network (from the instance to other)

===

When working with security groups and SSH access in AWS, what is it recommended to do?

---

It’s good to maintain one separate security group for SSH access

===

Some importants port to know when working with security groups in AWS (5)

---

-  21 = FTP (File Transfer Protocol)
-  22 = SSH (Secure Shell) and SFTP (Secure File Transfer Protocol)
-  80 = HTTP – access unsecured websites
-  443 = HTTPS – access secured websites
-  3389 = RDP (Remote Desktop Protocol) to log into a Windows instance

===

In security groups, what traffic is blocked and what traffic is authorized by default? (2)

---

-  All inbound traffic is blocked by default
-  All outbound traffic is authorised by default

===

What sub-categories do we have for EC2 reserved instances? (2)

---

-  Convertible Reserved Instance: you can change the Instance type (e.g: from micro to large)
-  Scheduled Reserved Instances: the instance is only launched during the window of time you reserve. This is convenient when you only need an instance running peridoically (wekly, monthly, etc) for an extended period of time (min 1 year)

===

What's the major difference between dedicated instances and dedicated hosts in EC2?

---

With dedicated instances you don't have any control over the underlying hardware.

===

How long can you reserve an EC2 Reserved Instance?

---

1 **OR** 3 years. No time inbetween.
