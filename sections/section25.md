What's AWS CDK? What's its purpose?

---

CDK stands for Cloud Development Kit and its purpose is to define your AWS cloud infraestructure programatically using a familiar language like JS/TS, Python, Java, etc.

===

What's the AWS CDK programm's output? What does it compile to?

---

The code is “compiled” into a CloudFormation template (JSON/YAML)

===

Explain the workflow of generating AWS infraestructure using the CDK (3)

---

-  Using the constructors provided by the CDK you define your AWS infraestructure using a programming language
-  Using the CDK CLI, you compile (or synthetize) your code to generate a Cloudformation template
-  The Cloudformation template can be deployed to have your infraestructure running

![image](https://user-images.githubusercontent.com/1868409/152777629-670303d6-3e8b-4392-b2c8-6495571453c1.png)
