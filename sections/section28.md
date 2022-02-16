What's the purpose of AWS STS?

---

Allows to grant limited and temporary access to AWS resources (up to 1 hour).

===

Explain API calls for assuming role in AWS STS (3) (IDH)

---

-  AssumeRole: Assume roles within your account or cross account
-  AssumeRoleWithSAML: return credentials for users logged with SAML
-  AssumeRoleWithWebIdentity: return creds for users logged with an IdP (Facebook Login, Google Login, OIDC compatible…). However, AWS doesn't recommend using this, and use Cognito Identity Pools instead

===

Explain API calls for getting temporary credentials (token) in AWS STS (2)

---

-  GetSessionToken: for MFA, from a user or AWS account root user
-  GetFederationToken: obtain temporary creds for a federated user

===

In AWS STS, what API call do we use for getting details about the IAM user or role?

---

GetCallerIdentity return details about the IAM user or role used in the API call

===

In AWS STS, what API call do we use for decoding error messages when an API call is denied?

---

DecodeAuthorizationMessage decodes error messages when an API call is denied

===

Explain how to Assume a Role using AWS STS (3)

---

-  First you need to define the target IAM Role within your account or cross-account
-  Then you need to define which principals can access this IAM Role
-  Then by using a Assume role API cal you can retrieve credentials and impersonate the IAM Role you have access to

![image](https://user-images.githubusercontent.com/1868409/153856976-d152ad07-42b6-45ad-9336-8ed118647de3.png)

===

Explain the IAM Authorization Model of evaluation of Policies

---

Any explicit Deny will take precedence over any explicit allow. If there is not an allow statement for the given resource, then permission is denied.

![image](https://user-images.githubusercontent.com/1868409/154155014-727c5927-f625-44d4-b95b-ae8c37102881.png)

===

How do IAM policies work in conjunction with S3 Bucket policies?

---

When evaluating if an IAM Principal can perform a specific operation on a
bucket, the union of its assigned IAM Policies and S3 Bucket Policies will
be evaluated.

===

Let's say we have the following case:

-  IAM Role attached to EC2 instance, explicit deny S3 bucket permissions
-  S3 Bucket Policy attached, explicit RW allow to the IAM Role

Can this EC2 instance write or not to the corresponding bucket?

---

EC2 instance cannot read and write to “my_bucket” since there is an explicit deny and we're evaluating both policies combined.

===

Let's say we want to allow a group of users to only write to their corresponding folder in a S3 bucket. How do you assign each user a /home/\<user\> write permission in an S3 bucket?

(2 option, one is naive)

---

-  Option 1 (naive): create an IAM policy for each user where the folder to write is /home/\<username\>. Example: home/andrew
-  Option 2: Create a dynamic policy with IAM where you can leverage the special policy variable: \${aws:username}

===

Explain types of Policies in IAM (3)

---

-  AWS Managed Policy: Maintained by AWS
-  Customer Managed Policy: created by users
-  Inline: Strict one-to-one relationship between policy and principal. So if IAM principal is deleted, Policy will be deleted too.

===

When configurin an AWS Service, sometimes this service might need some permissions in the form or Roles, how are those roles assigned by the user?

---

To configure AWS services, you must **pass an IAM role** to the service
(this happens only once during setup) then the service will later assume the role and perform actions.

===

What permission an IAM user should have to pass a role? (IDH)

---

For this, you need the IAM permission iam:PassRole, it oftem comes with iam:GetRole to view the role being passed

===

Can a role be passed to any AWS service?

---

No, Roles can only be passed to what their trust policy allows. So an AWS service can only assume the role that its corresponding trust policy allows

===

Explain ways to set up a MS Active Directory in AWS (3)

---

-  AWS Managed Microsoft AD: where you can create your own AD in AWS and also establish "trust" connection with your on-premise AD

![image](https://user-images.githubusercontent.com/1868409/154159946-b30acefb-7e67-41c3-ab21-9ef64ccc8e4d.png)

-  AD Connector: where you redirect to on-premise AD

![image](https://user-images.githubusercontent.com/1868409/154160101-30b48831-a069-4168-81ac-a438820984db.png)

-  Simple AD: AD-compatible managed directory on AWS that cannot be joined with on-premise AD
