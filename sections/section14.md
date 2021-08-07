How does CloudFront works at a high level?

---

-  If the content is already in the edge location with the lowest latency, CloudFront delivers it immediately.
-  If the content is not in that edge location, CloudFront retrieves it from an origin that you've defined—such as an Amazon S3 bucket, or an HTTP server (for example, a web server) that you have identified as the source for the definitive version of your content.

![image](https://user-images.githubusercontent.com/1868409/128111253-bdd3e6af-188e-4c6e-88e2-bb3ab33058dc.png)

===

List CloudFront origins (2)

---

-  S3 as an Origin
-  ALB or EC2 as an origin

===

Let's say you have an application that needs to act in compliance with some content distribution laws. So you need to restrict its access to certain users based on their geographic location, How would you achieve this using CloudFront? (2)

---

-  Whitelist: Allow your users to access your content only if they're in one of the
   countries on a list of approved countries.
-  Blacklist: Prevent your users from accessing your content if they're in one of the
   countries on a blacklist of banned countries.

===

What's the difference between CloudFront vs S3 Cross Region Replication? (2)

---

-  Cloudfront works like a cache. You would want to use it for static content that must be available everywhere.
-  S3 Cross Region Replication updates files in real time. You would want to use it for dynamic content that needs to be available at low-latency in few regions.

===

If you want to deliver your content in S3 using Cloudfront, why is important to create an "object access identity"?

---

So that you can restrict your S3 bucket to only have access from your OAI. If you’re using an Amazon S3 bucket as the origin for a CloudFront distribution, you won’t want people to be able to view files by simply using the direct Amazon S3 URL for the file. Instead, you want them to only access the files by using the CloudFront URL.

===

How can you separate your requests to leverage CloudFront capabilities? (2)

---

-  You can separate them in static requests and dynamic requests. Static requests refer to all that content that rarely changes (images, static files etc) so you can omit to have any caching rule in place to maximize cache hits.
-  Dynamic requests refer to data that can change, like API responses from your web server. In this case, you will want to have some appropriate cache rules based on headers or cookies.

![image](https://user-images.githubusercontent.com/1868409/128345941-e5e114fa-6f51-4bcd-ada3-0d0f9b897ca1.png)

===

If we want to edit our Cloudfront caching policies, where should we go?

---

We should go to "Behaviors".

===

Which protocol policies do we have in Cloudfront? (2)

---

-  Viewer Protocol Policy: Redirect HTTP to HTTPS, or use HTTPS only
-  Origin Protocol Policy: HTTPS only or Match Viewer

![image](https://user-images.githubusercontent.com/1868409/128503703-5e5101dd-19bd-415b-a8aa-458970ca5fc1.png)

===

How do Cloudfront signed URLs work?

---

In your application, you will generate a signed url using the SDK. Then the client will use this signed url to get access to the protected resource in your distribution.

![image](https://user-images.githubusercontent.com/1868409/128505549-24b901d6-15cf-4f9f-9e91-1a00e240a43a.png)

===

What's the difference between using a S3 pre-signed url and a Cloudfront signed url?

---

-  In CloudFront, a signed URL allow access to a path. Therefore, if the user has a valid signature, he can access it, no matter the origin.
-  In S3, a signed URL issue a request as the signer user. When you sign a request, you need to provide IAM credentials, so accessing a signed URL has the same effect as that user would have done it.

===

What's the recommended way to configure Cloudfront to sign urls?

---

The recommended way is to use a Key Group. First you will need to upload the public key to Cloudfront, then you can (securely) store your private key in your Applicatin and use it to sign your urls. Cloudfron will access the Key group to verify the signature using the PubKey you uploaded.

===

Describe Price classes in Cloudfront (3)

---

1. Price Class All: all regions – best performance
2. Price Class 200: most regions, but excludes the most expensive regions
3. Price Class 100: only the least expensive regions

===

Let's say you want to direct the inbound traffic comming through the path `/api/*` to a Load balancer origin and the traffic comming through the path `/public/*` to a S3 bucket origin. How can we achieve this using Cloudfront?

---

We can use Multiple Origin to route to different kind of origins based on the content type.

===

How can we increase high availability and do fail over using Cloudfront?

---

We can configure "Origin Groups" with one primary and one secondary origin. So in case the primary origin fails, the second one is used.

![image](https://user-images.githubusercontent.com/1868409/128606379-4f6120c2-a4ba-44a2-a43e-f82057507477.png)

===

Let's say that we required to send (from our client app) some sensitive information (e.g credit card numbers) and we would like to add some extra layer of security, How can we do it using CloudFront? (2)

---

-  We can use Field Level Encryption.
-  Using asymetric encryption we can simply configure the pubkey to be used by cloudfront in an edge location. By doing this, our sensitive information can travel in a more secured way. So that it can be decrypted using the PrivateKey in our server application.

![image](https://user-images.githubusercontent.com/1868409/128606858-fffc1e9a-71e8-454b-b014-da534dae33cf.png)

===

You are hosting highly dynamic content in Amazon S3 in us-east-1. Recently, there has been a need to make that data available with low latency in Singapore. What do you recommend using?

---

Since we only need to offer low latency for **one region**, we can use S3 Cross region replication. S3 CRR allows you to replicate the data from one bucket in a region to another bucket in another region.
