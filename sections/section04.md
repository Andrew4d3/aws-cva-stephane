What does it mean to have High Availibility?

---

High availability means running your application / system in at least 2 data
centers (== Availability Zones). The goal of high availability is to survive
a data center loss

===

List of ELB (EC2 Load Balancers) and their supported protocols and which group is recommended to use?

---

-  Classic Load Balancer (v1 - old generation) – 2009
   -  HTTP, HTTPS, TCP
-  Application Load Balancer (v2 - new generation) – 2016
   -  HTTP, HTTPS, WebSocket
-  Network Load Balancer (v2 - new generation) – 2017
   -  TCP, TLS (secure TCP) & UDP

Overall, it is recommended to use the newer / v2 generation load balancers as they
provide more features

===

How to restrict EC2 inbound traffic using Load Balancers?

---

Edit or use a new security group that only have HTTP access to the security group assinged to your load balancer.

===

Let's say we have an application behind an ALB. Where should we get the IP client?

---

The true IP of the client is inserted in the header `X-Forwarded-For`. We can also get Port `(X-Forwarded-Port)` and proto (X-Forwarded-Proto)

===

If we wanted to use an ALB with EC2 instances, what should we need to define?

---

Whe should need to define Target groups. They will contain our targeted instances.

===

What does it mean to have stickiness in your target groups? (Load Balancers)

---

When a target group has stickiness it means the load balancer will "stick" an instance to a user's session for the specific period of time you have set for it. Stickiness will not guarantee that your AWS instances are 100% balanced, since multiple session might be sticked to one single instance.

===

What's the difference between Cross-Zone Load Balancing Enabled and Disabled?

---

-  With Cross Zone Load Balancing: each load balancer instance distributes evenly across all registered instances in all AZ
-  Otherwise, each load balancer node distributes requests evenly across the registered instances in its Availability Zone only.

===

How does SNI (Server Name Indication) work with SSL cert in your load balancers?

---

Both, ALB and NLB support SNI. So they can pick the correct SSL cert for each of your domain names assigned to the target groups. (It's not supported for CLB)

![image](https://user-images.githubusercontent.com/1868409/103717604-aec04880-4fa4-11eb-9cae-ba914bc89af4.png)

===

In Load Balancers, what's "Connection Draining"?

---

It's the time to complete “in-flight requests” while the instance is de-registering or unhealthy

![image](https://user-images.githubusercontent.com/1868409/103717619-b97add80-4fa4-11eb-9fe6-e77656c069be.png)

===

List of Scaling Policy types (4):

---

-  Target Tracking Scaling: most simple, based on average
-  Simple Scaling: based on CloudWatch alarm. You set one single threshold.
-  Step Scaling: similar to simple scaling, but you can set multiple thresholds in steps (20% - 30%, 30% - 40%, etc)
-  Scheduled Actions: you annticipate a scaling based on known usage patterns. You set a specific number of instances you want for a specifc date and time.

===

In ASG, what are "Scaling Cooldowns"? (2)

---

-  The cooldown period helps to ensure that your Auto Scaling group doesn't launch or terminate additional instances before the previous scaling activity takes
   effect.
-  In addition to default cooldown for Auto Scaling group, we can create cooldowns that apply to a specific simple scaling policy

![image](https://user-images.githubusercontent.com/1868409/103966795-5cfff580-513f-11eb-80fd-c13267c04797.png)

===

If your application is scaling up and down multiple times, what can you do?

---

modify the ASG cool-down timers and the CloudWatch Alarm Period that
triggers the scale in
