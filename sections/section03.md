===

What is every AWS region compound of?

---

Each AWS Region consists of multiple, isolated, and physically separate AZ's within a geographic area

===

What is an AZ? (3)

---

- Each availability zone (AZ) is one or more
  discrete data centers with redundant power,
  networking, and connectivity
- They’re separate from each other, so that
  they’re isolated from disasters
- They’re connected with high bandwidth,
  ultra-low latency networking

===

What's is AWS IAM?

---

IAM stands for: Identity and Access Management. It's the central place where you manage the security of your AWS infraestructure.

===

What entities do you have in IAM?

---

- Users: for people accessing your AWS platform
- Groups: a group of users
- Roles: for machines or aws services

===

Should we use our root user as main account?

---

It's never recommended to use the root user for your AWS operations. It should only be used for an initial setup. Instead, you should create users with approriate access (as minimal as possible)
