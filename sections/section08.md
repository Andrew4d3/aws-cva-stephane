Explain Amazon VPC

---

Amazon Virtual Private Cloud (Amazon VPC) enables you to launch AWS resources into a virtual network that you've defined. This virtual network closely resembles a traditional network that you'd operate in your own data center, with the benefits of using the scalable infrastructure of AWS.

===

Explain Subnets in Amazon VPC (4)

---

-  Subnets are an AZ resource that allow you to partition your network inside your VPC. There are two types; public and private.
-  A public subnet is a subnet that is accessible from the internet
-  A private subnet is a subnet that is not accessible from the internet
-  To define access to the internet and between subnets, we use Route Tables

![image](https://user-images.githubusercontent.com/1868409/113077314-4a79e300-91a7-11eb-836f-0bf6c6a8b1e2.png)

===

What are internet gateways? (AWS VPC) (2)

---

-  Internet Gateways helps our VPC instances connect with the internet
-  Public Subnets have a route to the internet gateway

===

What are NAT Gateways and NAT Instances? What's the difference between them?

---

-  Both allow your instances in your Private Subnets to access the internet while remaining private
-  The difference is that NAT Gateways are AWS-managed while NAT Instances are self-managed

===

How can we make an EC2 instance in a Private subnet to be able to access the internet? (for software updates, patching, etc)

---

The EC2 instance living in the private network needs to connect with a NAT Gateway/Instance in a public subnet. Then the NAT will reach for the corresponding Internet gateway to get access to the internet.

![image](https://user-images.githubusercontent.com/1868409/113078465-89109d00-91a9-11eb-8358-58892376c0ea.png)

===

Differences between Network ACLs vs Security Groups (4) (Skip 3rd one)

---

![image](https://user-images.githubusercontent.com/1868409/113429732-651ea880-93af-11eb-8784-d66fc18064bf.png)

===

Where should we go in AWS if we want to capture information about IP traffic going into our network interfaces? What features do we have with this tool? (4)

---

VPC Flow Logs:

-  Capture IP traffic information going into our network interfaces: VPC, Subnet, Elastic Network Interface (ENI)
-  Helps to monitor & troubleshoot connectivity issues. (between subnets, the internet, etc)
-  Captures network information from AWS managed interfaces too: Elastic
   Load Balancers, ElastiCache, RDS, Aurora, etc.
-  VPC Flow logs data can go to S3 / CloudWatch Logs

===

What AWS VPC feature should we use if we wanted to connect two VPC together?

---

A VPC peering connection is a networking connection between two VPCs that enables you to route traffic between them using private IPv4 addresses or IPv6 addresses.

![image](https://user-images.githubusercontent.com/1868409/113434463-66ec6a00-93b7-11eb-83af-a76aff979d7c.png)

===

Things to take into account when using VPC Peering. (2)

---

-  They must not have overlapping CIDR (IP address range)
-  VPC Peering connection is not
   transitive (it must be established for each VPC that need to communicate with one another)

===

Let's say we have an EC2 instance and a Aurora DB that we want to connect together without using the public network (going through internet). What AWS feature can we use for this? (3)

---

A VPC endpoint enables private connections between your VPC and supported AWS services and VPC endpoint services powered by AWS PrivateLink. The traffic between your VPC and the other service does not leave the Amazon network. A VPC endpoint does not require an internet gateway, virtual private gateway, NAT device, VPN connection, or AWS Direct Connect connection.

===

Which two VPC endpoints do we have? What services do they target? (2)

---

-  VPC Endpoint Gateway: S3 & DynamoDB
-  VPC Endpoint Interface: the rest

===

What AWS options do we have when we want to connect our on-premise services to a VPC in AWS? (2) (Explain both of them)
Besides, If we use these solutions, what cannot we access?

---

-  Site to Site VPN: will connect an on-presises VPN to AWS. The connections goes over the internet and is automatically encrypted.
-  Direct Connect (DX): will establish a phisical and private connection between on-premises and AWS. This solution takes a month to establish.

Site-to-site VPN and Direct Connect cannot access VPC endpoints

![image](https://user-images.githubusercontent.com/1868409/113453836-420aed80-93dd-11eb-92e6-6245b9ede582.png)

===

Explain a typical 3 tier solution architecture in AWS (3)

---

-  In the public subnet of your VPC you will have a Route 53 routing requests to an ELB.
-  The ELB wil connect to an Auto Scaling Group in a private subnet that will contain several EC2 instances.
-  The EC2 instance will connect to your Data subnet, that will contain to an Amazon RDS DB that complements with a ElasticCahe store for caching and storing session data.

![image](https://user-images.githubusercontent.com/1868409/113454727-40dac000-93df-11eb-890f-4ed0cde37018.png)

===

How can you organize a LAMP stack in AWS? (6)

---

-  Linux: OS for EC2 instances
-  Apache: Web Server that run on Linux (EC2)
-  MySQL: database on RDS
-  PHP: Application logic (running on EC2)
-  Can add Redis / Memcached (ElastiCache) to include a caching tech
-  To store local application data & software: EBS drive (root)

===

In a Wordpress solution we usually want to store images and other media, so that all users can have access to them. If we have multiple EC2 instances in AWS, how can we achieve this?

---

You would need to attach an EFS to all the instances running the Wordpress application.

![image](https://user-images.githubusercontent.com/1868409/113455279-99f72380-93e0-11eb-95fa-66e14251de21.png)
