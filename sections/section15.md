What's the private docker registry used in AWS

---

ECR (Elastic Container Registry

===

What does a ECS task definition do?

---

Tasks definitions are metadata in JSON form to tell ECS how to run Docker Containers

===

Let's we want our ECS container to talk to S3 or pull an image from ECR, what do we need to have?

---

We need to set a task role (IAM role) in our task definition.

===

What do you need to create to run a task/container (or multiple task replicas) in ECS?

---

ECS Services help define how many tasks should run and how they should be run

===

Let's say we want to run multiple replicas of one service in one EC2 instance, how can we achieve this using ECS? (4)

---

-  First we need to create a new revision for the task. This time we will let the Host port empty, so that the ECS agent choose one randomly.
-  Then we need to create an ALB, we let the target group empty and create the corresponding SG that accepts 80 traffic from everywhere.
-  Now we need to update the Security Group for our ECS cluster, we need to accept All ports inbound traffic comming from the SG assigned to our Load Balancer
-  Now it's time to create a new service in our ECS cluster, this time we need to assign the load balancer that we created at the previous step.

![image](https://user-images.githubusercontent.com/1868409/128701025-32f83387-fcd1-4cad-a7c2-c5ec6d6e1c80.png)

===

How is access control in ECR?

---

Access is controlled through IAM (permission errors => policy)

===

What AWS command should we use to log into our ECR? (version 1 and version 2)

---

-  `aws ecr get-login --no-include-email --region eu-west-1` (version 1, no pipe)
-  `aws ecr get-login-password --region eu-west-1 | docker login --username AWS --password-stdin 1234567890.dkr.ecr.eu-west-1.amazonaws.com` (version 2 with Pipe)

===

What might the problem be if our EC2 instanste (working with ECS) is failing to pull images from ECR?

---

You might be missing the correct IAM role (ecsInstanceRole).

===

What service should we use if we wanted to run containers in AWS, but we don't want to worry about provisioning and scaling EC2 instances?

---

With Fargate, it’s all Serverless! We don’t provision EC2 instances. We just create task definitions, and AWS will run our containers for us. To scale, just increase the task number.

===

What's the difference between instance profiles and task roles when working with ECS? (2)

---

-  The Instancce profile works at the EC2 instance level and it's responsible (among other things) to allow the ECS agent to API calls to the ECS service and pull Docker images from ECR.
-  ECS taks roles work at the the service level, and they allow each task to have any permissions we want. The main idea is that we assign different roles (with minimum permissions) to different services, depending on what AWS resources we require those services to access.

![image](https://user-images.githubusercontent.com/1868409/129021925-95f140a9-3b86-4cb4-8a34-832b41a6fc09.png)

===

What does Amazon do when it places tasks in ECS?

---

When Amazon ECS places tasks, it uses the following process to select
container instances:

1. Identify the instances that satisfy the CPU, memory, and port
   requirements in the task definition.
2. Identify the instances that satisfy the task placement constraints.
3. Identify the instances that satisfy the task placement strategies.
4. Select the instances for task placement.

===

List ECS Task Placement Strategies (3)

---

-  Binpack
-  Random
-  Spread

===

Explain Binpack Task placement strategy (ECS)

---

Place tasks based on the least available amount of CPU or memory. This minimizes the number of instances in use (cost savings)

===

Explain Spread Task Placement strategy (ECS)

---

Place the task evenly based on the specified value. For example, the following definition:

![image](https://user-images.githubusercontent.com/1868409/129024145-58ba1ea9-c439-41ac-bef6-cf926b55e99c.png)

Will spread instances evenly based on the AZ.

===

Explain ECS Task Placement Constraints (2)

---

-  distinctInstance: place each task on a different container instance
-  memberOf: places task on instances that satisfy an expression

===

What does an ECS capacity provider do? (3)

---

-  A Capacity Provider is used in association with a cluster to determine
   the infrastructure that a task runs on. (To scale in/out your ECS infraestructure)
-  For ECS and Fargate users, the FARGATE and FARGATE_SPOT capacity
   providers are added automatically
-  For Amazon ECS on EC2, you need to associate the capacity provider with an
   auto-scaling group

===

What ECS Data volumes would you use if you wanted to extend the storage for temporary content?

---

EBS volumes will extend the storage capacity of your tasks.

===

What ECS Data volumes would you use if you wanted to share storage for multiple tasks and services?

---

EFS volumes: Tasks launched in any AZ will be able to share the same data in the EFS volume.

===

What ECS Data volumes would you use if you wanted to share an ephemeral storage between multiple containers that are part of the same ECS task?

---

Bind mounts: Works for both EC2 Tasks (using local EC2 instance storage) and Fargate tasks (get 4 GB for volume mounts)

===

Whene an EC2 instance (or you) cannot pull a Docker image, What would the problem be?

---

In case an EC2 instance (or you) cannot pull a Docker image, check IAM

===

How can we scale our services and clusters in ECS? (2)

---

-  Service Auto Scaling with target tracking, step scaling, or scheduled
-  Cluster Auto Scaling through Capacity Providers

===

Which ECS config must you enable in /etc/ecs/ecs.config to allow your ECS tasks to endorse IAM roles?

---

ECS_ENABLE_TASK_IAM_ROLE

===

You have started an EC2 instance and it's not registered with the ECS cluster. What's a reason for this issue? (3) and what is not? (1)

---

Possible reasons:

-  The ECS agent is not running
-  The AMI used isn't the AWS ECS AMI
-  The EC2 instance is missing IAM permissions

Not a reason:

The security groups on the EC2 instance are misconfigured
