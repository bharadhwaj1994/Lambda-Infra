# Lambda-Infra

Reporting Public Api is exposed to B2B partners to get generated reports from bikeleasing. The reports essentially consists of data such as contract data from financials to bike and employee details. The REST Api is used to get the customer requests over internet to calls the API gateway endpoint we use aws cognito or lambda authorizer or okta/auth0 etc to authorize the user and authenticate the client request is case the user is not authenticated the API returns a default http error messages such as 403 in case the user is authorized the request is send to aws Lambda where our kotlin based code is running as a lambda function. Kotlin code reads the client request and queries the database in our case MySQL rds and returns the response from the database and the next step is to convert the data into client requested file format and encrypt the file with PGP encryption and the next step is to drop the file in an aws S3 bucket associated to specific client (one folder for each b2b client) we send the generated report to b2b server location using aws transfer family using sftp by establishing a ssh connection using ssh private key/ user id/password associated to remote sftp public key. Once the report is in S3 bucket the aws transfer family invokes startFileTransfer API to send the file to destination server sftp location. Further we can leverage hashicorp vault or aws secret manager to store sensitive credentials such as database credentials to securely receive the credentials we create security group connections from the vpc our lambda is hosted in to the vault server. Further we make use of IAM Roles and IAM Policies to have lambda access to S3 bucket and aws secret manager and few other IAM roles policies for cloud watch, API Gateway etc. 

Why use lambda, API Gateway and AWS Transfer family ?

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
    IAM policy allows Lambda to perform CRUD operations on a Database table and write to Amazon CloudWatch Logs further we create a IAM role and attach the created policies to the IAM Role this can also be achieved using Terraforms and this can we automated with CI/CD using Jenkins where the terraforms are put in a github repo and connected to Jenkins server using webhoots and aws plugins are installed on the Jenkins server with associated credentail information about aws account, region where the resources need ot he created and We can configure Jenkins to init the terraform, plan the terraform and apply the terraform or destroy the terraform using Jenkins file where the stages are defined. any new changes to the terraform triggers a jenkins build and changes are pushed to aws and appropriate resources are modified/changed per requirements. 

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
