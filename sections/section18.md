What's CloudFormation? and What problems does it resolve?

---

CloudFormation is a declarative way of defining your AWS Infrastructure, for any resources (most of them are supported). With CloudFormation we avoid doing a lot of manual work by having all of our infrastructure described using Code (IaC).

===

Benefits of AWS CloudFormation (5)

---

-  Infrastructure as code: The code can be version controlled for example using git
-  Cost: Each resources within the stack is tagged with an identifier so you can easily see how
   much a stack costs you
-  Productivity: Ability to destroy and re-create an infrastructure on the cloud on the fly
-  Separation of concern:create many stacks for many apps, and many layers (VPC, Network, App)
-  Reusability: You can leverage existing templates on the web

===

Where are CloudFormation templates uploaded?

---

Templates have to be uploaded in S3 and then referenced in
CloudFormation

===

What do we need to do to update an existing CloudFormation Template?

---

To update a template, we can’t edit previous ones. We have to reupload a new version of the template to AWS

===

What happens when we delete a Cloudformation stack?

---

Deleting a stack deletes every single artifact that was created by
CloudFormation.

===

Ways to deploy a Cloudformation Template (2)

---

-  Manual Way: Editing templates in the CloudFormation Designer
-  Automated way: Editing templates in a YAML file and using the AWS CLI (Command Line Interface) to deploy the templates. This is the recommended way when you fully want to automate your flow

===

In AWS CloudFormation, what are Resources? (3) (IDH: Important detail here)

---

-  Resources represent the different AWS Components that will be created and configured.
-  Each resource can have one or several properties (optinals and required)
-  It's a Mandatory component in your cloudformation template. (ID)

===

What's the format of a Resource type?

---

```
AWS::aws-product-name::data-type-name
```

===

In AWS CloudFormation, Can I create a dynamic amount of resources?

---

No, you can’t. Everything in the CloudFormation template has to be declared. You can’t perform code generation there.

===

Is every AWS Service supported in AWS CloudFormation?

---

Almost. Only a select few niches are not there yet. But you can work around that using AWS Lambda Custom Resources

===

What AWS coudl we use if we wanted an Artifact manager for our pipelines and builds?

---

AWS CodeArtifact is a fully managed artifact repository service that makes it easy for organizations to securely store, publish, and share software packages used in their software development process.

===

What can you do with Parameters in CloudFormation?

---

-  Parameters are a way to provide inputs to your AWS CloudFormation template
-  They’re important to know about if: You want to reuse your templates across the company or
   some inputs can not be determined ahead of time

===

When should you use a parameter in CloudFormation?

---

Ask yourself this: Is this CloudFormation resource configuration likely to change in the future and it needs to be entered by the user? If so, make it a parameter.

===

How to Reference a Parameter in a Cloudformation template?

---

The shorthand for this in YAML is !Ref

Example:

```
Parameters:
  SecurityGroupDescription:
    Description: Security Group Description
    Type: String

Resources:
  ServerSecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: !Ref SecurityGroupDescription
      SecurityGroupIngress:
      - IpProtocol: tcp
        FromPort: 80
        ToPort: 80
        CidrIp: 0.0.0.0/0
      - IpProtocol: tcp
        FromPort: 22
        ToPort: 22
        CidrIp: 192.168.1.1/32
```

===

What are Pseudo-parameters in Cloudformation?

---

Pseudo parameters are parameters that are predefined by AWS CloudFormation. With them you can access information like region, account-id, stackname, etc.

===

What AWS CodeGuru do?

---

It's an Maching Learning powered service for automated code reviews and application performance recommendations.

===

Functionalities in AWS CodeGuru and their purpose (2)

---

-  CodeGuru Reviewer: helps you identify critical issues, security
   vulnerabilities, and hard-to-find bugs using ML based on hard-learned lessons across millions of
   code reviews on 1000s of open-source and Amazon repositories
-  CodeGuru Profiler: Helps understand the runtime behavior of your application like (e.g)
   identifying if your application is consuming excessive CPU capacity on a logging routine or detecting any other anomaly

===

What are mappings in AWS Cloudformation?

---

Mappings are hard-coded constants within your CloudFormation Template. Example:

![image](https://user-images.githubusercontent.com/1868409/135774587-091670f6-d108-4116-b9f4-2f6e2053ac49.png)

===

When would you use mappings vs parameters? (AWS Cloudformation) (2)

---

-  Use mappings with fixed values (or constants) that seldom change
-  Use parameters with values that need to be entered by the user (when launching the stack)

===

How to reference a mapping value in Cloudformation?

---

We use `Fn::FindInMap` to return a named value from a specific key or the shorthand `!FindInMap [ MapName, TopLevelKey, SecondLevelKey ]`. Example:

![image](https://user-images.githubusercontent.com/1868409/135775205-5d3b1e45-4dde-4ca6-b78e-48ce320af198.png)

===

What are outputs in AWS Cloudformation? (with example)

---

Outputs let you declare values that can be import by other stacks. For example: if you want to define a network CloudFormation, and want to output the variables such as VPC ID and your Subnet IDs so that they can be used by other stacks.

===

If you try to delete a cloudformation stack that's using variables exported by other stack (in the form of outputs). What might happen?

---

You can’t delete a CloudFormation Stack if its outputs are being referenced by another CloudFormation stack so AWS will refuse such a action.

===

How to use Cloudformation outputs? (2)

---

-  First we need to declare the Output in our first stack (origin):
   ![image](https://user-images.githubusercontent.com/1868409/135775544-00e9ccc4-042e-4e50-bce0-79fe945fefde.png)
   For this example, here we're exporting the security group `MyCompanyWideSSHSecurityGroup` as an output
-  Then, in our second stack, we can reference our output by using the `!ImportValue` shorthand:
   ![image](https://user-images.githubusercontent.com/1868409/135775633-9b0cc616-941d-467d-86dc-3328369caf54.png)

===

What do we use conditions for? (in AWS Cloudformation)

---

We use condition for add extra logic to the creation of our stack resources. Example:

![image](https://user-images.githubusercontent.com/1868409/135948394-175b0cc6-299e-455f-899d-6fd52045fc1a.png)
![image](https://user-images.githubusercontent.com/1868409/135948436-9bf89194-7191-4015-9d48-7313f2143144.png)

So for this example, we will only mount such a volume if the condition `CreateProdResources` is met.

===

How do the `Fn::Ref` function work for parameters and and resources? (AWS Cloudformation)

---

-  Parameters: returns the value of the parameter
-  Resources: returns the physical ID of the underlying resource (ex: EC2 ID)

===

What intrinsic cloudformation function should we use to get specifc attributes from resources?

---

The `Fn::GetAtt` or its shorthand `!GetAtt`. Example:

![image](https://user-images.githubusercontent.com/1868409/136484174-d68d8112-49ae-4828-9498-eed0c5b06734.png)

===

What intrinsic cloudformation function should we use for joining values?

---

The `Fn::Join` or its shorthand `!Join`. Example:

![image](https://user-images.githubusercontent.com/1868409/136484437-f11b7a4f-d62d-4fa2-8f0b-86e55e5e0dd6.png)

===

What intrinsic cloudformation function should we use for substituting values in a text?

---

`Fn::Sub`, or `!Sub` as a shorthand, is used to substitute variables from a
text. It’s a very handy function that will allow you to fully customize your
templates. Example:

```yaml
Name: !Sub
   - www.${Domain}
   - { Domain: !Ref RootDomainName }
```

===

What intrinsic cloudformation functions should we use for adding logic to our templates?

---

The intrinsic function (logical) can be any of the following:

-  Fn::And
-  Fn::Equals
-  Fn::If
-  Fn::Not
-  Fn::Or

Example:

![image](https://user-images.githubusercontent.com/1868409/136485037-95d5004e-bc3a-4a62-a56c-b7e1305a75a9.png)

===

What might happen when stack **Creation** fails in AWS cloudformation? (2)

---

-  By default everything rolls back (your resources get deleted) and we can look at the lock to see what happened
-  If you want to troubleshot to see what happened, you can disbale the automatic rollback

===

What might happen when stack **Update** fails in AWS cloudformation?

---

The stack will automatically roll back to the previous known working state and you could see in the log what happened through error messages.

===

When you need to update a stack, understanding how your changes will affect running resources before you implement them can help you update stacks with confidence. What Cloudformation feature do we use for just that?

---

**Change sets** allow you to preview how proposed changes to a stack might impact your running resources, for example, whether your changes will delete or replace any critical resources.

===

What are Nested stacks in Cloudformation?

---

Nested stacks are stacks as part of other stacks. They allow you to isolate repeated patterns / common components in separate stacks and call them from other stacks which is a good practice.

===

What's the difference between Cross-stacks vs Nested Stacks? (2)

---

-  Cross-stack is when you pass or share values to other stacks in the form of imported/exported values. (Example: VPC ids, etc)
-  You use a nested stack when you want to reuse a common template between multiple stacks, and thus, avoid duplication of code (copy&paste). Example (resusing a common Load balancer configuration with multiple stacks). This nested stack is only visible to the parent stack and it's not shared with anything else

===

If you wanted to create, update, or delete Cloudformation stacks across multiple accounts and Regions with a single operation. What AWS feature would you use?

---

**AWS CloudFormation StackSets**: Using an administrator account, you define and manage an AWS CloudFormation template, and use the template as the basis for provisioning stacks into selected target accounts across specified AWS Regions.

===

When working with Cloudformation, once our stacks are created or updated, they can still suffer manual modifications (configuration drift). What feature do we have for handling that situation?

---

To detect manual changes we can use Cloudformation Drift. This feature will show any difference your current stack might have with his original state.

===

List of AWS Cloudformation pseudo parameters (8)

---

-  AWS::AccountId
-  AWS::NotificationARNs
-  AWS::NoValue
-  AWS::Partition
-  AWS::Region
-  AWS::StackId
-  AWS::StackName
-  AWS::URLSuffix

===

Before a CloudFormation template can be used by CloudFormation, it must be uploaded to?

---

CloudFormation references a template from Amazon S3, no matter what. If you upload the template from the AWS console, it gets uploaded to Amazon S3 behind the scenes, and CloudFormation references that template from there.

===

When you write a CloudFormation template, you must specify the order in which CloudFormation should create your resources. (True or False)

---

False

===

Which CloudFormation intrinsic function allows you to retrieve the DNS name of an Application Load Balancer created with CloudFormation?

---

Fn:GetAtt
