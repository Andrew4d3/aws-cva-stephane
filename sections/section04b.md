What's the least privilege principle that you have to apply when working with AWS IAM?

---

In AWS you apply the least privilege principle: don’t give more permissions than a user needs.

===

In AWS, what's a policy?

---

A policy is a JSON document that defines the permissions of a given user or role

===

At the root level, what does an IAM policy consist of? (optionals and required) (3)

---

-  Version: policy language version (required)
-  Id: an identifier for the policy (optional)
-  Statement: one or more individual statements (required)

===

What does the Statement block from an IAM policy consist of? (optionals and required) (6)

---

-  Sid: an identifier for the statement (optional)
-  Effect: whether the statement allows or denies access (Allow, Deny) (required)
-  Principal: account/user/role to which this policy applied to (required)
-  Action: list of actions this policy applies the effect (allow or deny) (required)
-  Resource: list of resources to which the actions applied to (required)
-  Condition: conditions for when this policy is in effect (optional)

![image](https://user-images.githubusercontent.com/1868409/125171944-f22e8600-e184-11eb-8ccc-6cb86491de49.png)

===

List of MFA devices options in AWS (4)

---

-  Virtual MFA device: Google Authenticator or Authy
-  Universal 2nd Factor (U2F) Security Key: YubiKey by Yubico (3rd party)
-  Hardware Key Fob MFA Device: Provided by Gemalto
-  Hardware Key Fob MFA Device for AWS GovCloud (US): Provided by SurePassID (3rd party)

===

What IAM security tool can we use if we want to revise our user policies?

---

IAM Access Advisor: shows the service permissions granted to a user and when those
services were last accessed.

===

What IAM security tool can we use if we want to check the status of our users credentials?

---

IAM Credentials Report: is a report that lists all your account's users and the status of their various credentials

===

Some guidelines and best practices for IAM (6)

---

-  Don’t use the **root account** except for the initial AWS account setup
-  Always keep one **AWS user per phisical user**. Do not have multiples phisical users sharing one AWS user
-  Always prefer to assign permisions to **groups** instead of assigning permisions to users.
-  Create a **strong password policy**
-  Enforce the use of **MFA**
-  If you need to audit permissions, use the **IAM Credentials Report**
