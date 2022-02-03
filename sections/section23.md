What type of services can we integrate with API Gateways? (3)

---

-  Lambda Functions: it's an easy way to expose a REST API backed by AWS Lambda
-  Custom HTTP APIs: you can expose HTTP endpoints in the backend, either by using an internal HTTP server (ec2 or on premise) or an ALB
-  AWS Service: you can expose different AWS services like SQS, StepFunctions in order to add authentitacion, rate control, etc.

===

Explain Endpoint types for API Gateways (3)

---

-  Edge-Optimized (default): requests are routed through the CloudFront Edge locations (to improve latency) but the API Gateway still lives in only one region
-  Regional: if you're clients are only in one region. You could also manually combine with CloudFront in order to have more control over caching.
-  Private: to only be accessed from your VPC using an interface VPC endpoint (ENI).

===

What do we need to do in order to make our API gateway changes go live? (Explain details) (IDH) (3)

---

-  You need to make a “deployment” for your API Gateway changes to be in effect.
-  Changes are deployed to “Stages” (as many as you want) you can use the naming you like for them (dev, test, prod) and its own configuration parameters for each one of them
-  You can rollback your stage changes since a history of deployments is kept

===

In API Gateways, What's a Stage Variable?

---

Stage variables are like environment variables for API Gateway. You can use them to set often changing configuration values

===

Let's say we want to create multiple paths for multiple environments that at the same time point to multiple lambda aliases, how do we achieve this with API gateways? (3)

![image](https://user-images.githubusercontent.com/1868409/151541603-c78ed867-19f7-4b8e-9c79-99c57a05042f.png)

---

-  We need to use a stageVariable. Once we have all of our Lambda versions and aliases created, we need to configure the corresponding API Gateway method (e.g: GET)
-  In the Lambda function field, we need to do something like this: `api-gateway-demo:${stageVariables.lambdaAlias}`. "lambdaAlias" could be any name we want for our variable name and "api-gateway-demo" is the name of our lambda function.
-  Then we need to create a stage, and set the corresponding stage variable that we defined at the previous step (lambdaAlias)

===

Let's say you want to shift certain percentage of production traffic to the new version, since you don't want to do it all at once, How can you achieve this with AWS API gateways?

---

In API Gateway you have the possibility to enable canary deployments for any stage (usually prod)

===

Explain Integration Types for AWS API Gateways (4)

---

-  MOCK: returns a response without sending the request to the backend (good for development)
-  HTTP / AWS: where you can transform the request and response by using mapping templates
-  AWS_PROXY (Lambda Proxy): only redirect requests and responses to their corresponding destinations (lambda function or client) without applying any transformation
-  HTTP_PROXY: the same as AWS_PROXY but this time for custom HTTP endpoints (EC2, ECS, On Premise)

===

In API Gateways, What can you do with Mapping Templates? (AWS & HTTP Integration) (4)

---

Mapping templates can be used to modify request / responses as follows:

-  Rename or Modify query string parameters
-  Modify body content
-  Add or remove headers
-  Filter output results (remove unnecessary data)

===

A good use case for how to use mapping templates in AWS API gateway

---

Transforming a RestAPI request to be processed as a SOAP API call
![image](https://user-images.githubusercontent.com/1868409/151677314-a65adc2e-e90a-4a5f-b5fc-8c8af93cef2d.png)

===

What option do we have for providing API specifications to an AWS api gateways?

---

You can export your API Gateway to Swagger or OpenAPI 3.0

===

How caching works with AWS API Gateway? (ttl, definition, capacity, cost) (4)

---

-  Default TTL (time to live) is 300 seconds. You can set between min: 0s and max: 3600s.
-  They are defined per stage
-  The Cache capacity is between 0.5GB to 237GB
-  Using Cache is expensive, it makes sense in production, but not that much in dev or test

===

Ways to invalidate AWS API Gateway Cache (2) (IDH)

---

-  Through the console you're able to flush the entire cache immediately
-  Clients can invalidate the cache with this header: `Cache- Control: max-age=0`. It's neccesary to have IAM authorization and an InvalidateCache policy IAM resource policy otherwise any client can invalidate the API cache (and you don't want that)

===

Let's say you have a public AWS API Gateway and now you want to start charging all people who are using it. What do you need to configure? (2)

---

You need to configure 2 things:

-  Usage Plan that defines who can access one or more deployed API stages and methods.
-  And API keys that you will use to control access

===

In AWS API Gateways, explain how a Usage Plan works. (defintions, configurations, etc) (3)

---

-  They use API keys to identify API clients
-  You can set how much and how fast your customer can access your API Gateway
-  You can also configure throttling limits and quota limits that are enforced on individual client

===

How to configure a Usage plan for AWS API Gateways (4)

---

1. Create one or more APIs, configure their methods to require an API key, and
   deploy the APIs to stages.
2. Generate or import API keys to distribute to application developers (your
   customers) who will be using your API.
3. Create the usage plan with the desired throttle and quota limits.
4. Associate API stages and API keys with the usage plan. Callers of the API must supply an assigned API key in the x-api-key header in
   request's API.

===

How is Cloudwatch logs enabled for AWS API Gateway? And what information does it contain?

---

-  CloudWatch logging is enabled at the Stage level
-  Log contains information about request / response body

===

In AWS API Gateway, what option do you have for tracing?

---

You can enable X-Ray tracing to get extra information about requests in API Gateway

===

Some important CloudWatch Metrics to remember for API Gateway (5)

---

-  CacheHitCount & CacheMissCount: efficiency of the cache
-  Count: The total number API requests in a given period.
-  IntegrationLatency: The time between when API Gateway relays a
   request to the backend and when it receives a response from the
   backend.
-  Latency: The time between when API Gateway receives a request from
   a client and when it returns a response to the client. The latency
   includes the integration latency and other API Gateway overhead.
-  4XXError (client-side) & 5XXError (server-side)

===

When does AWS API Gateway start to throttle? What error do we get when that happens? (IDH)

---

-  API Gateway throttles requests at 10000 rps **across all APIs**
-  In case of throttling a 429 Too Many Requests error is thrown

===

What important concern should we keep in mind when defining throughtput limits to our API Gateway APIs?

---

Just like Lambda Concurrency, one API that is overloaded, if not limited, can cause other APIs to be throttled

===

In AWS API Gateway, what do you need to enable when receiving API calls from other domains? (IDH)

---

CORS must be enabled. If you have a Proxy integration, this needs to be done in the destination code. Otherwise, you can do it by enabling CORS on the API Gateway console

![image](https://user-images.githubusercontent.com/1868409/151782069-74402785-05a7-41cd-8f1f-830b94d57ca1.png)

===

Explain API Gateway Security through IAM Permissions (3)

---

-  This strategy is commonly used when you want to provide API Gateway within AWS (EC2, Lambda, IAM users, etc)
-  Here you use IAM for authentication and IAM policies for authorization
-  It uses SigV4 to include credentials in headers

![image](https://user-images.githubusercontent.com/1868409/151956670-c4438d28-6057-4676-8725-3ccc3cf3ee32.png)

===

Explain API Gateway Security through Resource policies (1), When to use it? (3)

---

-  It uses Resource policies (similar to Lambda Resource Policy)
-  Use this strategy when you want to allow:
   -  Cross Account Access
   -  Specific source IP address
   -  VPC Endpoint

===

Explain API Gateway Security using Cognito User Pools

---

Client retrieves token from Cognito and then API gateway verifies token using Cognito too

![image](https://user-images.githubusercontent.com/1868409/151957958-b4c5b46a-1bc7-4fd3-a15b-750570635249.png)

===

Explain API Gateway Security Lambda Authorizer (formerly Custom Authorizers) (3)

---

-  In this strategy, the client authenticates with a 3rd party auth system, and sends a request with a Bearer token attached to it.
-  Then API gateway sends this token to a Lambda authorizer function (code should be written by yourself), which verifies whether token is invalid or not. In case it's valid, it will return an IAM Policy.
-  API gateway will allow access and cache the corresponding IAM policy returned by the Lambda authorizer

![image](https://user-images.githubusercontent.com/1868409/151958875-336d9ac1-75ff-4859-96bf-048b688b1a19.png)

===

Difference between an HTTP API vs REST AṔI in AWS API Gateway

---

They both do pretty much the same. REST API might offer some high level capabilities like Custom authorizers, response transformation, x-ray, etc. But HTTP API is much cheaper and it would be the default option to go when you only want to proxy your requests.

===

Explain how the Websocket API works in AWS API Gateway (for connecting, sending msg from client and sending msgs from server)

---

-  For connecting API Gateway sends a onConnect call to server (e.g: Lambda Function)
-  For sending a message from client, API Gateway sends a sendMessage call to server
-  For sending a message from server, the server sends a signed (using SigV4) POST call to the API Gateway to the connection URL Callback

![image](https://user-images.githubusercontent.com/1868409/152139735-7354cf48-f9d3-4607-b0c7-8692ff1c7f1c.png)

===

Explain how routing works for Websocket API in AWS API Gateway (2)

---

-  When sending a message to a Web Socket API you should send a JSON with the following format:

```
{ action:"<action here>", data:"<data here>"}
```

-  The "action" key should match with one of the backend integrations available in your API Gateway, otherwise it will be routed to any default route you have

![image](https://user-images.githubusercontent.com/1868409/152143980-30178647-6158-4c3b-be8b-1cf3ee243b47.png)

===

How can we use AWS API Gateway to organize our microservice architecture? (2)

---

-  With API Gateway we can create a single interface for all the microservices in our company. We can achieve this by setting one resource endpoint per microservice.
-  Additionaly we can use Route53 to set our DNS routes (with SSL certificates)
