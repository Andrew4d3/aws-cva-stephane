What's AWS SAM?

---

SAM stands for (Serverless Application Model). It's a framework for developing and deploying serverless applications in AWS

===

Some major characteristics of AWS SAM (feature, syntax support)

---

-  It can generate complex CloudFormation templates from simple SAM YAML file
-  Supports anything from CloudFormation: Outputs, Mappings,
   Parameters, Resources…

===

What commands do we use for building and deploying a SAM application? (3) (Explain what each of them does)

---

-  `sam build`: for building the application locally, this transforms the SAM templates into a CloudformationTemplate
-  `sam package`: for packagin the application, this zips and upload the application code and configuration (cloudformation) to S3.
-  `sam deploy`: for deploying the application, this will retrieve the application from S3 and create and execute the corresponding Cloudformation stack using the configuration files

![image](https://user-images.githubusercontent.com/1868409/152335040-84104be7-851a-4a42-8438-7495c531b454.png)

===

How can you run and debug your AWS SAM application locally?

---

You can use the SAM CLI Debugging tool to locally build, test, and debug your serverless applications that are defined using AWS SAM templates

===

Let's say you want to attach some common permision to your AWS SAM application (e.g: Polling from a SQS queue). But you don't want to deal directly with IAM permissions, What SAM feature can we use?

---

AWS SAM allows you to choose from a list of policy templates to scope the permissions of your Lambda functions to the resources that are used by your application.

![image](https://user-images.githubusercontent.com/1868409/152650749-18021a6a-dd13-41b4-ab85-5152bf8de012.png)

===

Where is AWS SAM built on?

---

SAM is built on CloudFormation

===

What sections does a AWS SAM template require to work?

---

SAM requires the Transform and Resources sections

===

How is CodeDeploy integrated to AWS SAM?

---

SAM framework natively uses CodeDeploy to update Lambda functions using traffic Shifting (canary releases)

===

Let's say you have a serverless application that you've developed using SAM. This application is used quite often in different environments, but you have to clone, build and package it, every single time. What AWS feature can you use to overcome this issue? (Explain details) (3)

---

-  You can use the Serverless Application Repository (SAR)
-  It's a manager for serverless applications that you can use to store and pre-build frequently used applications so that you only focus on publishing them
-  Application settings and behaviour can be customized using Environment variables

===

What resource types does AWS SAM support?

---

-  AWS::Serverless::Function
-  AWS::Serverless::Api
-  AWS::Serverless::SimpleTable
