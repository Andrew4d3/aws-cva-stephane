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
