What problems does AWS Elastic Beanstalk resolve?

---

-  When launching a new application in AWS, there are a lot of things developers need to configure (EC2 instances, ELB, ASG). This process might become tedious if you're constantly launching multiple apps (since most web apps share the same configurations of ALB + ASG).
-  AWS Elastic Beanstalk is an easy-to-use service for deploying and scaling web applications and services developed with the most popular technologies. You can simply upload your code and Elastic Beanstalk automatically handles the deployment, from capacity provisioning, load balancing, auto-scaling to application health monitoring.

===

Elastic Beanstalk – Components (3)

---

-  Application: collection of Elastic Beanstalk components (environments, versions, configurations, etc)
-  Application Version: an iteration of your application code
-  Environment: Collection of AWS resources running an application version (only one application
   version at a time). You can create multiple environments (dev, test, prod, etc)

===

Tiers in Elastic Beanstalk

---

-  Web Server Tier
-  Worker Environment Tier

===

Deployment modes in Elastic Beanstalk (2)

---

-  Single Instance: great for dev
-  High Availability with Load Balancer: great for prod

![image](https://user-images.githubusercontent.com/1868409/133355506-441363ed-0341-4892-9a9a-f4d3094ec972.png)

===

List of (automated) Beanstalk Deployment Options for Updates (5)

---

-  All at once (deploy all in one go)
-  Rolling
-  Rolling with additional batches
-  Immutable
-  Traffic splitting

![image](https://user-images.githubusercontent.com/1868409/133355801-40743cfc-a30b-46e7-9623-53e756d2a8d4.png)

===

Explain deployment option: All at once (Elastic Beanstalk)

---

Suitable if you can accept a short loss of service, and if quick deployments are important to you. With this method, Elastic Beanstalk deploys the new application version to each instance. Then, the web proxy or application server might need to restart. As a result, your application might be unavailable to users (or have low availability) for a short time.

![image](https://user-images.githubusercontent.com/1868409/133356080-35dc8ecf-5418-4dee-a7b3-5a3ba25ac33d.png)

===

Explain deployment option: Rolling (Elastic Beanstalk)

---

It avoids downtime and minimizes reduced availability, at a cost of a longer deployment time. Suitable if you can't accept any period of completely lost service. With this method, your application is deployed to your environment one batch of instances at a time (bucket size). Bear in mind that your Application will run below capacity during a short period of time.

![image](https://user-images.githubusercontent.com/1868409/133356391-254a6815-3aea-4354-bab6-6a90132c3036.png)

===

Explain deployment option: Rolling with additional batch (Elastic Beanstalk)

---

Suitable if you must maintain the same bandwidth throughout the deployment. With this method, Elastic Beanstalk launches an extra batch of instances, then performs a rolling deployment. Launching the extra batch takes time, and ensures that the same bandwidth is retained throughout the deployment.

![image](https://user-images.githubusercontent.com/1868409/133357758-4b6059b0-0e59-4e37-bfdd-ed21607827db.png)

===

Explain deployment option: Immutable (Elastic Beanstalk)

---

A slower (and more expensive) deployment method, that ensures your new application version is always deployed to new instances, instead of updating existing instances. It also has the additional advantage of a quick and safe rollback in case the deployment fails. With this method, Elastic Beanstalk performs an immutable update where a second Auto Scaling group is launched in your environment and the new version serves traffic alongside the old version until the new instances pass health checks.

![image](https://user-images.githubusercontent.com/1868409/133532744-be010404-16b3-4c1c-b6a8-a94cd65b1b6e.png)

===

Explain deployment option: Blue/Green (Elastic Beanstalk) (2)

---

-  You can avoid any downtime by performing a blue/green deployment, where you can deploy a new version of your app to a separate environment, and then swap CNAMEs of the two environments to redirect traffic to the new version instantly.
-  You can also use Route 53 weighted policies to redirect a little bit of traffic to
   the stage environment

![image](https://user-images.githubusercontent.com/1868409/133533583-472e5ac1-bf13-45cd-ae72-277c1eb350a3.png)

===

Explain deployment option: Traffic Splitting (Canary Testing) (Elastic Beanstalk)

---

A traffic-splitting deployment allows you to monitor the health of your new application version under a configurable percentage of production traffic before completing the deployment. In case of deployment failures, a traffic-splitting deployment triggers an automatic rollback mechanism.

===

What tool should we use if we wanted to integrate our pipeline with Elastic Beanstalk?

---

We can install an additional CLI called the “EB cli” which makes working with Beanstalk from the CLI easier

===

Steps for deploying an application with Elastic Beanstalk (4)

---

-  Describe dependencies
-  Package code as zip
-  Upload the zip file, either by using the console or the CLI. In both options you need to create a new app version and deploy
-  Elastic Beanstalk will deploy the zip on each EC2 instance, resolve dependencies and start the application

===

How can we manage our Elastic Beanstalk versions? How can we prevent data loss? (2)

---

-  We can set a lifecycle policy for our versions. This policy will remove old versions based on time or a limit (e.g: do not store more than 100 app versions)
-  If you want to prevent data loss, you can enable the option of not deleting the source bundle in S3

===

Let's say you want to set up some configurations for you Elastic Beanstalk app, but you don't want to do it through the Console or CLI. You want to keep those configurations together with your code. What would you use?

---

You can add AWS Elastic Beanstalk configuration files (.ebextensions) to your web application's source code to configure your environment and customize the AWS resources that it contains. Configuration files are YAML- or JSON-formatted documents with a .config file extension that you place in a folder named .ebextensions and deploy in your application source bundle.

===

What does elastic beanstalk use under the hood?

---

Under the hood, Elastic Beanstalk relies on CloudFormation

===

Let's say we want to replicate an existing version of a Elastic Beanstalk environment, but we don't want to configure everything again, what options do we have? (2)

---

-  You can use the "Clone environment" option to launch a new environment with the exact same configuration
-  When cloning an environment, everything is preserved (load Balancer type and configuration, RDS database type, environment variables) exept for any data in the database you might have.

===

Let's say we want to change the ELB type (e.g: from NLB to ALB) of an Elastic Beanstalk environment? How can we achieve this? (4)

---

1. We cannot change the ELB type in our configurations. So we need to perform a migration.
2. To migrate, we need to create a new environment with exactly the same configurations as our target, exept for the ELB type. Cloning is not an option since it will take the same ELB.
3. Then deploy your application onto the new environment.
4. Now you can perform a CNAME swap or Route 53 update

===

What's the problem of having our RDS database coupled with an Elastic Beanstalk environment? What solutions do we have for resolving that problem?

---

RDS can be provisioned with Beanstalk, which is great for dev / test. But this is not great for prod as the database lifecycle is tied to the Beanstalk environment lifecycle, so once the envrioment is gone, your data is gone as well (that's dangerous). The best thing we can do for prod is to separately create an RDS database and attach it to our prod envrioment.

===

How can we decouple an RDS database from an Elastic Beanstalk env?

---

1. Create a snapshot of RDS DB (just in case things go wrong)
2. Go to the RDS console and protect the RDS database from deletion (another safeguard measure)
3. Create a new Elastic Beanstalk environment, without RDS, point your application to existing RDS.
4. Perform a CNAME swap (blue/green) or Route 53 update, confirm working
5. Terminate the old environment (RDS won’t be deleted since you prevented it from happening in step 2)
6. Delete CloudFormation stack (in DELETE_FAILED state)

![image](https://user-images.githubusercontent.com/1868409/133820741-d1ac6189-b897-4aad-9c5e-1b70346da640.png)

===

What options do we have for running containers in Elastic Beanstalk? (2)

---

-  You can run a Single Container docker application. If you go with this option, EB will build and run the Docker container using a Docker file, or if you prefer so, you can describe the image to use using a Dockerrun.aws.json (v1) as well as ports an other configurations. Bear in mind, This option does not use ECS
-  You can run a Multi Docker application, which will create and provision a ECS cluster. This option requires a Dockerrun.aws.json (v2) at the root of source code that is used to generate the ECS task definition. Bear in mind, your Docker images must be pre-built and stored in ECR or any other docker registy.

===

How can we use Elastic Beanstalk with HTTPS? (2)

---

-  One way is to load a SSL Certificate onto the load balancer.
-  This can be done either by using the console or from the source code (.ebextensions/securelistener-alb.config)

===

In Elatic Beanstalk, how can we redirect HTTP requests to HTTPS?

---

-  You can configure your EC2 instances to redirect HTTP to HTTPS
-  OR configure the Application Load Balancer (ALB only) with a rule

===

What's a common case to use Worker environments in Elastic Beanstalk? (2)

---

-  A common case is when your application performs tasks that are long to complete (processing a video, generating a zip file, etc).
-  Whitin your worker environment you can define periodic tasks in a file cron.yaml.

===

Let's say we want to use Elastic Beanstalk for our application, but unfortunatly the app language is not supported yet, and we cannot use Docker, what can we do?

---

Our only option is to create a Custom Platforms, so that we can define from scratch things like: OS, additional software and scripts that Beanstalk runs on these platforms

===

Steps for creating your own Custom Platform in Elastic Beanstalk (3)

---

-  Crete an AMI with all the required software and configs
-  Define an AMI using a Platform.yaml file
-  Build that platform using the Packer software (open source tool to create AMIs)

===

I would like to customize the runtime of Elastic Beanstalk and include some of my company wide security software. What should I do?

---

Provide a custom platform
