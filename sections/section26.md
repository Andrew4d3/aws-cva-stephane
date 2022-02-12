When do we use AWS Cognito?

---

We use it when we want to give our users an identity so that they can interact with our
application.

===

List of Cognito services (3, one is deprecated)

---

-  Cognito User Pools
-  Cognito Identity Pools (Federated Identity)
-  Cognito Sync (deprecated and replaced with AppSync)

===

What type of logins do Cognito user pools (CUP) have? (2)

---

-  Simple login: Username (or email) / password combination
-  Federated Identities: users from Facebook, Google, SAML…

===

What feature do Cognito user pools (CUP) offer? (password, verification, authentication, security)

---

-  Password reset
-  Email & Phone Number Verification
-  Multi-factor authentication (MFA)
-  Block users if their credentials are compromised elsewhere

===

Explain at high level details how Cognito user pools (CUP) work (3)

---

-  Cognito keeps a serverless database of user for your web & mobile apps
-  Your client will login to your application using CUP (either by simple or federation login)
-  Then CUP will return a JWT that your application can use to authorize resources

![image](https://user-images.githubusercontent.com/1868409/152975082-e6cb900f-eb9a-46c3-b3ce-d46bf35464a1.png)

===

In which events can our AWS CUP invoke a Lambda function? (Lambda triggers) (3)

---

-  Authentication Events: for pre or post authentication you can validate to accept or deny sing-in request or log data.
-  Sign-Up events: for pre or post sign-up you can validate to accept or deny sign-up requests or add custome welcome messages or event logging.
-  Token Creation: for pre token generation you can add or remove attributes in your tokens.

===

How can we customize our login screen in AWS CUP?

---

Cognito has a hosted authentication UI that you can add to your app to handle signup and sign-in workflows and customize with a custom logo and custom CSS

===

Explain how Cognito Identity Pools work (3)

---

-  Our client application will attempt to login with some credentials to our CUP or any 3rd party IdP (FB, Google, SAML, OpenID). If login succeeds, it will get a token
-  Our client will send this token to a Cognito Identity Pool, it will validate the token with the 3rd party provider or CUP, and if validation passes it will obtain some IAM temporary credentials using STS
-  CIP will send back those credential to the client, which will use to directly access AWS resources (S3, DynamoDB tables)

![image](https://user-images.githubusercontent.com/1868409/153187535-ddb3d706-8882-41dd-954e-f649c674669c.png)

===

How does Cognito identiy pool allow access to AWS resources? (3)

---

-  You get temporary IAM credentials using STS that belong to a specific IAM role
-  You can define default IAM roles for authenticated and guest users
-  You can also partition your users’ access using policy variables

![image](https://user-images.githubusercontent.com/1868409/153189865-d5deea61-03fc-420b-a5f4-96237d121d0d.png)

===

What's the biggest difference between CUP and CIP?

---

-  CUP manages a database for your users and it does't assign any IAM policy to your logged users.
-  CIP doesn't manage any users database since the loging process is delegated to other authetication services (CUP included). What it does is to map IAM roles and policies to logged or guest users.

===

Explain what CognitoSync does, is there a better alternative? (IDH)

---

-  Amazon Cognito Sync is an AWS service and client library that enables cross-device syncing of application-related user data. You can use it to synchronize user profile data across mobile devices and the web without requiring your own backend.
-  This service is soon to be deprecated, so it's better to use AppSync
