Kotlin with GraalVM defined

Oracle GraalVM is a high-performance JDK that can speed up the performance of Java and JVM-based applications using an alternative just-in-time (JIT) compiler. It lowers application latency, improves peak throughput by reducing garbage collection time, and comes with 24/7 Oracle support.

Why use lambda, API Gateway and AWS Transfer family ?

As your functions receive more requests, Lambda automatically handles scaling the number of execution environments until you reach your account's concurrency limit. By default, Lambda provides your account with a total concurrency limit of 1,000 across all functions in a region.

Concurrency is the number of in-flight requests your AWS Lambda function is handling at the same time. For each concurrent request, Lambda provisions a separate instance of your execution environment. As your functions receive more requests, Lambda automatically handles scaling the number of execution environments until you reach your account's concurrency limit. By default, Lambda provides your account with a total concurrency limit of 1,000 across all functions in a region. To support your specific account needs, you can request a quota increase and configure function-level concurrency controls so that your critical functions don't experience throttling.

Lambda invokes your function in a secure and isolated execution environment. To handle a request, Lambda must first initialize an execution environment (the Init phase), before using it to invoke your function (the Invoke phase):

Using API Gateway provides users with a secure HTTP endpoint to invoke your Lambda function and can help manage large volumes of calls to your function by throttling traffic and automatically validating and authorizing API calls. API Gateway also provides flexible security controls using AWS Identity and Access Management (IAM) and Amazon Cognito. 

The AWS Transfer Family offers fully managed support for the transfer of files over SFTP, AS2, FTPS, and FTP directly into and out of Amazon S3 or Amazon EFS. You can seamlessly migrate, automate, and monitor your file transfer workflows by maintaining existing client-side configurations for authentication, access, and firewalls — so nothing changes for your customers, partners, and internal teams, or their applications.

The AWS Transfer Family provides you with a fully managed, highly available file transfer service with auto-scaling capabilities, eliminating the need for you to manage file transfer related infrastructure. Your end users’ workflows remain unchanged, while data uploaded and downloaded over the chosen protocols is stored in your Amazon S3 bucket or Amazon EFS file system. With the data in AWS, you can now easily use it with the broad array of AWS services for data processing, content management, analytics, machine learning, and archival, in an environment that can meet your compliance requirements.

How do I improve performance of file transfers for remotely located end users?

A: You can use AWS Global Accelerator with your Transfer server endpoint to improve file transfer throughput and round-trip time.

Can multiple host keys be used to verify the authenticity of my SFTP server?

A: Yes. The oldest host key of each key type can be used to verify the authenticity of an SFTP server. By adding RSA, ED25519, and ECDSA host keys, 3 separate host keys can be used to identify your SFTP server.

What options do I have to validate the identity of a remote SFTP server?

A: You can use remote server’s SSH key fingerprint to validate the identity of remote server. Upload the remote server’s SSH key fingerprint to your SFTP connector’s configuration, and each time connector establishes a connection to the remote server, identity of the remote server will be validated using the fingerprint. If the fingerprint provided by the remote server does not match that uploaded to the SFTP connector configuration, the connection will fail and the error details will be logged in CloudWatch. To re-establish connection, you can manually edit the connector configuration to specify the updated SSH fingerprint of the server.

Q: What file transfer operations are supported by SFTP connectors?

A: You can use SFTP connectors to send files from Amazon S3 to a directory on remote SFTP server, or to retrieve files from a directory on remote SFTP server to Amazon S3. You can initiate a file-transfer on demand by executing the StartFileTransfer API. Visit SFTP connectors documentation to learn more, and please let us know via AWS Support or through your AWS account team if you would like more operations to be supported using SFTP connectors.

Q: How do I track status of my file transfers?

A: SFTP connectors generate logs for each file-transfer that you can access via Amazon CloudWatch. You can track whether the file transfer was completed or failed. SFTP connectors also emits additional details such as operation (send or retrieve), timestamp, source and remote path of the file, transfer status and error description (if any) to help
you maintain data lineage.



Requires IAM Policies
    IAM policy allows Lambda to perform CRUD operations on a Database table and write to Amazon CloudWatch Logs further we create a IAM role and attach the created policies to the IAM Role this can also be achieved using Terraforms and this can we automated with CI/CD using Jenkins where the terraforms are put in a github repo and connected to Jenkins server using webhooks and aws plugins are installed on the Jenkins server with associated credentail information about aws account, region where the resources need ot he created and We can configure Jenkins to init the terraform, plan the terraform and apply the terraform or destroy the terraform using Jenkins file where the stages are defined. any new changes to the terraform triggers a jenkins build and changes are pushed to aws and appropriate resources are modified/changed per requirements. 

    Resource based IAM role and policy for API Gateway to invoke lambda
    IAM S3 policy for lambda to perform operations with s3 like create/read delete objects in s3
    IAM cloudwatch lambda policy to allow lambda to publich logs to cloud watch 

Sample policies

{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "Stmt1428341300017",
      "Action": [
        "dynamodb:DeleteItem",
        "dynamodb:GetItem",
        "dynamodb:PutItem",
        "dynamodb:Query",
        "dynamodb:Scan",
        "dynamodb:UpdateItem"
      ],
      "Effect": "Allow",
      "Resource": "*"
    },
    {
      "Sid": "",
      "Resource": "*",
      "Action": [
        "logs:CreateLogGroup",
        "logs:CreateLogStream",
        "logs:PutLogEvents"
      ],
      "Effect": "Allow"
    }
  ]
}


How to deploy and manage Lambda Functions?
Simple deployments
Typically, Lambda Function resource updates when source code changes. If publish = true is specified a new Lambda Function version will also be created.

Published Lambda Function can be invoked using either by version number or using $LATEST. This is the simplest way of deployment which does not required any additional tool or service.

Controlled deployments (rolling, canary, rollbacks)
In order to do controlled deployments (rolling, canary, rollbacks) of Lambda Functions we need to use Lambda Function aliases.

In simple terms, Lambda alias is like a pointer to either one version of Lambda Function (when deployment complete), or to two weighted versions of Lambda Function (during rolling or canary deployment).

One Lambda Function can be used in multiple aliases. Using aliases gives large control of which version deployed when having multiple environments.

There is alias module, which simplifies working with alias (create, manage configurations, updates, etc). See examples/alias for various use-cases how aliases can be configured and used.

There is deploy module, which creates required resources to do deployments using AWS CodeDeploy. It also creates the deployment, and wait for completion. See examples/deploy for complete end-to-end build/update/deploy process.



Using aliases
Each alias has a unique ARN. An alias can point only to a function version, not to another alias. You can update an alias to point to a new version of the function.

Event sources such as Amazon Simple Storage Service (Amazon S3) invoke your Lambda function. These event sources maintain a mapping that identifies the function to invoke when events occur. If you specify a Lambda function alias in the mapping configuration, you don't need to update the mapping when the function version changes. For more information, see Lambda event source mappings.

In a resource policy, you can grant permissions for event sources to use your Lambda function. If you specify an alias ARN in the policy, you don't need to update the policy when the function version changes.

https://github.com/terraform-aws-modules/terraform-aws-lambda/blob/master/examples/deploy/main.tf
