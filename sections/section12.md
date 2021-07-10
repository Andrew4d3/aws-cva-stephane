Steps to start using the AWS cli (3)

---

-  Install the CLI for your OS
-  Get access keys for the corresponding user using the IAM console
-  Run the `aws configure` command and set the requested credentials

===

CLI AWS command for listing your S3 buckets

---

```
$ aws s3 ls
```

===

CLI AWS command for creating a S3 bucket

---

```
$ aws s3 mb s3://<Bucket_Name>
```

Remember: "make bucket"

===

CLI AWS command for deleting a S3 bucket

---

```
$ aws s3 rb s3://<Bucket_Name>
```

Remember: "remove bucket"

===

CLI AWS command for copying a local file or S3 object to another location locally or in S3.

---

```
aws s3 cp <File> s3://<BucketName>/<Name of object>
```

or viceversa

You can also copy a bucket object to your local folder, e.g:

```
aws s3 cp s3://mybucket/test.txt test2.txt
```

===

Let's say we want to allow a S3 user (without any permision) to ONLY get objects from one specific bucket, what should we do?

---

Using IAM, we should create a custom policy with only `GetObject` access for the targeted bucket. Then we attach this policy to the targeted user.

===

What tool can we use to test our IAM policies?

---

The IAM Policy simulator

===

If we wanted to allow an EC2 instance to be able to perform actions upon our S3 recources (like create other EC2 instances), what would we do?

---

We would need to create a Role (or use one provided by AWS) and attach it to the corresponding EC2 instance at launch.

===

When using the AWS cli, what option can we use to know if our intened command can work but without actually running it?

---

The `--dry-run` option. Example:

```
$ aws ec2 run-instances --dry-run --image-id ami-05373777d08895384 --instance-type t2.micro --region sa-east-1
```

The above command will never run, it wil print a message like this if succeeds:

```
An error occurred (DryRunOperation) when calling the RunInstances operation: Request would have succeeded, but DryRun flag is set.
```

Meaning that the command would have worked if there were no `--dry-run` option

===

What aws CLI command should we use if we wanted to decode an Error encoded message from AWS? Some consideration when using such a command?

---

This command:

```
$ aws sts decode-authorization-message --encoded-message
```

Our role or user should have the corresponding policy that allows STS decoding

===

How can we get Instance info (IP, hostname, iam roles, etc) from inside the instance?

---

We can use the meta_data endpoint provided by AWS. It only works from inside the instance.

```
$ curl http://169.254.169.254/latest/meta-data/
```

===

What AWS cli feature would we use if we wanted to perform multiple actions with multiple users? (4)

---

(1) We can configure a different user profile, using the `--profile` option. Like this:

```
$ aws configure --profile <USER_PROFILE_NAME>
```

(2) Then we would need to enter the corresponding access key id and secrets for the other profile. Now, if we wanted to run commands using this profile, we would need to set the targeted profile using the `--profile` argument like this:

```
$ aws s3 ls --profile <USER_PROFILE_NAME>
```

(3) Or if we have plans to use the alternative profile for more commands we can just add this environment variable, so that we don't have to include the `--profile` argument:

```
$ export AWS_PROFILE=<USER_PROFILE_NAME>
```

(4) To change back to the default profile just change this env var to `default`

===

How can we use the AWS cli with MFA? (5)

---

-  We need to get a session token using STS
-  First we need to add a MFA device to our user account
-  Then, we would run the following command (e.g):

```
$ aws sts get-session-token --serial-number arn:aws:iam::045366617141:mfa/andrew-s3 --token-code 146874
```

-  You need to replace the `--serial-number` with the the MFA device ARN you added in the previous step and the `--token-code` with the one provided by your 2FA app.
-  After doing this you will get some temporary credentials that you can use with to work with the CLI or with the SDK.

===

When is it required to use the AWS SDK?

---

When we want to use AWS services such as DynamoDB

===

What does it happen when you don't specify a default region while using the AWS SDK?

---

The region: us-east-1 will be chosen by default

===

What types of "Quotas" do we have in AWS? (2)

---

-  API Rate Limits: like DescribeInstance api calls for EC2 (with a limit of 100 calls per second) and GetOject on S3 with a limit of 5500 GET calls per second per prefix.
-  Service Limits: like running on-demmand standard instance with a limit 1152

===

How to handle Intermittent errors and Consistent errors when dealing with API Rate limits?

---

-  For Intermitten errors: implement an Exponential Backoff strategy (The SDK already implements one by default)
-  For consistent errors: request an API throttling limit increase

===

Ways to request a Service quota increase (2)

---

You can do it by:

-  Opening a ticket or
-  using the service quotas API

===

Let's say we're not using the SDK so we need to implement our own Exponential Backoff strategy. For which errors should we do it? and for which errors should not we do it?

---

-  Implement the retries only for 5xx server errors and throttling exeptions (you surpass the API call quota)
-  Don't do it for 4xx client errors. This means there's something wrong with the sent request.

===

How is the AWS CLI Credentials Provider chain? (6)

---

1. Command line options
2. Env variables
3. CLI credential file (~/.aws/credentials)
4. CLI configuration file (~/.aws/config)
5. Container credentials (ECS)
6. Instance Profile credentials (EC2 instance profiles using IAM roles)

===

How is the AWS SDK Default Credentials Provider chain? (5)

---

1. SDK properties
2. Env variables
3. credential file (~/.aws/credentials)
4. Container credentials (ECS)
5. Instance Profile credentials (EC2 instance profiles using IAM roles)

===

An application deployed on an EC2 instance is using environment variables
with credentials from an IAM user to call the Amazon S3 API.

-  The IAM user has S3FullAccess permissions.
-  The application only uses one S3 bucket, so according to best practices:
   -  An IAM Role & EC2 Instance Profile was created for the EC2 instance
   -  The Role was assigned the minimum permissions to access that one S3 bucket

The IAM Instance Profile was assigned to the EC2 instance, but it still had
access to all S3 buckets. Why?

---

The environment variables contains IAM user credentials that have full access to all the S3 buckets. Environment variables have a higher priority than EC2 instance profiles (IAM roles).

===

What's a good practice for using AWS credentials? (2)

---

(1) Never store any AWS credentials on code. (2) Relies on IAM roles our environment variables (if working from outside AWS)

===

What do we have to do if we want to perform an AWS action without using the SDK or CLI? (only using a AWS HTTP request)

---

We need to sign the AWS HTTP request using Signature v4 (SigV4)

===

Ways to sign an AWS HTTP request using SigV4 (2)

---

-  HTTP Headers
-  Query strings
