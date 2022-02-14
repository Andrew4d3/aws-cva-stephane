What is the AWS Step Functions purpose?

---

With Step Functions you can Model your workflows as state machines (one per workflow)

===

What features do AWS step functions provide? (3)

---

-  You can define your workflow in JSON
-  You can visualize the workflow and the execution of the workflow, as well as history
-  Workflow can be started with a SDK call, API Gateway or Event Bridge

===

In AWS Step Functions what's the purpose of a Task state?

---

The purpose is to do some work in your state machine

===

Ways to define a Task state in StepFunctions? (2)

---

-  By invoking an AWS Service: invoking lambda function, ECS task, DyanmoDB action, push messages into SQS, SNS.
-  By runing an Activity: the code for such activity can be on EC2, ECS or an on-premise service. The activity will poll the step function for any pending work and once is done with the execution send the result back to StepFunctions

===

Other type of states in StepFunctions (6)

---

-  Choice State: test for a condition to decide which branch to send (or default branch)
-  Fail or Succeed State: stop execution with failure or success
-  Pass State: Simply pass its input to its output or inject some fixed data without performing work.
-  Wait State: Provide a delay for a certain amount of time or until a specified time/date.
-  Map State: can be used to run a set of steps for each element of an input array
-  Parallel State: Begin parallel branches of execution.

===

Explain ways to handle errors in AWS StepFunctions (2)

---

-  Retry: to retry failed state
-  Catch: transition to failure path

===

List of predefined error codes in AWS StepFunctions (4)

---

-  States.ALL : matches any error name
-  States.Timeout: Task ran longer than TimeoutSeconds or no heartbeat received
-  States.TaskFailed: execution failure
-  States.Permissions: insufficient privileges to execute code

===

Explain attributes to congifure a Retry handler for a task in AWS StepFunctions (4)

---

-  ErrorEquals: match a specific kind of error
-  IntervalSeconds: initial delay before retrying
-  BackoffRate: multiple the delay after each retry
-  MaxAttempts: default to 3, set to 0 for never retried

===

What happens when the maximum number retries is reached in AWS StepFunction task?

---

When max attempts are reached, the Catch phase is triggered

===

Explain attributes to congifure a Catch handler for a task in AWS StepFunctions (3)

---

-  ErrorEquals: match a specific kind of error
-  Next: State to send to
-  ResultPath: determines what input is sent to the state specified in the Next field.

===

When defining a Catch Handler in AWS StepFUnctions, how can you send the error message to the next task?

---

Using the ResultPath attribute you can include the returned error within the input for the next task

![image](https://user-images.githubusercontent.com/1868409/153775521-da59f709-3a1f-4f00-8e81-36015b4f8b2a.png)

===

In AWS StepFunctions, when to use Standard and when to use Express

---

-  Use Standard for workflows that can take up to an extended period of time to complete (1 year max) and you have a loose budget (since it's more expensive)
-  Use Express for workflows that takes a short period of time to complete (5 min max) and you have a small budget (since it's cheaper)

===

What is AWS AppSync?

---

AWS AppSync provides a robust, scalable GraphQL interface for application developers to combine data from multiple sources, including Amazon DynamoDB, AWS Lambda, and HTTP APIs.

===

Other important features for AWS AppSync

---

-  Retrieve data in real-time with WebSocket or MQTT on WebSocket
-  For mobile apps: local data access & data synchronization

MQTT: standard messaging protocol for the Internet of Things

===

Ways to provide security in AWS AppSync

---

-  API_KEY
-  AWS_IAM
-  OPENID_CONNECT: OpenID Connect provider / JSON Web Token
-  AMAZON_COGNITO_USER_POOLS
