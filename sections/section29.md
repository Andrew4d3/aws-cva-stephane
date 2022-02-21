What AWS KMS does?

---

KMS (Key Management Service) is a fully-managed Key manager that provides an easy way to control access to your data.

===

Explain CMK (Customer Master Key) types in AWS KMS (2) (focus use cases)

---

-  Symmetric (AES-256 keys): used AWS services that are integrated with KMS. With this type of key, you never get access to the unencrypted Key so you must call the KMS API to use.
-  Asymmetric (RSA & ECC key pairs): it uses Public (Encrypt) and Private Key (Decrypt) pairs.
   You can use it when encryption happens outside of AWS by users who can’t call the KMS API

===

Explain AWS KMS features to manage keys (4)

---

-  Disable
-  Enable
-  Create
-  Rotation of policies

===

How can you audit the acccess to your CMKs in AWS KMS?

---

using CloudTrail

===

Explain pricing in AWS KMS for CMKs and API calls (4)

---

-  Managed Service Default CMK: free
-  User Keys created in KMS: \$1 / month
-  User Keys imported (must be 256-bit symmetric key): \$1 / month
-  You pay for API call to KMS: \$0.03 per 10000 calls.

===

What's the real value of using AWS KMS for protecting our data (secrets, volumens, DB)?

---

The value in KMS is that the CMK used to encrypt data can never be retrieved by the user, and the CMK can be rotated for extra security

===

What limitation do we have when encrypting data using KMS? (per call) What alternative do we have for work around this limitation?

---

KMS can only help in encrypting up to 4KB of data per call. If data is larger than 4 KB, use envelope encryption

===

Using KMS, how can you copy EBS snapshots between different regions? Since a CMK is tightly bound to one region.

---

When copying a snapshot to a different region. You can ask to re-encrypt the given snapshot using a CMK from the target region. KMS will first decrypt the snapshot using the CMK from the source region and then encrypt it using the CMK from the destination region.

![image](https://user-images.githubusercontent.com/1868409/154257612-6e42dc70-d83d-47a9-babe-00bab424cdcd.png)

===

What does KMS policy do? Explain types (2)

---

It controls access to KMS keys, “similar” to S3 bucket policies do. There are two types:

-  Default KMS Key Policy: created if you don’t provide a specific KMS Key Policy
-  Custom KMS Key Policy: define users, roles that can access the KMS key and who can administer it.

===

What do we neeed to remember when copying snapshot across acounts?

---

We need to remember to attach the corresponding KMS Key policy that authorizes cross-account access.

===

How does KMS work when encrypting (3) and decrypting data (3)?

---

For encrypting:

-  First you send an Encrypt API call where you indicate key to be used (using its alias)
-  KMS will first verify IAM permissions and if everything is OK, will perform the encryption
-  KMS will return the encrypted data back to the client

![image](https://user-images.githubusercontent.com/1868409/154473127-5230c4c2-2b47-4eb8-b55b-7aba66ae3355.png)

===

How does KMS work when decrypting data (3)?

---

For decrypting:

-  You send a Decrypt API call with the encrypted datablob to be decrypted. There is no need to indicate the key to use since it's already included inside the data blob.
-  KMS will check permissions again and perform the decryption if everything is OK
-  Finaly it will return the decrypted data in plain text.

![image](https://user-images.githubusercontent.com/1868409/154473127-5230c4c2-2b47-4eb8-b55b-7aba66ae3355.png)

===

When we need to encrypt data that is larger than 4 KB, what do we need to use? What KMS API call should we use?

---

We need to use Envelope Encryption. The main API that will help us is the GenerateDataKey API

===

What KMS API calls should we remember? (5)

---

-  Encrypt: encrypt up to 4 KB of data through KMS
-  Decrypt: decrypt up to 4 KB of data (including Data Encryption Keys)
-  GenerateDataKey: generates a unique symmetric data encryption key (DEK) and a copy that is encrypted under the CMK that you specify
-  GenerateDataKeyWithoutPlaintext: Generate a DEK to use at some point in the future (not immediately)
-  GenerateRandom: Returns a random byte string

===

How request quotas are handled in AWS KMS? What happens when you exceed a request quota?

---

-  The request quota is shared across all the KMS operations called in your account in one region, including requests that are made by AWS on your behalf (ex: SSE-KMS).
-  When you exceed a request quota, you get a ThrottlingException

===

How can work around the issue of exceeding request quotas in AWS KMS?

---

-  Using exponential backoff
-  For GenerateDataKey, consider using DEK caching from the Encryption SDK
-  You can request a Request Quotas increase through API or AWS support

===

What KMS API call does SSE-KMS encryption use?

---

SSE-KMS leverages the GenerateDataKey & Decrypt KMS API calls

===

What do you need to configure in S3 for allowing access to SSE-KMS encryption?

---

-  A KMS Key Policy that authorizes the user / role
-  An IAM policy that authorizes access to KMS

===

What do you need to configure in S3 to force in-transit and at-rest encryption with KMS?

---

You need to configure your bucket policy so that you deny any not secure transport and also deny any upload of an unencrypted object.

===

Let's say you want to use SSE-KMS encryption in S3, but at the same time you want to reduce the API calls you make to KMS, how can you do that?

---

You can configure to use a "S3 bucket key". What S3 does internally is to use a CMK to generate a S3 Bucket key. And then this key is used to generate multiple Data keys to encrypt your objects without calling KMS.

![image](https://user-images.githubusercontent.com/1868409/154820341-047d678d-94b9-464a-8c4f-54369669f73e.png)

===

What's the purpose of AWS SSM Parament store?

---

Its purpose is to secure storage for configuration and secrets of your AWS applications.

===

How configurations and secrets are organized with SSM Parament store?

---

They are organized in the form of a hierarchy of paths like this:

![image](https://user-images.githubusercontent.com/1868409/154820628-1ffb8b52-6da8-4e31-be22-5a8a5dc8092c.png)

===

Let's say we need to expire or force the rotation of certain secrets or configurations in our SSM Parament store. What do we need to configure?

---

We can configure a parameter policy. By doing this we can assign a TTL to a parameter (expiration date) to force updating or deleting sensitive data such as passwords. Example:

![image](https://user-images.githubusercontent.com/1868409/154820725-b20665cc-e36e-4462-8f49-b0a7159109e2.png)

===

What alternative to SSM Parament store do we have for storing secrets in AWS? (IDH)

---

AWS Secrets Manager is a service which only purpose is to store secrets. Additionaly, it's strongly integrated with RDS.

===

What are the major difference between AWS Secrets Manager and SSM Parament Store? (4) (store, KMS encryption, pricing, rotation)

---

-  Secrets manager can only store secrets while Parament store can be use for also storing configurations in plain text
-  KMS encryption is optional in Parament Store while it's mandatory in Secrets Manager
-  Parament store is way cheaper than Secrets manager
-  Rotation of passwords for RDS is strongly integrated in Secrets manager, so you don't have to code any lambda function for rotating passwords, while in SSM Parament Store there is not a native feature for rotating secrets automatically.

===

How can we automate rotation of secrets for RDS databases in SSM Parament store?

---

Since there is not a native feature in SSM parament store for doing this. You can create a Cloudwatch event to invoke a Lambda function that will update your DB password and additionally update your SSM parament store secret accordingly.

![image](https://user-images.githubusercontent.com/1868409/154867406-abf8ae49-54ae-4298-ad54-9ca5825f4b3b.png)

===

How can you encrypt your logs in AWS Cloudwatch? (IDH)

---

You can encrypt CloudWatch logs with KMS keys. This is done at the log group level by associating a CMK with a log group. Bear in mind, this only can be done using the CLI. It's not possible to do it using the UI console.
