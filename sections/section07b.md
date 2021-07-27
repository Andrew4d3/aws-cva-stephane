How to configure an ALB in AWS? (5)

---

1. First create your ALB. Set a SG that accepts inbound HTTP or HTTPS requests from anywhere.
2. Create the instance that you're going to use with your loadbalancer, configure a Security group that accepts HTTP inbound requests from the security group you created in the previous step.
3. Now it's time to register a Target group. Be sure to register all the machine you want to use with your loadbalancer.
4. Now go back to your load balancer and create a listener. Here you're going to define a rule for you target group, based on your path, hostname or querystring.
5. At this point your loadbalancer should be working, check your target groups instances to verify if their healthchecks are healthy.
