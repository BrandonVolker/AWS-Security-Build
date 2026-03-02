# AWS Security Build

## Table of Contents ##
- [User Journey](#user-journey)
- [Infrastructure Components](#infrastructure-components)
- [Logging](#logging)
- [Alerting](#alerting)
- [Compliance](#compliance)
- [Conclusion](#conclusion)


## Overview ##
Diversey Bank, a fictional financial institution (FI) headquartered in Chicago, Illinois, offers a variety of consumer banking solutions. This lab will build the components necessary to host the bank's website and faciliate customer access to financial accounts via the Bank Account Portal application that is hosted within the company's Amazon Web Services (AWS) cloud environment. The customer's journey will be shown in it's entirety to allow the reader to gain an understanding of the business use-case. A deep-dive of the application workflow will then be presented which will showcase the role of each AWS service in supporting the application.

## Lab Goal / Disclaimer ##
This lab seeks to demonstrate a basic competency in securing the AWS services that may be used by a legitimate FI within their AWS environment. Portions of the Bank Account Portal application will be "vibe coded" to quickly mock the client-facing website and other functionality to allow more time to be spent on securing the underlying infrastructure components. The AWS Free Tier was also leveraged for the deployment of services where available. For that reason, certain architectural best-practices such as multi-region deployment were not implemented.

## Architecture Diagram ##
The following diagram serves as the foundation on which the application will be built.

<img width="1272" height="718" alt="architecture-diagram" src="https://github.com/user-attachments/assets/402d9b2a-8eb5-4e8b-a8e1-db936f1dfb6a" />



## User Journey ##
1. Customer visits https://www.diverseybank.com and clicks "Login"
   <img width="1920" height="1040" alt="homepage" src="https://github.com/user-attachments/assets/339b4e97-e381-4240-9157-b5659e19ed1a" />
   
2. Customer is prompted to authenticate to their account using their email address.
   <img width="1920" height="1040" alt="emailentered" src="https://github.com/user-attachments/assets/686fdbec-a177-4e52-bf06-12479cd4c8a3" />
   
3. Customer is prompted to enter the one-time passcode (OTP) that was sent to their email address.
   <img width="1923" height="996" alt="otp-prompt" src="https://github.com/user-attachments/assets/e5f65b2d-ab2a-4ca6-a2fe-7a0cfc96d5dc" />
   
4. Customer navigates to their email account and fetches the OTP.
   <img width="1917" height="584" alt="otpinemail" src="https://github.com/user-attachments/assets/d2746534-400c-4d94-9e05-2b4a058f01bc" />
   
6. Customer enters the OTP into the Bank Account Portal.
   <img width="1920" height="979" alt="otp-entered" src="https://github.com/user-attachments/assets/605fc93c-cfa0-43d7-8d45-ca125f4c605f" />
   
7. Customer has been authenticated and is presented with their Bank Account Portal Dashboard.
   <img width="1920" height="1040" alt="dashboard" src="https://github.com/user-attachments/assets/b8cea9b9-a56e-4fe6-b383-aefce459d89d" />
   
8. Customer clicks the Savings account tile to view information on that account.
   <img width="1920" height="1040" alt="savings" src="https://github.com/user-attachments/assets/c93a8d49-78cd-4b65-8a1b-e3a370510513" />
   
9. Customer clicks the Checking account tile to view information on that account.
   <img width="1920" height="1040" alt="checking" src="https://github.com/user-attachments/assets/4b857561-6b77-43c9-a5cd-0f89ef1aa577" />
   
10. Customer clicks "View Account Information" which displays the account number and bank's routing number. This information is displayed on-screen for 10 seconds before being hidden.
    <img width="1920" height="1040" alt="unmasked" src="https://github.com/user-attachments/assets/0aeff0d2-2c21-407c-945c-a0a632fd1e8b" />
    
11. Customer clicks "My Profile" to view their current personal contact information.
    <img width="1920" height="1040" alt="myprofile" src="https://github.com/user-attachments/assets/124cc916-d558-47e6-b60a-73bb2bd10364" />
    
12. Customer updates their contact information with a new address after a recent move.
    <img width="1920" height="1040" alt="myprofileupdated" src="https://github.com/user-attachments/assets/9d34a99d-b9a9-4c77-b1c8-4f538facc1f0" />
    
13. Customer navigates back to their Checking account and decides to chat with the integrated AI chatbot.
    <img width="1920" height="1040" alt="chatbot" src="https://github.com/user-attachments/assets/52732cda-aba3-4ed8-9c69-97ac20894aba" />
    
14. Customer proceeds to ask a couple questions regarding their account(s).
    <img width="1920" height="1040" alt="questionasked" src="https://github.com/user-attachments/assets/556b84cd-75b9-4e90-aef7-ccd28f5aba7c" />
    <img width="1920" height="1040" alt="questionanswered" src="https://github.com/user-attachments/assets/6c646fc7-6bce-406d-af33-3dab47efe16a" />
    <img width="1920" height="1040" alt="question2asked" src="https://github.com/user-attachments/assets/50824391-7344-4d75-a731-a287274be365" />
    <img width="1920" height="1040" alt="question2answered" src="https://github.com/user-attachments/assets/49d9e55c-30ff-4961-bb88-82f520a1e295" />
    <img width="1920" height="1040" alt="custsupportquestion" src="https://github.com/user-attachments/assets/c2856cce-0be5-47fe-ae58-1f6bdfc7fe1e" />


## Infrastructure Components ##
The following section will take a look "behind the scenes" to see how the application is built and supported.


### Route 53 Domain Name Service (DNS) ###
[Route 53](https://aws.amazon.com/route53/) is AWS's highly available and scalable DNS service that translates human-readble names to Internet Protocol (IP) addresses--a critical service that is needed to route traffic over the Internet.

A hosted zone, *diverseybank.com* was created within Route 53. This hosted zone includes (13) records that support the routing of traffic to the CloudFront Distribution among other functions. A brief explanation of each record is provided within the screenshot.
<img width="1804" height="857" alt="route53-config" src="https://github.com/user-attachments/assets/a6ae8be5-046f-466e-b253-3f9476014209" />


### CloudFront Content Delivery Network (CDN) ###
[CloudFront](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Introduction.html) is a CDN that delivers web content through a worldwide network of data center edge locations. When a user requests content that is served via CloudFront, the request is routed to the edge location that provides the lowest latency, providing the best possible performance. The web content is cached at multiple edge locations to ensure reliability and availability.

A CloudFront Distribution was created and named *diverseybank-web*.

1. The domain names *diverseybank.com* and *www.diverseybank.com* are attached to the distribution meaning that when a user enters these names into their web browser, they will be redirected to CloudFront (recall the A/AAAA records above).
   <img width="1628" height="654" alt="cloudfrontconfig" src="https://github.com/user-attachments/assets/9cd46b83-326b-4b23-90bb-9460ac4d906f" />


2. A custom Secure Sockets Layer (SSL)/Transport Layer Security (TLS) certificate has been created within AWS Certificate Manager (ACM) and associated with the CloudFront Distribution.
   
   <img width="1625" height="745" alt="tls-cert" src="https://github.com/user-attachments/assets/de1853e5-d253-4495-bad1-2c47c98c818a" />

   <img width="1059" height="773" alt="cert-in-browser" src="https://github.com/user-attachments/assets/9a18a4e1-3f69-44f0-a2c6-45439659e540" />



4. The *Security Policy* sets the minimum Transport Layer Security (TLS) version to 1.2. While 1.3 is the industry recommendation, 1.2 is still accepted by many banks to provide support for older clients.
   

6. The *Default root object* is configured for *spa.html* which tells CloudFront which file to serve when a request to the domain is made without a specified path. The significance of this file is explained below.
   

The *Security* tab shows that CloudFront is leveraging AWS's Web Application Firewall (WAF) to provide the following protections.
<img width="1630" height="814" alt="cloudfrontwaf" src="https://github.com/user-attachments/assets/5fb72dbc-6aec-4769-a337-de5a1c3d9693" />
<img width="1482" height="407" alt="waf protections" src="https://github.com/user-attachments/assets/c369527f-faec-49e3-b5cf-3e046f7a1fe7" />

The *Origins* tab shows the location from which the distribution fetches its content. In this case, it is an S3 bucket which will be explained below.
<img width="1623" height="586" alt="cloudfront-origins" src="https://github.com/user-attachments/assets/577903f2-4ecf-4a42-992a-17bfe092dc70" />

CloudFront is also configured to redirect Hypertext Transfer Protocol (HTTP) requests to the secure version (HTTPS).
<img width="1626" height="557" alt="cloudfront-https" src="https://github.com/user-attachments/assets/9e16ab87-446d-478c-83d1-9af7b4214bb8" />


### Single Page Application (SPA) ###
The SPA was written by the Claude Sonnet 4.5 large language model (LLM) and is comprised of a single Hypertext Markup Language File (HTML) file. This file is fetched by the client/customer browser and includes all of the static content (colors, fonts, buttons) that is rendered within the browser upon page load. As the user interacts with the page, Application Programming Interface (API) calls are made by the browser to back-end components within AWS to return and update user-specific information. [SPAs](https://softwaremind.com/blog/what-is-a-single-page-application-spa/) are popular because they dyamically render new information on the same HTML page rather than reloading the page from scratch which results in a quicker load time and better user experience.
<img width="1920" height="1040" alt="spa-content" src="https://github.com/user-attachments/assets/7d06fb99-5ade-419a-9255-979df512b094" />
<img width="1645" height="787" alt="spa-config-api" src="https://github.com/user-attachments/assets/24e88306-abbd-4418-979d-36a43e118637" />


### S3 Bucket ###
The [AWS Simple Storage Service (S3)](https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html) provides scalable object storage within containers called buckets. The SPA file is named *spa.html* and resides within an S3 bucket named *diverseybank-web-static-content*.

<img width="1479" height="636" alt="spainbucket" src="https://github.com/user-attachments/assets/b2ced838-9b1a-4177-a43f-e6ffa34453e9" />


The bucket is configured to block all public access while the bucket policy grants access to the CloudFront distribution. This policy will ensure that clients cannot directly interact with the bucket and instead go through CloudFront, the Content Delivery Network (CDN) that sits in front of the bucket.
<img width="1636" height="818" alt="s3 settings" src="https://github.com/user-attachments/assets/b392892d-99cb-4633-bc1c-7fa72eb4dc84" />


### Cognito ###
Amazon [Cognito](https://docs.aws.amazon.com/cognito/latest/developerguide/what-is-amazon-cognito.html) is an identity platform for web and mobile applications. This project leverages Cognito to perform user authentication and authorization to the Bank Account Portal application.

A user pool and app client was created within Cognito and named *diverseybank-web-customer-login*. The following timeout values were configured:

*Refresh token expiration* - 720 minutes (12 hours) - Used to silently obtain new ID and access tokens from Cognito. Once this duration has been reached, the user is forced to re-authenticate, despite active sessions (an absolute timeout).

*Access token expiration*- 15 minutes - Used to authorize API calls to Cognito. Must be silently refreshed to prevent the customer from being logged out.

*ID token expiration* - 15 minutes - Used to authorize API calls to the API Gateway. Must be silently refreshed to prevent the customer from being logged out.

<img width="1627" height="797" alt="cognito" src="https://github.com/user-attachments/assets/a4a7e13b-aad1-46c8-b94a-5df57ac9d82a" />

The following is the test user that has already registered with Cognito and has been assigned a User ID (Sub).

<img width="1623" height="638" alt="users" src="https://github.com/user-attachments/assets/0c4bba44-f111-4024-a597-1b1bde9100a8" />
<img width="1625" height="320" alt="sub" src="https://github.com/user-attachments/assets/68a9529f-fa1b-4f8f-8fd0-501be23addb6" />


### API Gateway ###
[API Gateway](https://docs.aws.amazon.com/apigateway/latest/developerguide/welcome.html) is an AWS service used for creating, publishing, maintaining, monitoring, and securing Representational State Transfer (REST), HTTP, and WebSocket APIs at scale. The gateway acts as the "front door" for applications to access data, business logic, or functionality from backend services.

One API has been created and named *customer-account-api*. This API has an attached *Security policy* which enforces TLS 1.2 at minimum.
<img width="1618" height="587" alt="api gw" src="https://github.com/user-attachments/assets/277d650c-decd-4ca0-9195-45848d32d8aa" />

The API has been configured with (6) resources and various methods attached to each. An explanation of the methods is included within the screenshot.
<img width="1479" height="622" alt="apiconfigfinal" src="https://github.com/user-attachments/assets/2635aa1b-2808-49e5-ac65-18aef8f07b29" />

Each method (GET, PUT) has an authorizer attached to the policy that is named *customer-account-cognito-authorizer*. The authorizer validates the JSON Web Token (JWT) on every API request, ensuring that only authenticated users can access protected resources.

<img width="1624" height="505" alt="authorizer" src="https://github.com/user-attachments/assets/b6a22420-19b1-4436-85e5-fd2843b2bfdd" />

The following is a look at the JWT token that is stored within the browser's memory.
<img width="1951" height="770" alt="jwt browser" src="https://github.com/user-attachments/assets/fff21785-6c82-4d12-b86b-ad55bbae24f0" />

Here is what the JWT token looks like when it has been decoded using jwt.io. The token is valid for 1 hour.
<img width="1917" height="1030" alt="jwt" src="https://github.com/user-attachments/assets/c1bcdd3e-5d81-4bc4-9da2-a74ad7524919" />
<img width="601" height="843" alt="jwtclaims" src="https://github.com/user-attachments/assets/20ca2f44-1fb9-4d22-babf-917d5e0561bb" />


### Lambda ###
AWS [Lambda](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html) is a serverless, event-driven compute service, that runs code using functions--the basic building blocks used to create an application. Event sources and AWS services trigger the functions to process data and perform certain actions.

Two functions have been created and are named *prod-customer-account-function* and *prod-secrets-rotation-function*.
<img width="1621" height="423" alt="functions" src="https://github.com/user-attachments/assets/b71f5f10-94cc-4e04-9ed6-31390835a846" />


*prod-customer-account-function* has (4) triggers. These triggers are the HTTP methods that were displayed in the API hierarchy in the previous section and are responsible for fetching the account details, updating the customer's contact information, and sending the customer's message to the chatbot.
<img width="1627" height="821" alt="functions" src="https://github.com/user-attachments/assets/aa8837b2-8efd-47c5-8a25-c384053a801b" />


Claude wrote two files, *index.mjs* and *package.json*, that were zipped and uploaded to the function. *us-east-1-bundle.pem* was added manually.

*index.mjs* is the main Lambda function handler and contains the business logic for the API.

*package.json* is the Node.js manifest which defines the project name, version, and npm package dependencies.

*us-east-1-bundle.pem* is the SSL certificate bundle used to connect to *prod-customer-account-db* (explained below).


<img width="1629" height="819" alt="prod-customer-account-function" src="https://github.com/user-attachments/assets/20f5cc6d-e36c-4ea8-9f7c-72e62a39a036" />



*prod-secrets-rotation-function* is used to rotate the *prod-customer-account-db* password every 30 days per the current policy or on an ad-hoc basis. The function is supported by a Python script (written by Claude) which generates a new password, connects to the database, changes the database password, validates it, then activates it in Secrets Manager for use by Lambda.

<img width="1627" height="813" alt="prod-secrets-rotation-function" src="https://github.com/user-attachments/assets/81fa09fc-841c-4df9-b56e-e063ecdfa141" />



### Relational Database Service (RDS) ###
AWS [RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Welcome.html) is a managed database service that offers a variety of database engines including IBM Db2, MariaSB, Microsoft SQL Server, Oracle Database, PostreSQL and MySQL.

A MySQL database instance has been created and is named *prod-customer-account-db*.
<img width="1622" height="461" alt="rds" src="https://github.com/user-attachments/assets/ef2c9312-f204-407a-b703-3b1d72c45335" />


This database contains three tables:
*accounts* which is comprised of the following tables: account_id, account_type, account_balance_customer_id, account_number_token, and last_four.
<img width="1936" height="1010" alt="accounts" src="https://github.com/user-attachments/assets/fdd7bd3b-9ddb-4239-a638-780cd4b732b5" />

*customers* which is comprised of the following tables: customer_id, first_name, middle_name, last_name, street_address_1, street_address_2, state, city, zip_code, country_code, phone_number, email_address, ssn_token, itin_token, and cognito_sub.
<img width="1924" height="1007" alt="customers" src="https://github.com/user-attachments/assets/d2cb5073-2818-41c0-9509-aa367aa2974e" />

*transactions* which is comprised of the following tables: trans_id, trans_date, trans_merchant, trans_type, trans_amount, and account_id.
<img width="1949" height="1015" alt="transactions" src="https://github.com/user-attachments/assets/efa64d1a-0e93-40f2-99b8-dd7fd0301e65" />


### Tokenization ###
Note that MySQL is not storing the account number, Social Security Number (SSN), or Individual Taxpayer Indentification Number (ITIN) of any customer as these are highly sensitive data attributes. An adversary that intercepts these attributes could damage the reputation of the organization and put customers at risk of identity theft and fraud. The database instead stores tokens which are random 32 character hex strings with a "tok_" prefix to minimize that risk. The token strings were generated by Lambda using the crypto module that is built-in to Node.js. If an adversary were to compromise the database, the tokens would be useless as there is no relationship to the actual values.
<img width="1596" height="789" alt="tokens" src="https://github.com/user-attachments/assets/589d358a-ad1b-4b10-ae7d-9ef47eaaba64" />


### Secrets Manager ###
AWS [Secrets Manager](https://docs.aws.amazon.com/secretsmanager/latest/userguide/intro.html) helps to manage, retrieve, and rotate database and application credentials, OAuth tokens, API keys, and other secrets throughout their lifecycles.

A secret has been created and is named *prod-customer-account-db-secret*. This secret is used by Lambda to connect to the MySQL database to retrieve customer account details and update customer profile information.

The secret will be rotated every 30 days using the *prod-secrets-rotation-function*, a Lambda Function that was created for this purpose.

<img width="1628" height="818" alt="secret" src="https://github.com/user-attachments/assets/178c3a9f-07d2-4415-a19a-31b03ae911b4" />


### DynamoDB ###
Amazon [DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html) is a serverless, fully managed, distributed NoSQL database service. DynamoDB is used in this project as a tokenization service.

A DynamoDB table has been created and is named *prod-tokenization-vault*.
<img width="1623" height="607" alt="dynamodb" src="https://github.com/user-attachments/assets/da4b468a-4b8d-440e-9ef4-d1a516ab675d" />

This table contains the token_id to encrypted_value mapping that is necessary to render the plaintext values of the sensitive attributes (e.g. to display the account number to the customer on screen).
<img width="1627" height="810" alt="explore" src="https://github.com/user-attachments/assets/2323491d-0a1a-4749-b4ed-b4e4dc49724e" />



### Key Management Service (KMS) ###
AWS [KMS](https://docs.aws.amazon.com/kms/latest/developerguide/overview.html) is an encryption and key management service used to protect data within AWS applications.

Three symmetric customer managed keys (CMKs) were created within KMS for this project:

<img width="1627" height="334" alt="kms" src="https://github.com/user-attachments/assets/286ce9e5-c4f8-4dd0-aa15-629728363815" />


*prod-secrets-key* is used to encrypt the secret to the MySQL database while it is stored within Secrets Manager.

*prod-customer-account-db-key* is used to encrypt the MySQL database at the storage-level.

*prod-tokenization-vault-key* is used to encrypt sensitive data attributes (account number, SSN, and ITIN) at the field-level before they are written to the DynamoDB table.




### Detokenization/Decryption Workflow ###

The following describes the interactions that take place to show the customer their account number in the clear via the "View Account Information" option within the browser:
1. The SPA sends a GET /accounts request to the API Gateway with the customer's JWT token in the authorization header.
2. The Cognito authorizer (attached to the API policy) validates the JWT and passes the customer's "sub ID" to Lambda.
3. Lambda queries the MySQL database for the customer's account which is stored as account_number_token.
4. Lambda uses account_number_token to look up the corresponding record in DynamoDB, which stores the actual account number in an encrypted format.
5. Lambda calls KMS to decrypt the encrypted account number using a Customer Managed Key (CMK). Lambda has the necessary kms:Decrypt permission via an execution role.
6. KMS returns the decrypted account number to Lambda.
7. Lambda returns the full account number to the SPA on the API response which is protected using TLS. The account number is held in memory for the duration of the user's session.
8. The SPA will display the account number on screen for 10 seconds before hiding it. This action minimizes the risk of a social engineering technique knows as Shoulder Surfing where an adversary views sensitive information that has been displayed on the screen without the user's knowledge.


### Bedrock ###
Amazon [Bedrock](https://aws.amazon.com/bedrock/) is a generative Artificial Intelligence (AI) platform used for building generative applications and agents at production scale.

This lab leverages Bedrock to consume Antropic's Claude Haiku 4.5 model.
<img width="1624" height="816" alt="claude" src="https://github.com/user-attachments/assets/fd736f6c-4e8d-441f-a33c-e6022bbf470e" />

The model is invoked by Lambda when the user interacts with the chatbot feature within Bank Account Portal.

A snippet of the AI chat function within the SPA including invocation of the /chatbot resource on the API Gateway.

<img width="574" height="974" alt="spa-chatbot" src="https://github.com/user-attachments/assets/5483d31e-2e72-484a-af4b-78122ef1ae1e" />

A snippet of the *index.mjs* logic file that is used by the Lambda function to invoke the LLM, including the prompt.

<img width="1882" height="979" alt="indexfile" src="https://github.com/user-attachments/assets/85bc59b3-dde8-45c7-9617-878ccfd3c347" />


### LLM Guardrails ###
[Guardrails](https://aws.amazon.com/bedrock/guardrails/) are used to implement safeguards based on application-specific use cases and responsible AI practices.

A guardrail was created and is named *prod-bank-account-portal-chatbot-guardrail*. This guardrail protects against harmful categories, prompt attacks, custom denied topics, and PII disclosure.

<img width="1479" height="714" alt="guardrail" src="https://github.com/user-attachments/assets/5fe7d46c-0c76-4d00-97cd-b732410d798d" />

<img width="1890" height="538" alt="deniedtopics" src="https://github.com/user-attachments/assets/e631484c-9e6d-43da-bab8-9acc37504142" />

<img width="1896" height="792" alt="guardrailfilter" src="https://github.com/user-attachments/assets/d76660d3-5c67-4281-a035-bc2353aaad55" />

<img width="1911" height="974" alt="guardrailresponse" src="https://github.com/user-attachments/assets/59c20b5b-2783-4c25-b986-16b0e1561329" />


### Virtual Private Cloud (VPC) Endpoints ###
[VPC endpoints](https://wa.aws.amazon.com/wellarchitected/2020-07-02T19-33-23/wat.concept.vpc-endpoint.en.html) enable private connectivity from a VPC to supported AWS services powered by PrivateLink without requiring an internet gateway, NAT device, VPN connection,or AWS Direct Connect. These Interface Endpoints use Elastic Network Interfaces (ENIs) which are assigned private IP addresses that act as entry points to the AWS service.

(4) VPC Endpoints have been created for this project. Lambda will leverage PrivateLink to connect to KMS, Secrets Manager, Bedrock, and DynamoDB via these endpoints.

<img width="1626" height="376" alt="vpcendpoints" src="https://github.com/user-attachments/assets/d268a55d-1848-4db0-8fbb-d4b781989867" />



### Network Configuration ###
The following illustrates the network configuration of this AWS environment.

**VPC**: prod-diverseybank-vpc


| AZ         | Subnet Name  | CIDR Block    | Attached To |
|------------|--------------|---------------|-------------|
| us-east-1a | app-tier-1a  | 10.0.128.0/20 | prod-customer-account-function, prod-secrets-rotation-function, prod-secrets-manager-endpoint, prod-kms-endpoint, prod-bedrock-endpoint, prod-dynamodb-endpoint |
| us-east-1a | data-tier-1a | 10.0.160.0/20 | prod-customer-account-db |
| us-east-1b | app-tier-1b  | 10.0.144.0/20 | prod-customer-account-function, prod-secrets-rotation-function, prod-secrets-manager-endpoint, prod-kms-endpoint, prod-bedrock-endpoint, prod-dynamodb-endpoint |
| us-east-1b | data-tier-1b | 10.0.176.0/20 | prod-customer-account-db |


<img width="1625" height="797" alt="vpcresourcemap" src="https://github.com/user-attachments/assets/2e872fa8-3ee7-4f60-b810-8006f0c99c38" />


### Security Group Configuration ###
[Security Groups](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-security-groups.html) act as virtual firewalls, controlling the inbound and outbound traffic to AWS resources.


Three security groups have been created and named *prod-lambda-sg*, *prod-rds-sg*, and *prod-vpc-endpoints-sg*.

<img width="1621" height="382" alt="securitygroups" src="https://github.com/user-attachments/assets/46f3bdde-4ba9-47c3-b5f4-50be421014dc" />




*prod-lambda-sg* controls traffic to/from the *prod-customer-account-function* Lambda function. This security group has one inbound rule that is self-referencing meaning only resources sharing this security group can send traffic to Lambda. This allows Lambda to establish connections to the four VPC endpoints over port 443 (HTTPS). 

<img width="1622" height="608" alt="prod-lambda-sg-in" src="https://github.com/user-attachments/assets/b6c8b118-7f80-415b-94a3-3de9352d14dc" />



There are two outbound rules: the first allows the Lambda function to connect to the *prod-customer-account-db* database over port 3306 (MySQL) while the second allows a connection to the *prod-vpc-endpoints-sg* security group for accessing KMS, Secrets Manager, Bedrock, and DynamoDB over port 443 (HTTPS) through the VPC endpoints.

<img width="1621" height="637" alt="prod-lambda-sg-out" src="https://github.com/user-attachments/assets/7cfd3b46-2d7a-4903-821b-aeef7765ad37" />



*prod-rds-sg* controls traffic to the *prod-customer-account-db* MySQL database. This security group has only one inbound rule that permits the connection from the *prod-customer-account-function* Lambda function over port 3306 (MySQL). There are no outbound rules.

<img width="1623" height="590" alt="prod-rds-sg-in" src="https://github.com/user-attachments/assets/6f82fd1e-d381-4037-8aa9-b2c94fbaafe3" />


*prod-vpc-endpoints-sg* controls traffic to the VPC endpoints (KMS, Secrets Manager, Bedrock and DynamoDB). This security group has only one inbound rule that permits the connection from the *prod-customer-account-function* Lambda function over port 443 (HTTPS). There are no outbound rules.

<img width="1625" height="575" alt="prod-vpc-endpoints-sg" src="https://github.com/user-attachments/assets/10c73559-bf26-47a5-a4f4-f7ab900439e4" />


### Identity and Access Management (IAM) ###
AWS [IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html) is a service used for managing user identities and permissions that control access to AWS resources. Indenties include the users, groups, and roles that are defined through IAM policies that define which actions are allowed or denied on the resources.

Lambda uses an execution role called *prod-customer-account-function-role-gr535woc* that it assumes when it needs to access resources. There are (6) permission policies attached.
<img width="1627" height="816" alt="prod-customer-account-function-role" src="https://github.com/user-attachments/assets/215c745b-a860-4061-bf18-66ff39cab7bc" />


<img width="1620" height="318" alt="bedrock-policy" src="https://github.com/user-attachments/assets/a2a19f9a-bfb2-481d-b4aa-3933424e04c7" />

<img width="1626" height="739" alt="exroledetail1" src="https://github.com/user-attachments/assets/5049a2a7-84af-4468-b70d-69e10b72f431" />

<img width="1623" height="663" alt="detailedpolicy2" src="https://github.com/user-attachments/assets/b5477189-6713-4781-a88c-c2b804d28edc" />

<img width="1624" height="419" alt="vpc-networking-policy" src="https://github.com/user-attachments/assets/c88d7ca5-769b-4ff1-8d53-1502f29a2094" />


Another IAM role called *prod-secrets-rotation-function-role-bg63yebj* is used by the *prod-secrets-rotation-function* Lambda function for rotating the *prod-customer-account-db* secret. There are (3) permissions policies attached.

<img width="1626" height="704" alt="prod-secrets-rotation-function" src="https://github.com/user-attachments/assets/5b5b3a74-40fb-4d40-a8ee-8df37ccd9d8f" />

<img width="1623" height="846" alt="secretsrotationdetailed1" src="https://github.com/user-attachments/assets/e415de73-e4f4-4702-9284-2178e991addd" />

<img width="1603" height="422" alt="vpc-networking-policy" src="https://github.com/user-attachments/assets/fcf999d6-836a-4062-aed5-ac3bd67cbbce" />

It is worth mentioning that there are other IAM service-linked roles that were automatically created by AWS for Guard Duty, Security Hub, Config, and other services. These roles follow the principle of least privilege and cannot be modified. 


## Logging  ##
AWS [CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-user-guide.html) helps enable operational and risk auditing, governance, and compliance in an AWS account. Actions taken by a user, role, or an AWS service are recorded as events in CloudTrail.

A trail has been created and is named *prod-cloudtrail-logging*. The logs are being sent to an encrypted S3 bucket.
<img width="1782" height="679" alt="cloudtrail" src="https://github.com/user-attachments/assets/8fa6c91a-cbe6-4421-999c-676271089d86" />

The test customer had logged in to the Bank Account Portal, navigated around their account, and logged out. The following Event History can be seen within the CloudTrail service when looking specifically at the Cognito events.

<img width="1622" height="454" alt="eventhistory" src="https://github.com/user-attachments/assets/d916b0a2-0294-4403-acf3-8a16cacfa568" />

The *RespondToAuthChallenge* event confirms the user's successful login to their account and shows the three tokens that Cognito has returned. The IP address of the session and browser information can also be seen.
<img width="709" height="805" alt="RespondToAuthChallenge" src="https://github.com/user-attachments/assets/a6e510b7-fd6c-470c-acb4-67b52b5f470c" />

The following is another log that shows the Lambda function fetching its credentials from Secrets Manager. These credentials will be used to authenticate to prod-customer-account-db MySQL database.

<img width="1130" height="797" alt="GetSecretValue" src="https://github.com/user-attachments/assets/33299922-57d9-4215-8416-959135e6163e" />


## Alerting ##
While the Root User was used to build this lab, it is a best-practice to leverage IAM users for human access to production accounts as those accounts offer more granular access control and auditability. In this example, an alert has been set-up that will notify the security team via email when someone has logged into the AWS Console using Root credentials. 

Amazon [Simple Notification Service (SNS)](https://docs.aws.amazon.com/sns/latest/dg/welcome.html) is a fully managed service that provides message delivery from publishers to subscribers. An SNS topic was created and is named *prod-security-alerts*. The email address of the security team has been added to the topic via a Subscription.

<img width="1624" height="608" alt="sns" src="https://github.com/user-attachments/assets/ae8fda56-b878-40cf-8b27-74a1bec8ccc1" />


Amazon [EventBridge](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-what-is.html) is a severless service used for building scalable event-driven applications. A rule was created within EventBridge and is named *prod-root-login-alert*. The rule is configured to look for the *ConsoleLogin* event name within CloudTrail.

<img width="1624" height="711" alt="prod-root-login-alert" src="https://github.com/user-attachments/assets/63cbbd0c-bd2d-4365-b7ca-792fa3bb61a0" />


The AWS Console was then accessed by the Root User. The event can be seen within CloudTrail's Event History.

<img width="1627" height="306" alt="consolelogin" src="https://github.com/user-attachments/assets/93a01c6b-03f8-4197-b0fc-8a9d767038d7" />


An email alert has also been sent to the security team.

<img width="1303" height="616" alt="consoleloginemail" src="https://github.com/user-attachments/assets/baa88fa5-2054-4efe-a2b2-2776f2463eaa" />


## Guard Duty ##
Amazon [GuardDuty](https://docs.aws.amazon.com/guardduty/latest/ug/what-is-guardduty.html) is a threat detection service that continuously monitors, analyzes, and processes AWS data sources and logs within an AWS environment. GuardDuty reported (1) Low severity finding related to the *ListManagedNotificationEvents* API being invoked using root credentials. This was generated because this lab was built using the root user account.

<img width="1629" height="793" alt="guarddutyfinding" src="https://github.com/user-attachments/assets/a4071d44-ec73-45d2-9250-4446a48841c2" />

A few sample (mock) findings related to the services that are used in this lab were generated to illustrate some security issues that the tool is capable of detecting.

<img width="1009" height="125" alt="samplefindings" src="https://github.com/user-attachments/assets/91efeb77-4c8b-45d0-b4b5-84f342d05efb" />
<img width="1624" height="718" alt="guarddutysample" src="https://github.com/user-attachments/assets/e7b5fea9-bd5b-420e-9e41-e06f797ea954" />

## Macie ##
Amazon [Macie](https://docs.aws.amazon.com/macie/latest/user/what-is-macie.html) is a data security service that discovers sensitive sensitive data using machine learning and pattern matching, provides visibility into data security risks, and enabled automated protection against those risks.

The *diverseybank-web-static-content* S3 bucket currently hosts the SPA, the publically-facing static content for the Diversey Bank website. Sensitive data should never make its way into the bucket, therefore, Macie has been enabled to scan the bucket and alert on the presence of that data.

A job has been created within Macie and is named *diverseybank-web-static-content-macie-job*. This job is configured to scan the *diverseybank-web-static-content* against two customer data identifiers.

*diverseybank-checking-account-number* - Checks for the presence of '60' followed by (8) digits.
*diverseybank-savings-account-number* - Checks for the presence of '70' followed by (8) digits.

A list of managed data identifiers, recommended by Macie, which includes common sensitive data attributes such as SSN, ITIN, and credit card number, are also included in the scan which runs daily.

<img width="1636" height="913" alt="macie-job" src="https://github.com/user-attachments/assets/9003fc10-2c9b-4042-9c85-08eec46b3c77" />

## Macie Practical ##

1. A text file that includes a few mock Social Security Numbers has been uploaded to the S3 bucket.
   <img width="1735" height="720" alt="macie-file-uploaded" src="https://github.com/user-attachments/assets/5b35061d-289b-470b-b1a7-15289e3b1038" />

2. The Macie job has been manually triggered.
   




## Auto-Remediation ##
A defense in-depth approach to security is necessary for protecting company resources. Preventative controls in the form of IAM policies should be used to enforce least privilege access to resources while detective and corrective controls in the form of alerting and automated remediation capabilities are necessary to address the shortcomings of other controls.


### Configuration ###
The following configuration steps were taken detect whether any security group has been modified to include an inbound rule that allows connectons from 0.0.0.0/0 and automatically removes that modification. This is a high risk condition that would grant all IPv4 addresses on the Internet the ability to connect to AWS resources in the absence of other controls.

1. Created an IAM Lambda execution role called *prod-sg-remediation-function-role* and assigned the following permissions.
   
   <img width="1602" height="740" alt="prod-sg-remediation-function-role-policy" src="https://github.com/user-attachments/assets/c7602db7-5424-4d90-8090-a789f17431ee" />
   <img width="1622" height="362" alt="prod-sg-remediation-trust" src="https://github.com/user-attachments/assets/f8ca09aa-3e44-4d43-a9b7-f75d0a463452" />


2. Created an IAM role called *prod-sg-remediation-eventbridge-role* and assigned the following permissions.
   
   <img width="1614" height="287" alt="prod-sg-remediation-eventbridge-perm" src="https://github.com/user-attachments/assets/f16ddae5-3357-42d4-a25a-a5a288772695" />
   <img width="1622" height="361" alt="prod-eventbridge-trust" src="https://github.com/user-attachments/assets/df7d79e9-8fc5-4152-bee3-3bb649e69110" />


3. Created a Lambda function called *prod-sg-remediation-function* that uses a custom Python script (written by Claude). This script is invoked by EventBridge when an             *AuthorizeSecurityGroupsIngress* CloudTrail event is        detected, to determine whether 0.0.0.0/0 or ::/0 has been added as a rule, automatically revokes the rule(s) if found, writes logs to CloudWatch, and sends an email alert to the security team via the existing *prod-security-alerts* SNS topic.
   <img width="1630" height="803" alt="prod-sg-remediation-function" src="https://github.com/user-attachments/assets/73ccd6bc-3981-4736-bba3-4caddbdbc147" />


3. Created an EventBridge rule called *prod-sg-open-inbound-rule-alert* that matches the *AuthorizeSecurityGroupsIngress* event pattern from the CloudTrail event.
   <img width="1624" height="687" alt="prod-sg-open-inbound-rule-alert" src="https://github.com/user-attachments/assets/b4ccd649-48f4-4be5-b3e5-5351162592e9" />

   The rule's target is pointed to *prod-sg-remediation-function* while the permission for EventBridge to invoke the function is granted by the *prod-sg-remediation-eventbridge-role*.
   <img width="1478" height="232" alt="inbound-rule-alert-target" src="https://github.com/user-attachments/assets/aa36807b-632b-43dc-9a9d-fde961c6cf40" />


### Practical ###

1. Two new inbound rules are added to the *prod-rds-sg* security group that allows 0.0.0.0/0 and ::/0.
   <img width="1625" height="646" alt="prod-rds-sg-overlypermissive" src="https://github.com/user-attachments/assets/11808e0d-2af3-4699-9aea-dfdfa1006198" />


2. Within seconds, the EventBridge rule triggered the auto-remediation of the security group, removed the two rules, and sent an email notification to the security team.
   <img width="1623" height="506" alt="cloudwatch-auto-remediation" src="https://github.com/user-attachments/assets/777436cf-90a9-49e9-b05a-07e39f56ac1f" />
   <img width="1632" height="813" alt="cloudtrail-event1" src="https://github.com/user-attachments/assets/a8f203fb-5d5f-4063-8447-b6532b2962e3" />
   <img width="1627" height="809" alt="cloudtrail-event2" src="https://github.com/user-attachments/assets/645b12c7-79e2-439c-9050-30c7d92bb644" />
   <img width="1471" height="798" alt="email-auto-remediated" src="https://github.com/user-attachments/assets/169b2f75-01e5-427c-ab0f-8c8655b7e93b" />


## Compliance ##
[Security Hub Cloud Security Posture Management (CSPM)](https://docs.aws.amazon.com/securityhub/latest/userguide/what-is-securityhub.html) provides a comprehensive view of the security state in AWS by assessing the environment against security industry standards and best practices. After the lab was deployed, CSPM was enabled alongside AWS Config which is used to record and evaluate the configuration of the deployed resources.

The current AWS deployment will be evaluated against the following standards which have been enabled.

*CIS AWS Foundations Benchmark v5.0.0*

*PCI DSS v4.0.1*

*AWS Foundational Security Best Practices v1.0.0*

Within an hour, CSPM had assessed the AWS configuration and generated a security score of **83%**. The pass/fail breakdown can be seen below.


### Pre-remediation ###

<img width="1784" height="886" alt="cspm-dashboard" src="https://github.com/user-attachments/assets/bb06a078-dcf7-4204-9a50-b11ee7e58195" />


**CIS AWS Foundations Benchmark v5.0.0**
<img width="1786" height="964" alt="cisbenchmark" src="https://github.com/user-attachments/assets/b22e05a5-e3c2-47e1-afbc-66336d85feab" />



**PCI DSS v4.0.1**
<img width="1742" height="968" alt="pcidss" src="https://github.com/user-attachments/assets/31037f1b-35e3-4664-a4e1-a02da8540b5a" />



**AWS Foundational Security Best Practices v1.0.0**
<img width="1743" height="959" alt="foundational" src="https://github.com/user-attachments/assets/a5a0d492-9a62-46da-a56c-0ea8580b15d0" />



## Remediation ##
All Critical and High findings were prioritized and remediated.

| Severity | Finding                                          | Remediation Action | 
|----------|--------------------------------------------------|--------------|
| Critical | Hardware MFA should be enabled for the root user | Enrolled a YubiKey for the root user |
| Critical | SSM documents should have the block public sharing setting enabled | Disabled public sharing |
| High | VPC default security groups should not allow inbound or outbound traffic | Removed rules from default SG |
| High | CloudTrail should be enabled and configured with at least one multi-Region trail that includes read and write management events | Created a multi-region trail |
| High | IAM Access Analyzer external access analyzer should be enabled | Enabled IAM Access Analyzer for external access |
| High | At least one CloudTrail trail should be enabled | Remediated with the creation of the multi-region trail |
| High | GuardDuty EKS Runtime Monitoring should be enabled | Enabled EKS Runtime Monitoring |
| High | Amazon EBS Snapshots should not be publicly accessible |  Blocked public access globally |

During the remediation effort of the Security Hub findings, an additional High finding from Amazon Inspector was discovered.


## Amazon Inspector ##
Amazon [Inspector](https://docs.aws.amazon.com/inspector/latest/user/what-is-inspector.html) is a vulnerability management service that automatically discovers workloads and scans them for software vulnerabilities and unintended network exposure. Inspector reported the following vulnerability to Security Hub on the *prod-customer-account-function*.

<img width="1631" height="921" alt="inspector-finding" src="https://github.com/user-attachments/assets/5e23d23e-13bf-49c8-a048-eba822697787" />

<img width="424" height="248" alt="resources" src="https://github.com/user-attachments/assets/b0fbfc34-8a34-4def-aea5-5428be52d36c" />


The *fast-xml-parser* NPM package was updated from 5.3.4 to 5.3.6 to remediate the finding.


### Post-remediation ###
All Critical and High findings have been remediated.

<img width="1793" height="676" alt="cspm-remediated" src="https://github.com/user-attachments/assets/fcd8cb01-9715-4018-becc-33a05903eafe" />

<img width="1629" height="744" alt="cspm-post-remediation" src="https://github.com/user-attachments/assets/57dca812-b69f-4fc8-86a2-2eae0ac7a61b" />


## Conclusion ##
This lab explored several AWS-native services and their role in supporting a basic web application. An emphasis on security was maintained throughout the lab, as the topics of data security, access control, vulnerability management, logging and alerting, and compliance demonstrated the importance of a defense in-depth, layered approach to protecting company assets. 


## References ##

[Route 53](https://aws.amazon.com/route53/)

[CloudFront](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Introduction.html)

[SPAs](https://softwaremind.com/blog/what-is-a-single-page-application-spa/)

[AWS Simple Storage Service (S3)](https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html)

[Cognito](https://docs.aws.amazon.com/cognito/latest/developerguide/what-is-amazon-cognito.html)

[API Gateway](https://docs.aws.amazon.com/apigateway/latest/developerguide/welcome.html)

[Lambda](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html)

[RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Welcome.html)

[Secrets Manager](https://docs.aws.amazon.com/secretsmanager/latest/userguide/intro.html)

[DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html)

[KMS](https://docs.aws.amazon.com/kms/latest/developerguide/overview.html)

[Bedrock](https://aws.amazon.com/bedrock/)

[Guardrails](https://aws.amazon.com/bedrock/guardrails/)

[VPC endpoints](https://wa.aws.amazon.com/wellarchitected/2020-07-02T19-33-23/wat.concept.vpc-endpoint.en.html)

[Security Groups](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-security-groups.html)

[IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html)

[CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-user-guide.html)

[Simple Notification Service (SNS)](https://docs.aws.amazon.com/sns/latest/dg/welcome.html)

[EventBridge](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-what-is.html)

[GuardDuty](https://docs.aws.amazon.com/guardduty/latest/ug/what-is-guardduty.html)

[Macie](https://docs.aws.amazon.com/macie/latest/user/what-is-macie.html)

[Security Hub Cloud Security Posture Management (CSPM)](https://docs.aws.amazon.com/securityhub/latest/userguide/what-is-securityhub.html)

[Inspector](https://docs.aws.amazon.com/inspector/latest/user/what-is-inspector.html)
