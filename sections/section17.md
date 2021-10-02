Explain Tech stack for each CICD element (4)

-  Code
-  Build
-  Test
-  Deploy
-  Provision

---

-  Code: Github, AWS CodeCommit, Bitbucket, other...
-  Build and Test: Jenkins, Drone, Travis, AWS CodeBuild
-  Deploy and Provision: AWS Elastic Beanstalk or AWS Code deploy if you have your own EC2 instances fleet
-  Aditionally, all this is orchestrated using: AWS CodePipeline

===

How to allow Cross Account access with CodeCommit? (2)

---

-  First of all, you **never** share your credentials to the other users (SSH key or username and password).
-  The right way is to configures appropriate IAM Roles in both of your AWS Accounts and then use AWS STS (with AssumeRole API)

===

Let's say you have a code repo in CodeCommit and you would like to trigger some notifications when something happens in this repo (e.g: a branch deletion, pushes in master branch, etc). How can you achieve this?

---

-  You can trigger notifications in CodeCommit using AWS SNS (Simple Notification Service) or AWS Lambda or AWS CloudWatch Event Rules. This works for pushes to branches and branch deletions.
-  For pull requests (creation, updates, deletions, comments) you can use CloudWatch Event Rules.

===

What are stages in CodePipeline? (2)

---

-  CodePipeline is made of stages. Each of these stages can have sequential actions and / or parallel actions. Examples of stages can be: Build, test, deploy, load test etc.
-  You can define Manual approvals for any stage

===

Explain Artifacts in CodePipeline (2)

---

-  Any stage can have a series of input and output files in the form of "artifacts". These files might be anything you want, ranging from your zipped source code to your compiled binaries.
-  Artifacts are passed to be stored in Amazon S3 and then passed on to the next
   stage.

![image](https://user-images.githubusercontent.com/1868409/133934519-7c6635ad-2c60-46e8-92b6-717fc633118e.png)

git clone https://andrew-at-045366617141:YnBBD9zFYY7ytD+RBT+GkeVf+Ie2M0/GO8W9LXP9TlY=@git-codecommit.us-east-1.amazonaws.com/v1/repos/my-first-aws-repo

===

What file does AWS Codebuild use to define instructions to run?

---

Build instructions can be defined in code (buildspec.yml file)

===

When it comes to detecting failed build or notifying thresholds of failures. What AWS service Codebuild uses? (2)

---

-  Use CloudWatch Events to detect failed builds and trigger notifications
-  Use CloudWatch Alarms to notify if you need “thresholds” for failures

===

In summary, How does AWS Codebuild work? (3)

---

-  Everything happens inside a Codebuild Container
-  The Codebuild container pulls the source code from your repo and runs the instructions in the buildspec.yml file
-  Codebuild uses S3 for caching and storing any artifact produced.

![image](https://user-images.githubusercontent.com/1868409/134603082-206e23aa-1c31-4036-92b7-bea6c11ecef2.png)

===

What does a buildspec.yml (AWS Codebuild) contain?

---

It contains what you're expected to find in any other pipeline file from any other provider (Jenkins, Travis, Drone, etc). Enviroment variables, phases (install, test, build, etc), artifacts.

===

How to store your secrets with AWS Codebuild?

---

You should use SSM Parameter store. Never store your secrets in Code!

===

What's the default behaviour of CodeBuild and VPC? Is it possible to change?

---

-  By default, your CodeBuild containers are launched outside your VPC. Therefore, by default it cannot access resources in it
-  If you want to access resources in your VPC (RDS, ElastiCache, ALB, etc), you will need to specify a VPC configuration for your Build project (VPC ID, subnets, security groups). This is neccessary when you need to run a query for integration tests, or access your ALB.

===

When do we use AWS CodeDeploy

---

We use it when We want to deploy our application automatically to many EC2 instances and these instances are not managed by Elastic Beanstalk.

===

What requirements do we need to make AWS CodeDeploy work? (2)

---

-  Each EC2 Machine (or On Premise machine) must be running the CodeDeploy Agent
-  An appspec.yml file should be at the root of your code repository. This file used by CodeDeploy to manage a deployment

===

How does AWS CodeDeploy work? (4)

---

![image](https://user-images.githubusercontent.com/1868409/135184578-5f411cbe-8c56-4b90-96b5-cb42f7cfd974.png)

===

One way CodeDeploy and CodePipeline can be combined

---

CodeDeploy can be chained into CodePipeline and use artifacts from
there

===

Some restrictions of CodeDeploy (2)

---

-  Note: Blue / Green only works with EC2 instances (not on premise)
-  CodeDeploy does not provision resources

===

AWS CodeDeploy AppSpec order of Hooks (6)

---

-  ApplicationStop
-  DownloadBundle
-  BeforeInstall
-  AfterInstall
-  ApplicationStart
-  ValidateService: really important

===

Deployment types in AWS CodeDeploy (2)

---

-  In place deployment - Half at a time
   ![image](https://user-images.githubusercontent.com/1868409/135185783-8c174c30-f241-4a5a-bab0-dfd509eefbd2.png)

-  Blue Green Deployment
   ![image](https://user-images.githubusercontent.com/1868409/135185843-375ab398-5f48-4398-a6b9-804bd19d753f.png)

===

Steps for configuring CodeDeploy in AWS

---

1. Create a CodeDeploy role (you can use the helper)
2. Create a second IAM role with S3 read only access (to pull the code from S3)
3. Create the CodeDeploy application. Here you need to define the compute platform (EC2/On-premise, Lambda or ECS)
4. Launch an EC2 instance with the IAM role you created at step 2
5. Log into your EC2 instance using SSH and install the CodeDeploy agent by running these commands: [Link](https://gist.github.com/Andrew4d3/122a7b5b41350df421c9476cdefafaa0)
6. Add a tag to your EC2 instance. Example: Environment - dev
7. Create a Deployment group. Here you need to set the role you created at the first step and the tags you created at the previous step
8. Now you can create your first deployment. Here you need to indicate the deployment's source (Github or S3). Remember your code should have a appspec.yml file at the root level.
9. If everything goes well, all the hooks should run and your application will end up beying deployed

===

How does CodeDeploy work with ASG when performing a Blue/Green deployment? (3)

---

-  First a new ASG is created with all the settings being copied
-  Then, an ELB will split traffic between your Old version ASG and your new versiong ASG
-  Your old version ASG will be alive for a determined period of time chosen by yourself.

===

What happens when a roll back occurs in CodeDeploy?

---

If a roll back happens, CodeDeploy redeploys the last known good revision as a new deployment.

===

Explain AWS CodeStar

---

CodeStar is an integrated solution that regroups in a single dashboard: GitHub, CodeCommit,
CodeBuild, CodeDeploy, CloudFormation, CodePipeline, CloudWatch

===

You would like to send email alerts each time developers open pull requests or comments are added to commits in your CodeCommit repository. Which AWS service helps you in doing that?

---

AWS CloudWatch Events

===

Your colleague has an IAM user in another AWS Account who wants to access your CodeCommit repository. What should you do?

---

Create an IAM role in your AWS account with the required permissions, then tell him to use STS cross-account access to assume this IAM role

===

A CodePipeline pipeline that you manage just failed to deploy your code to Elastic Beanstalk even though the code has been pushed successfully to your CodeCommit repository. The pipeline just working fine 10 minutes ago. What is most likely the reason for this?

---

Your CodeBuild stage probably failed some tests

===

You're using CodeBuild to build your application as part of the CICD process. The build process takes a long time, so you investigated this and found that 15 minutes at each build is spent on pulling dependencies from remote repositories. What should you do to drastically speed up the build process?

---

Modify the buildspec.yml to enable Dependencies Caching in Amazon S3

===

Which Lifecycle Event hook should be used in the appspec.yml file to ensure the application is properly running after being deployed?

---

ValidateService
