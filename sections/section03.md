What is every AWS region compound of?

---

Each AWS Region consists of multiple, isolated, and physically separate AZ's within a geographic area

===

What is an AZ? (3)

---

-  Each availability zone (AZ) is one or more
   discrete data centers with redundant power,
   networking, and connectivity
-  They’re separate from each other, so that
   they’re isolated from disasters
-  They’re connected with high bandwidth,
   ultra-low latency networking

===

What's is AWS IAM?

---

IAM stands for: Identity and Access Management. It's the central place where you manage the security of your AWS infraestructure.

===

What entities do you have in IAM?

---

-  Users: for people accessing your AWS platform
-  Groups: a group of users
-  Roles: for machines or aws services

===

Should we use our root user as main account?

---

It's never recommended to use the root user for your AWS operations. It should only be used for an initial setup. Instead, you should create users with approriate access (as minimal as possible)

===

How can we connect to an EC2 instance using SSH? (3)

---

1. Download the .pem file (private key)
2. Change permissions to the file with: `$ chmod 0400 <file>.pem`
3. Run SSH command: `$ ssh -i <file>.pem ec2-user@<Public IP or DNS>`

===

Where do we define our firewall rules for our EC2 instances?

---

Security Groups

===

What might be happening if your EC2 instance is not accessible (time out)?

---

There must be a Security Group issue,

===

What's an Elastic IP?

---

-  An Elastic IP address is a static (fixed) and public IPv4 address.
-  You can associate an Elastic IP address with your instance to enable communication with the internet.
-  An Elastic IP address is allocated to your AWS account, and is yours until you release it.

===

Should we use Elastic IP's?

---

No, it's a bad practice. It's better to use the Domain names provided by AWS or a Load balancer.

===

Steps to create an Elastic IP (4)

---

EC2 > Elastic IPs > Allocate > Wizard

===

How to provide startup instructions to your EC2 instances?

---

Using EC2 user data: When launching a new instance, go to "Advance Details". There you can upload or paste a shell script with all the software or configurations that you want. All commands will run with root permissions.

===

What types of EC2 instances do we have in AWS? (5) Explain each one of them.

---

-  On Demand: pay as you go
-  Reserved: pay for an extended period of time (min. 1 year)
-  Spot Instances: pay a cheaper price but your instance can get teminated/stopped at any moment if the current price supass your max price.
-  Dedicated Hosts: an entire physical server
-  Dedicated Instances (VPC configuration): : no other customers will share your hardware

===

When is it a good idea to use a Spot instance?

---

Services where we don't require a constant availibility and they also are resilient to failure like Batch Jobs, Data analysis etc.

===

When is recommended to use an On-Demand EC2 instance?

---

Recommended for short-term and un-interrupted workloads, where
you can't predict how the application will behave

===

When is recommended to use a Reserved EC2 instance?

---

Recommended for steady state usage applications that will be running for an exteded period of time (minimum 1 year) like databases or core stable services.

===

When do we need EC2 dedicated hosts?

---

Useful for software that have complicated licensing model or for companies that have strong regulatory or compliance needs

===

What's an AMI?

---

An Amazon Machine Image (AMI) provides the information required to launch an instance. You can create your own AMIs or used the ones provided by Amazon, the community or third-party vendors. A custom AMI can provide pre-installed packages and configurations to fit your needs and get you started quickly. They are also specific for every AWS region.
