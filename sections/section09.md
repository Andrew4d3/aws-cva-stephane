What's Amazon S3?

---

Amazon Simple Storage Service (Amazon S3) has a simple web services interface that you can use to store and retrieve any amount of data, at any time, from anywhere on the web.

===

In Amazon S3, What's a bucket and what's an object?

---

Amazon S3 allows people to store "objects" (files) in “buckets” (directories)

===

Three major characteristics for S3 buckets.

---

-  They are defined at the region level
-  They should have a globally unique name
-  The should meet the following naming convention (no need to memorize):
   -  No uppercase
   -  No underscore
   -  3-63 characters long
   -  Starting with a lowecase letter or a number

===

When it comes to objects in S3, what do we use to indentify them? (2)

---

-  A key is the unique identifier for an object within a bucket.
-  This is the full path that we use to access the file. For example, in the URL https://doc.s3.amazonaws.com/2006-03-01/AmazonS3.wsdl, "doc" is the name of the bucket and "2006-03-01/AmazonS3.wsdl" is the key.

===

When it comes to the size of objects in S3, what should we keep in mind? (2)

---

-  The Max Object Size is 5TB
-  If uploading more than 5GB, must use “multi-part upload”

===

What additional info we can assosiate to our objects in S3? (3)

---

-  Metadata (list of key / value pairs – system or user metadata)
-  Tags ( up to 10)
-  Version ID (if versioning is enabled)

===

What does it happen when we have S3 versioning enabled? (3)

---

-  if you delete an object, Amazon S3 inserts a delete marker instead of removing the object permanently. The delete marker becomes the current object version.
-  If you overwrite an object, it results in a new object version in the bucket.
-  You can always restore the previous version.

===

In S3, what does it mean when an object has "null" as version?

---

It means the file was added when S3 versioning was disabled.

===

When Object versioning is enabled in S3, How can roll back an unintended object deletion?

---

We only need to delete the "Delete marker" that was assigned to the object when the deletion took place.

===

Four methods of encrypting objects (at rest) in S3.

---

-  SSE-S3: encrypts S3 objects using keys handled & managed by AWS
-  SSE-KMS: leverage AWS Key Management Service to manage encryption keys
-  SSE-C: when you want to manage your own encryption keys
-  Client Side Encryption

===

Explain SSE-S3 (S3 encryption) (2)

---

-  Server-side encryption protects data at rest using a unique key that is managed by AWS.
-  It will use a 256-bit Advanced Encryption Standard algorithm (AES-256).

===

Explain SSE-KMS (S3 encryption) (2)

---

-  It's similar to SSE-S3, but with some additional benefits and charges for using this service.
-  Among the benefits, There are separate permissions for the use of a CMK (Customer Master Key) that provides additional protection against unauthorized access of your objects in Amazon S3. SSE-KMS also provides you with an audit trail that shows when your CMK was used and by whom.

===

Explain SSE-C (S3 encryption) (2)

---

-  Here, you manage the encryption keys and Amazon S3 manages the encryption, as it writes to disks, and decryption, when you access your objects.
-  Using this method encryption keys must be provided in the HTTP headers, for every HTTP request made and using HTTPS.

===

Explain Client Side Encryption (S3) (3)

---

-  Encryption is performed on client side using libraries such as Amazon S3 Encryption Client.
-  The client should encrypt the data before sending it and decrypt the data once is received.
-  As well as SSE-C, the responsability for managing and storing keys is on the client.

===

How does S3 handle encryption in transit?

---

-  Amazon S3 exposes a HTTPS endpoint for accessing your objects with an encrypted conection.
-  It also exposes a HTTP endpoint (not encrypted) that you can use. But it's recommended to always use HTTPS

===

Which two types of S3 security do we have in S3?

---

-  User based: via IAM policies, it defines which API calls should be allowed for a specific user from IAM using the AWS console.
-  Resource Based: Bucket policies and Object/Bucket ACL (Access control list)

===

When can an IAM principal access a S3 object? (3)

---

An IAM principal can access a S3 object if the user IAM permissions allow it OR the resource policy ALLOWS it AND there’s no explicit DENY

===

What does a S3 Bucket policy usually contain? (4)

---

-  Resources: Buckets, objects are the Amazon S3 resources for which you can allow or deny permissions
-  Actions: For each resource, Amazon S3 supports a set of operations. You identify resource operations that you will allow (or deny) by using action keywords. For example, the `s3:ListBucket` permission allows the user to use the Amazon S3 GET Bucket (List Objects) operation.
-  Effect: What the effect will be when the user requests the specific action.This can be either allow or deny.
-  Principal: is the account or user to apply the policy to

===

Three use cases for using S3 bucket policies:

---

-  Grant public access to the bucket
-  Force objects to be encrypted at upload
-  Grant access to another account (Cross Account)

===

What 4 ways do we have to block public access to our S3 buckets and objects?

---

-  Block public access to buckets and objects granted through:
   -  new access control lists (ACLs)
   -  any access control lists (ACLs)
   -  new public bucket or access point policies
-  Block public and cross-account access to buckets and objects through any public bucket or access point policies

===

What S3 Security options do we have for networking, loging and audit (3)

---

-  Networking: Supports VPC Endpoints
-  Logging and Audit:
   -  S3 Access Logs can be stored in other S3 bucket
   -  API calls can be logged in AWS CloudTrail

===

What 2 security features can we use for handling our files in S3? (User security)

---

-  MFA Delete: MFA (multi factor authentication) can be required in versioned
   buckets to delete objects
-  Pre-Signed URLs: URLs that are valid only for a limited time (ex: premium video service for logged in users)

===

Let's say we want to enforce all of our uploads in S3 to be encrypted. How can we create a bucket policy for this scenario? (3)

---

-  Choose the target bucket.
-  Expand Permissions in the right panel, and choose Edit bucket policy.
-  Copy the following policy, paste it in that bucket policy box, and then click Save. (Throughout this solution, be sure to replace <bucket_name> with the actual name of your bucket.) [Link](https://aws.amazon.com/blogs/security/how-to-prevent-uploads-of-unencrypted-objects-to-amazon-s3/)

===

When using S3 for hosting static websites, what should we do to avoid getting a 403 forbidden error? (2)

---

(1) You must disable block public access settings for the bucket (2) and write a bucket policy that grants public read access

[Link](https://docs.aws.amazon.com/AmazonS3/latest/userguide/WebsiteAccessPermissionsReqd.html#bucket-policy-static-site)

===

What do we need to enable when a client does a cross-origin request on our S3 bucket?

---

we need to enable the correct CORS headers

===

When using S3, when do we get eventual consistency in our objects?

---

-  For new objects: if we try to retrieve a non-existant object and then we create it (PUT 200). We might get a 404 for subsequent retrieving calls for this object (we would need to wait some seconds to have it fully available)
-  For existing objects: when we update or delete existent objects we might get the old version of those objects for some short period of time.

===

Is there a way to have full consistency within our S3 objects?

---

No, there's no way. That's how S3 works.
