What common DNS records do we have in AWS? (4)

---

In AWS, the most common records are:

-  A: hostname to IPv4
-  AAAA: hostname to IPv6
-  CNAME: hostname to hostname
-  Alias: hostname to AWS resource.

===

How does a DNS TTL work? (3)

---

-  DNS TTL are used to determine the expiration time for your DNS record in the client's computer.
-  Your computer will cache the DNS record for the time declared by the TTL
-  Once this time is expired, it will make a new DNS request to the DNS server.

===

What trade-offs do we have to understand when deciding if we want to use a short or long DNS TTL? (2)

---

-  If we choose a short TTL (eg: 60s), it will be easy to propagate changes in our DNS record but at the same time we will increase the traffic to our DNS server (that means more expenses)
-  If we choose a long TTL (eg: 24h), there will be less traffic going to our DNS server (which means less spended money) but at the same time it would be difficult to propagate changes for our DNS records.

===

What's the differece between a CNAME and an AWS Alias?

---

-  A CNAME points a hostname to any other hostname (It does't have to be an AWS resource) and only works for non root domains (eg: something.mydomain.com, not mydomain.com)
-  An Alias points a hostname to an AWS Resource and can either work for root domains as well as for non root domains. It's also free of charge and it has a native healtcheck feature.

===

List of Route 53 routing policies (5)

---

-  Simple
-  Weighted
-  Latency
-  Geolocation
-  Multivalue

===

When to use a Simple Routing Policy? And what CANNOT we do with it? (2)

---

-  Use it when you need to redirect to a single resource
-  You can’t attach health checks to simple routing policy

===

What will it happen when you set multiple values (IPs) for a simple routing policy?

---

If multiple values are returned, a random one is chosen by the client

===

Explain weighted routing policies.

---

We use them when we want to split the traffic of one host name to different destinations (based on a %).

===

When latency of users is a priority what route 53 routing policy should we use? (2)

---

-  We should use a Latency Routing Policy. This policy will redirect to the server that has the least latency close to us.
-  Bear in mind, that this policiy doesn't take into account geographic proximity (Germany may be directed to the US if that’s the lowest latency)

===

How does a Route 53 health checker work?

---

-  If X health checks failes -> unhealthy (default 3)
-  If X health checks passes -> health (default 3)
-  The default Health Check Interval is 30s (can set to 10s but with a higher cost)

===

How do geolocation routing policies work? (2)

---

-  We use geolocation routing policies when we want to direct the traffic coming from a specific country or region to a specific IP or hostname.
-  We should also create a default address for those locations that are specified

===

How do multiValue routing policies work?

---

-  When a client makes a DNS request with multivalue answer routing, Route 53 responds to DNS queries with up to eight healthy records selected at random for the particular domain name.
-  These records can each be attached to a Route 53 health check, which helps prevent clients from receiving a DNS response that is not reachable.
