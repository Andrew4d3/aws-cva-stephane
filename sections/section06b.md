When launching EC2 instances, what's the default behaviour for the EBS volumes? (2)

---

-  By default, the root EBS volume is deleted (attribute enabled)
-  By default, any other attached EBS volume is not deleted (attribute disabled)

===

For the EBS volume atached to the root of your EC2 instance, what do we need to do to preserve that volume after the instance termination?

---

We can change the default behaviour by disabling the "Delete on termination" option in the wizard.

===

When taking EBS snapshots, what's recommended to do?

---

Even when it's not required. It's recommend to detach your EBS volume from your instance before taking a snapshot of it.

===

How can you transfer an EBS snapshot to a different region?

---

In order to do this, you first need to "Copy" (AWS option) and then select the Region destination.

===

In short words, what's an AMI? (AWS)

---

AMI stands for Amazon Machine Image and it's customization of an EC2 instance.

===

Steps for creating your own AMIs (3)

---

1. Start an EC2 instance and customize it
2. Stop the instance (for data integrity)
3. Build an AMI – this will also create EBS snapshots

===

What EBS types can it only be used as boot (root) volumes?

---

Only gp2/gp3 and io1/io2 can be used as boot volumes

===

When working with EBS, what type should we use if we need less than 16000 IOPS? and what type should we use when we need more than that?

---

-  For less than 16000 use gp2 or gp3
-  FOr more than 16000 use io1 or io2 block express

===

Usually, EBS cannot be attached to multiple instances, but what's the exeption?

---

EBS from the io1/io2 are Mult-attach compatible, which mean you can attach several EC2 instances within the same AZ to one single EBS from this family.

===

Explain Provisioned modes in AWS EFS (2)

---

-  With Bursting Throughput mode, throughput on Amazon EFS scales as the size of your file system in the EFS storage grows
-  With Provisioned Throughput mode, you can instantly provision the throughput of your file system independent of the amount of data stored.
