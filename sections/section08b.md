What's the difference between Cluster Mode Disabled and Cluster Mode Enabled in AWS ElastiCache? What feature do you have available in both options?

---

-  In Cluster Mode disabled you scale reads using multiple read replicas and a primary node.
-  In cluster mode enable data is partitioned across shards, at the same time, each shard can have multiple read replicas.
-  You can use Multi-az for failover in both options.
