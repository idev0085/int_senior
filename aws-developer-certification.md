# AWS Certified Developer – Associate (DVA-C02) Complete Guide

## Table of Contents
1. [What is AWS Certified Developer?](#what-is-aws-certified-developer)
2. [Exam Overview & Structure](#exam-overview--structure)
3. [Study Roadmap & Preparation Tips](#study-roadmap--preparation-tips)
4. [Domain 1: Development with AWS Services](#domain-1-development-with-aws-services)
5. [Domain 2: Security](#domain-2-security)
6. [Domain 3: Deployment](#domain-3-deployment)
7. [Domain 4: Troubleshooting & Optimization](#domain-4-troubleshooting--optimization)
8. [Core AWS Services Deep Dive](#core-aws-services-deep-dive)
9. [Practice Questions & Answers](#practice-questions--answers)
10. [Cheat Sheet](#cheat-sheet)

---

## What is AWS Certified Developer?

### Overview
The **AWS Certified Developer – Associate** certification validates your ability to develop, deploy, and debug cloud-based applications using AWS services. It is ideal for developers who work with AWS APIs, SDKs, and services.

### Who Should Take This?
- Software developers with at least 1 year of hands-on AWS experience
- Developers wanting to demonstrate cloud expertise
- Professionals aiming for a career in cloud development
- Full-stack developers expanding to cloud-native development

### Prerequisites
- AWS Cloud Practitioner (recommended, not required)
- Basic understanding of programming (Python, Java, Node.js, etc.)
- Familiarity with web services, APIs, and databases
- Understanding of Linux/networking basics

### Certification Path
```
AWS Cloud Practitioner (Foundational)
        ↓
AWS Certified Developer – Associate  ← You are here
        ↓
AWS Certified DevOps Engineer – Professional
        or
AWS Solutions Architect – Associate
```

---

## Exam Overview & Structure

### Exam Details (DVA-C02)
| Detail | Info |
|--------|------|
| Exam Code | DVA-C02 |
| Duration | 130 minutes |
| Questions | 65 questions |
| Passing Score | 720/1000 |
| Cost | $150 USD |
| Format | Multiple choice, Multiple select |
| Languages | English, Japanese, Korean, Simplified Chinese |
| Valid For | 3 years |

### Exam Domains & Weightage

| Domain | Topic | Weight |
|--------|-------|--------|
| Domain 1 | Development with AWS Services | 32% |
| Domain 2 | Security | 26% |
| Domain 3 | Deployment | 24% |
| Domain 4 | Troubleshooting & Optimization | 18% |

### Question Types
- **Single answer**: Choose the best option
- **Multiple answer**: Choose 2-3 correct options (stated clearly)
- **Scenario-based**: Real-world problem with best solution

---

## Study Roadmap & Preparation Tips

### Phase 1: Foundations (Weeks 1–2)
```
Week 1:
  ✅ AWS Global Infrastructure (Regions, AZs, Edge Locations)
  ✅ IAM (Users, Groups, Roles, Policies)
  ✅ AWS Free Tier account setup
  ✅ AWS CLI setup and configuration
  ✅ Core services overview (EC2, S3, RDS, Lambda)

Week 2:
  ✅ EC2 (instances, AMIs, security groups)
  ✅ S3 (buckets, policies, versioning, encryption)
  ✅ VPC basics (subnets, routing, NAT gateway)
  ✅ RDS and DynamoDB overview
```

### Phase 2: Core Developer Services (Weeks 3–5)
```
Week 3:
  ✅ Lambda (functions, triggers, layers, cold starts)
  ✅ API Gateway (REST, HTTP, WebSocket APIs)
  ✅ DynamoDB (tables, indexes, streams, DAX)
  ✅ SQS and SNS (queues, topics, fan-out)

Week 4:
  ✅ CodeCommit, CodeBuild, CodeDeploy, CodePipeline
  ✅ Elastic Beanstalk (environments, deployment modes)
  ✅ CloudFormation (templates, stacks, changsets)
  ✅ ECR and ECS basics

Week 5:
  ✅ Cognito (User Pool, Identity Pool)
  ✅ KMS (keys, encryption, key policies)
  ✅ Secrets Manager vs Parameter Store
  ✅ X-Ray (tracing, segments, subsegments, annotations)
```

### Phase 3: Security & Optimization (Week 6)
```
Week 6:
  ✅ Security: IAM roles, STS, policies
  ✅ CloudWatch (metrics, logs, alarms, dashboards)
  ✅ Elastic Cache (Redis, Memcached)
  ✅ Step Functions, EventBridge
  ✅ SAM (Serverless Application Model)
```

### Phase 4: Practice & Review (Weeks 7–8)
```
Week 7–8:
  ✅ Practice exams (minimum 3 full tests)
  ✅ Review weak areas
  ✅ Hands-on labs in AWS console
  ✅ Read AWS whitepapers
  ✅ Flashcard review
```

### Best Study Resources
| Resource | Type | Cost |
|----------|------|------|
| AWS Skill Builder | Official course + practice exams | Free / Paid |
| Stephane Maarek (Udemy) | Video course | ~$15 |
| A Cloud Guru / Pluralsight | Video + Labs | Subscription |
| AWS Documentation | Official docs | Free |
| Tutorials Dojo | Practice exams | ~$15 |
| Jon Bonso Practice Exams | Practice exams | ~$15 |
| AWS Free Tier | Hands-on practice | Free |

### Preparation Tips for Beginners
1. **Hands-on is essential** – Create real AWS resources, not just watch videos
2. **Use AWS Free Tier** – Most services have free usage to practice
3. **Read error messages** – AWS error messages contain critical hints
4. **Understand WHY** – Don't memorize, understand use cases
5. **Focus on SDK/API** – Developer exam tests code-level knowledge
6. **Study encryption** – KMS, SSE, client-side encryption heavily tested
7. **Learn IAM deeply** – Most questions have IAM angle
8. **Practice PromQL for CloudWatch** – Know how to write filter/metric expressions
9. **Read official AWS FAQ pages** – Often contain exam-ready content
10. **Time yourself** – 2 minutes per question average

---

## Domain 1: Development with AWS Services

### 1.1 Lambda
**What is Lambda?**
AWS Lambda is a serverless compute service that runs code without managing servers.

**Key Concepts:**
```
- Max execution timeout: 15 minutes
- Memory: 128 MB – 10,240 MB (10 GB)
- Ephemeral storage (/tmp): 512 MB – 10 GB
- Max deployment package: 50 MB (zipped), 250 MB (unzipped)
- Max concurrent executions: 1,000 (default, can increase)
- Supported runtimes: Node.js, Python, Java, Go, Ruby, .NET, custom
```

**Lambda Triggers:**
```
API Gateway → HTTP requests
S3 → Object events (put, delete)
DynamoDB Streams → Table changes
SQS → Message processing
SNS → Topic notifications
EventBridge → Scheduled/event-based
Cognito → User pool events
ALB → HTTP requests
Kinesis → Stream data
CloudWatch Logs → Log events
```

**Lambda Invocation Types:**
```
Synchronous:  Client waits for response (API Gateway, CLI --invocation-type RequestResponse)
Asynchronous: Fire and forget (S3, SNS, EventBridge) - retries 2x on failure
Event Source Mapping: Polls source and invokes (SQS, DynamoDB Streams, Kinesis)
```

**Cold Start vs Warm Start:**
```javascript
// Cold start: new container spun up (slower, ~100ms–1s+)
// Warm start: existing container reused (fast, <1ms)

// Mitigation strategies:
// 1. Provisioned Concurrency (pre-warm containers)
// 2. Keep functions small (less code = faster init)
// 3. Minimize runtime dependencies
// 4. Use Lambda SnapStart (Java only)
```

**Lambda Environment Variables:**
```javascript
// Access environment variables
const dbUrl = process.env.DATABASE_URL;
const stage = process.env.STAGE;

// Encrypted at rest using KMS
// Use Secrets Manager for sensitive values
```

**Lambda Layers:**
```
- Share code/libraries across functions
- Max 5 layers per function
- Max total unzipped size: 250 MB
- Good for: shared utilities, large dependencies (numpy, pandas)
```

**Lambda Concurrency:**
```
Reserved concurrency:  Guarantee X executions for a function
Provisioned concurrency: Pre-warm X containers (eliminates cold starts)
Burst concurrency: Initial burst of 500-3000 (region dependent)
Throttling: 429 TooManyRequestsException
```

**Lambda with SQS:**
```javascript
// Lambda SQS Event
exports.handler = async (event) => {
  for (const record of event.Records) {
    const body = JSON.parse(record.body);
    // Process message
    console.log('Message:', body);
    // If no error thrown, message auto-deleted from queue
  }
};

// Batch size: 1–10,000 messages per invocation (FIFO: max 10)
// If ONE record fails, ENTIRE batch goes back to queue
// Use partial batch responses to fail individual records
```

**Lambda Destinations:**
```
On Success: → SQS, SNS, Lambda, EventBridge
On Failure: → SQS, SNS, Lambda, EventBridge
DLQ (Dead Letter Queue): SQS or SNS (older method)
```

---

### 1.2 API Gateway

**Types of API Gateway:**
```
REST API:     Full features, proxy/non-proxy, usage plans, cache
HTTP API:     Cheaper, faster, limited features (no usage plans)
WebSocket:    Two-way real-time communication
```

**Integration Types:**
```
Lambda Proxy:      Request/response passed as-is to Lambda
Lambda Custom:     You define request/response mapping
HTTP Proxy:        Forward to HTTP endpoint
HTTP Custom:       Forward with transformations
AWS Service:       Integrate directly with AWS services
Mock:              Return response without backend
```

**API Gateway Caching:**
```javascript
// Stage-level caching, TTL: 0–3600 seconds (default 300s)
// Cache capacity: 0.5 GB – 237 GB
// Cache key: by default = method + path + query params

// Invalidate cache:
// Header: Cache-Control: max-age=0
// Must have proper IAM permission
```

**API Gateway Stages:**
```
dev → Stage variables (e.g., Lambda alias, backend URL)
staging
production

// Stage Variables usage in Lambda ARN:
arn:aws:lambda:us-east-1:123:function:MyApp:${stageVariables.lambdaAlias}
```

**API Gateway Deployment:**
```
Canary: Route % of traffic to new deployment (test before full deploy)
Stage settings: Throttling, logging, cache per stage
```

**Request/Response Mapping:**
```json
// Mapping template (VTL - Velocity Template Language)
{
  "body": $input.json('$'),
  "headers": {
    "x-custom-header": "$input.params('x-custom-header')"
  },
  "queryParams": {
    "param1": "$input.params('param1')"
  }
}
```

**API Gateway Error Codes:**
```
400 - Bad Request
401 - Unauthorized
403 - Forbidden
404 - Resource not found
429 - Too Many Requests (throttled)
500 - Integration failure
502 - Bad Gateway (Lambda syntax error)
503 - Service Unavailable
504 - Endpoint request timeout (>29s)
```

**API Gateway Authorization:**
```
IAM Authorization:   Sign requests with Sig V4, for internal/machine-to-machine
Lambda Authorizer:   Custom logic (JWT, OAuth, custom headers)
Cognito User Pools:  JWT verification - built-in, no custom code
```

---

### 1.3 DynamoDB

**Core Concepts:**
```
Table:      Top-level container for data
Item:       A single row (max 400 KB)
Attribute:  A field in an item
Primary Key: Uniquely identifies each item

Partition Key (PK): Simple primary key - must be unique
Sort Key (SK):      Combined with PK → composite key - PK can repeat
```

**Capacity Modes:**
```
Provisioned:  You specify RCU/WCU  (cheaper for predictable)
On-Demand:   AWS auto-scales       (more expensive, unpredictable traffic)

RCU (Read Capacity Unit):
  - 1 strongly consistent read/sec (up to 4KB)
  - 2 eventually consistent reads/sec (up to 4KB)

WCU (Write Capacity Unit):
  - 1 write/sec (up to 1KB)
```

**Consistency Models:**
```
Eventually Consistent Reads: Default, cheaper, slight delay, uses 0.5 RCU
Strongly Consistent Reads:   Latest data, costs 1 RCU, set ConsistentRead=true
Transactional Reads:         ACID, 2x RCU
```

**DynamoDB API Operations:**
```
GetItem        - Read one item by PK (+ SK if composite)
PutItem        - Create or replace entire item
UpdateItem     - Modify specific attributes
DeleteItem     - Remove item
Query          - Get items by PK (+ optional SK filter) — fast, efficient
Scan           - Read ENTIRE table — slow, expensive, avoid in production
BatchGetItem   - Up to 100 items, 16 MB
BatchWriteItem - PutItem/DeleteItem, up to 25 items, 16 MB
TransactGetItems  - Up to 25 items atomically
TransactWriteItems - Up to 25 items atomically
```

**Indexes:**
```
LSI (Local Secondary Index):
  - Same PK, different SK
  - Must be created at table creation
  - Max 5 per table
  - Shares RCU/WCU with base table

GSI (Global Secondary Index):
  - Different PK and/or SK
  - Can be created anytime
  - Max 20 per table
  - Has own RCU/WCU
  - Eventual consistency only
```

**DynamoDB Streams:**
```
- Ordered stream of item-level modifications
- Retention: 24 hours
- Used with: Lambda, Kinesis, replication

Stream Views:
  KEYS_ONLY       - PK attributes only
  NEW_IMAGE        - Full item after modification
  OLD_IMAGE        - Full item before modification
  NEW_AND_OLD_IMAGES - Both before and after
```

**DynamoDB Accelerator (DAX):**
```
- In-memory cache for DynamoDB
- Microsecond read latency (vs millisecond)
- No code changes needed
- Cluster: 3 nodes (multi-AZ)
- Write-through cache
- Does NOT cache Scan/Query results by default
- Item cache TTL: 5 minutes default
```

**DynamoDB TTL:**
```javascript
// Automatically delete items after expiration
// Set TTL attribute with Unix epoch timestamp
{
  "userId": "123",
  "sessionToken": "abc",
  "ttl": 1735689600  // Unix timestamp (2026-01-01 00:00:00)
}
// Items deleted within 48 hours of expiry
// Free of charge, no WCU consumed
```

**Conditional Expressions:**
```javascript
// Prevent overwriting existing items
const params = {
  TableName: 'Users',
  Item: { userId: '123', name: 'John' },
  ConditionExpression: 'attribute_not_exists(userId)'
};

// Optimistic locking with version number
const params = {
  TableName: 'Items',
  Key: { itemId: '123' },
  UpdateExpression: 'SET version = :newVersion',
  ConditionExpression: 'version = :expectedVersion',
  ExpressionAttributeValues: {
    ':newVersion': 2,
    ':expectedVersion': 1
  }
};
```

---

### 1.4 S3

**S3 Storage Classes:**
```
S3 Standard:           Active data, high availability (3 AZs), most expensive
S3 Intelligent-Tiering: Auto-moves between tiers based on access patterns
S3 Standard-IA:        Infrequent access, retrieval fee, 3 AZs
S3 One Zone-IA:        Single AZ, cheaper, risk of loss
S3 Glacier Instant:    Archiving, milliseconds retrieval
S3 Glacier Flexible:   Archiving, minutes–hours retrieval
S3 Glacier Deep Archive: Lowest cost, 12–48 hours retrieval
```

**S3 Encryption:**
```
SSE-S3:    AWS manages keys (AES-256), header: x-amz-server-side-encryption: AES256
SSE-KMS:   Customer controls via KMS, header: x-amz-server-side-encryption: aws:kms
SSE-C:     Customer provides key in request, HTTPS required
CSE:       Client encrypts before uploading (AWS Encryption SDK)
```

**S3 Versioning:**
```
- Must be enabled per bucket
- Once enabled, cannot be disabled (only suspended)
- Delete creates a delete marker
- Restore: delete the delete marker
- Permanently delete: specify version ID
- Old versions still billed for storage
```

**S3 Pre-signed URLs:**
```javascript
// Generate a temporary URL for private objects
const { S3Client, GetObjectCommand } = require("@aws-sdk/client-s3");
const { getSignedUrl } = require("@aws-sdk/s3-request-presigner");

const client = new S3Client({ region: "us-east-1" });
const command = new GetObjectCommand({
  Bucket: "my-bucket",
  Key: "my-file.pdf"
});

// URL valid for 1 hour (3600 seconds)
const url = await getSignedUrl(client, command, { expiresIn: 3600 });
// Default expiry: 3600s, Max: 7 days (604800s)
```

**S3 Events:**
```
s3:ObjectCreated:*   - On any object creation
s3:ObjectRemoved:*   - On delete
s3:ObjectRestore:*   - On Glacier restore

Destinations: Lambda, SQS, SNS
or use EventBridge for more targets/filtering
```

**S3 Multipart Upload:**
```
- Recommended for files > 100 MB
- Required for files > 5 GB
- Parallel upload for better throughput
- Each part 5 MB – 5 GB

// Lifecycle: auto-abort incomplete multipart uploads
```

**S3 Transfer Acceleration:**
```
- Uses CloudFront edge locations
- Upload to edge, then AWS backbone to bucket
- Custom endpoint: bucket.s3-accelerate.amazonaws.com
- Extra cost, but faster for global uploads
```

**S3 CORS:**
```json
// Allow cross-origin requests
[
  {
    "AllowedHeaders": ["*"],
    "AllowedMethods": ["GET", "PUT", "POST"],
    "AllowedOrigins": ["https://example.com"],
    "ExposeHeaders": ["ETag"],
    "MaxAgeSeconds": 3000
  }
]
```

---

### 1.5 SQS & SNS

**SQS (Simple Queue Service):**
```
Standard Queue:         At-least-once delivery, best-effort ordering
FIFO Queue:             Exactly-once, strict order, 300 TPS (3,000 with batching)

Message Size:           Max 256 KB (use S3 for larger)
Retention:              1 min – 14 days (default 4 days)
Visibility Timeout:     0s – 12 hours (default 30s)
Long Polling:           Wait up to 20s for messages (reduces empty fetches)
Short Polling:          Returns immediately (even if empty)
```

**SQS Key Concepts:**
```
Visibility Timeout:
  - Message hidden from others while being processed
  - If not deleted in time, becomes visible again
  - Set longer than processing time

Dead Letter Queue (DLQ):
  - After maxReceiveCount retries (1–1000), move to DLQ
  - Good for debugging poison pills

Delay Queue:
  - Delay message delivery (0 – 900 seconds)
  - MessageDelay per message OR queue-level default

Message Group ID (FIFO):
  - Messages in same group processed in order
  - Different groups processed in parallel
```

**SQS with Lambda:**
```javascript
exports.handler = async (event) => {
  // Batch size: 1–10,000 per invocation
  const results = [];
  for (const record of event.Records) {
    try {
      const body = JSON.parse(record.body);
      await processMessage(body);
    } catch (error) {
      // Report as batch item failure for partial batch response
      results.push({ itemIdentifier: record.messageId });
    }
  }
  // Return failures for partial batch processing
  return { batchItemFailures: results };
};
```

**SNS (Simple Notification Service):**
```
Topic:    Publisher sends message to topic
Subscription: Subscribers receive messages (Lambda, SQS, HTTP/S, Email, SMS)

Fan-out Pattern:
  SNS Topic → multiple SQS Queues → multiple Lambda functions

Message Filtering:
  Filter messages per subscription using policies
  Subscribers only get relevant messages

SNS FIFO:
  - Strict ordering within message group
  - Only SQS FIFO can subscribe
  - Max 300 messages/sec (3,000 with batching)
```

**Kinesis Data Streams:**
```
- Real-time streaming, ordered, replay-able
- Shards: 1 MB/s in, 2 MB/s out per shard
- Retention: 1–365 days (default 24h)
- Records up to 1 MB
- Partition key determines shard

vs SQS:
  SQS:     Queue, at-least-once, no replay, few consumers
  Kinesis: Stream, ordered, replay, multiple consumers, real-time
```

---

### 1.6 Elastic Beanstalk

**What is Elastic Beanstalk?**
Platform-as-a-Service (PaaS) that handles deployment, scaling, load balancing, and monitoring. You just bring the code.

**Deployment Policies:**
```
All at once:      Fastest, downtime during deployment
Rolling:          Update few instances at a time, reduced capacity
Rolling with additional batch: Launches new instances, no capacity loss
Immutable:        New ASG with new instances, safest, slowest
Blue/Green:       Separate environments, swap URL, zero downtime
Traffic Splitting: Canary testing with % traffic to new env
```

**Environment Types:**
```
Web Server:  Runs HTTP servers (EC2 + ALB + Auto Scaling)
Worker:      Processes background jobs (EC2 + SQS)
```

**Beanstalk Configuration:**
```yaml
# .ebextensions/options.config
option_settings:
  aws:autoscaling:asg:
    MinSize: 2
    MaxSize: 10
  aws:elasticbeanstalk:application:environment:
    NODE_ENV: production
    DATABASE_URL: my-db-endpoint
  aws:ec2:instances:
    InstanceTypes: t3.medium
```

**Beanstalk Deployment with Docker:**
```json
// Dockerrun.aws.json (v2 - ECS based)
{
  "AWSEBDockerrunVersion": 2,
  "containerDefinitions": [
    {
      "name": "my-app",
      "image": "my-ecr-image:latest",
      "essential": true,
      "memory": 256,
      "portMappings": [
        { "hostPort": 80, "containerPort": 3000 }
      ]
    }
  ]
}
```

---

### 1.7 CloudFormation

**CloudFormation Template Structure:**
```yaml
AWSTemplateFormatVersion: "2010-09-09"
Description: "My stack description"

Parameters:               # Input values
  Environment:
    Type: String
    Default: dev
    AllowedValues: [dev, staging, prod]

Mappings:                # Key-value lookups
  RegionAmi:
    us-east-1:
      AMI: ami-12345678
    us-west-2:
      AMI: ami-87654321

Conditions:              # Conditional resource creation
  IsProduction: !Equals [!Ref Environment, prod]

Resources:               # AWS resources (REQUIRED)
  MyBucket:
    Type: AWS::S3::Bucket
    Properties:
      BucketName: my-app-bucket
      VersioningConfiguration:
        Status: Enabled

  MyLambda:
    Type: AWS::Lambda::Function
    Properties:
      FunctionName: my-function
      Runtime: nodejs18.x
      Role: !GetAtt LambdaRole.Arn
      Handler: index.handler

Outputs:                 # Stack output values
  BucketName:
    Value: !Ref MyBucket
    Export:
      Name: !Sub "${AWS::StackName}-BucketName"
```

**CloudFormation Intrinsic Functions:**
```yaml
!Ref          - Reference parameter or resource
!GetAtt       - Get attribute of a resource
!Sub          - String substitution
!Join         - Join strings
!Select       - Select from list
!If           - Conditional value
!And          - Logical AND
!Or           - Logical OR
!Not          - Logical NOT
!Equals       - Equality check
!FindInMap    - Look up map value
!ImportValue  - Import exported value from another stack
!Base64       - Base64 encode
!Cidr         - CIDR block generation
```

**CloudFormation Drift:**
```
- Detect if actual resources differ from template
- Caused by manual console/CLI changes
- Fix: update stack or import resource
```

**StackSets:**
```
- Deploy stacks across multiple accounts and regions
- Useful for organization-wide standards enforcement
```

---

## Domain 2: Security

### 2.1 IAM Deep Dive

**IAM Policy Structure:**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowS3ReadOnly",
      "Effect": "Allow",
      "Principal": "*",  // For resource-based policies only
      "Action": [
        "s3:GetObject",
        "s3:ListBucket"
      ],
      "Resource": [
        "arn:aws:s3:::my-bucket",
        "arn:aws:s3:::my-bucket/*"
      ],
      "Condition": {
        "StringEquals": {
          "s3:prefix": ["home/", "public/"]
        },
        "IpAddress": {
          "aws:SourceIp": "203.0.113.0/24"
        }
      }
    }
  ]
}
```

**Policy Types:**
```
Identity-based:   Attached to IAM user/group/role
Resource-based:   Attached to resource (S3 bucket, Lambda, etc.)
Permission boundary: Max permissions an IAM entity can have
SCPs (Service Control Policies): Organization-wide guardrails (AWS Organizations)
Session policies: Passed when assuming role

Evaluation order:
  DENY always wins over ALLOW
  Implicit DENY if no matching Allow
  Explicit DENY overrides everything
```

**IAM Roles & STS:**
```
STS (Security Token Service):
  - Provide temporary credentials (Access Key, Secret, Session Token)
  - AssumeRole:          Cross-account access
  - AssumeRoleWithSAML:  Federation with SAML IdP
  - AssumeRoleWithWebIdentity: Federation with web IdP (old method)
  - GetFederationToken:  User-based federation

// Assume role example
aws sts assume-role \
  --role-arn arn:aws:iam::123456789:role/MyRole \
  --role-session-name MySession
```

**IAM Best Practices:**
```
1. Never use root account for daily work
2. Create admin IAM user → use for admin work
3. Least privilege: Grant minimum required permissions
4. Use IAM roles for EC2/Lambda (not access keys)
5. Enable MFA for all users
6. Rotate access keys regularly
7. Use IAM Groups to assign permissions
8. Use Conditions to restrict access further
9. Enable CloudTrail for audit logging
10. Use AWS Organizations SCPs for guardrails
```

---

### 2.2 KMS (Key Management Service)

**KMS Key Types:**
```
AWS Managed Keys (aws/service):
  - Free, auto-rotated every year
  - Cannot control policy
  - Used by default for AWS services

Customer Managed Keys (CMK):
  - You create and manage
  - $1/month + API call fees
  - Full control over policy/rotation
  - Can be asymmetric

AWS Owned Keys:
  - Used by AWS, invisible to you
  - Free, fully managed
```

**KMS Operations:**
```javascript
const { KMSClient, EncryptCommand, DecryptCommand, GenerateDataKeyCommand } = require("@aws-sdk/client-kms");

const kms = new KMSClient({ region: "us-east-1" });
const KEY_ID = "arn:aws:kms:us-east-1:123:key/my-key-id";

// Encrypt (max 4 KB directly)
const encrypt = await kms.send(new EncryptCommand({
  KeyId: KEY_ID,
  Plaintext: Buffer.from("my secret data")
}));
const ciphertext = encrypt.CiphertextBlob;

// Decrypt
const decrypt = await kms.send(new DecryptCommand({
  CiphertextBlob: ciphertext
}));
const plaintext = Buffer.from(decrypt.Plaintext).toString();

// For large data: Envelope Encryption
const dataKey = await kms.send(new GenerateDataKeyCommand({
  KeyId: KEY_ID,
  KeySpec: "AES_256"
}));
// Use dataKey.Plaintext to encrypt large data locally
// Store dataKey.CiphertextBlob alongside encrypted data
```

**Envelope Encryption:**
```
1. Generate Data Key (DEK) from KMS
2. Use DEK to encrypt your large data (locally)
3. Encrypt the DEK with KMS CMK
4. Store encrypted DEK with encrypted data
5. To decrypt: decrypt DEK with KMS, then decrypt data with DEK

Why: KMS API encrypts only up to 4 KB
     Envelope encryption handles any data size
```

**KMS Multi-Region Keys:**
```
- Same key material in different regions
- Key ID starts with mrk-
- Used for disaster recovery, global apps
- Encrypt in one region, decrypt in another
```

---

### 2.3 Secrets Manager

**Secrets Manager vs SSM Parameter Store:**
```
Feature              | Secrets Manager   | SSM Parameter Store
---------------------|-------------------|---------------------
Cost                 | $0.40/secret/mo   | Free (Standard), $0.05/mo (Advanced)
Rotation             | Automatic (Lambda) | Manual
Cross-account        | Yes               | Limited
Encryption           | Always KMS        | Optional KMS
Size                 | Up to 65 KB       | 4 KB (Standard), 8 KB (Advanced)
Versioning           | Yes               | Yes
Use case             | DB passwords, API keys | Config, non-secret params
```

**Secrets Manager with Lambda:**
```javascript
const { SecretsManagerClient, GetSecretValueCommand } = require("@aws-sdk/client-secrets-manager");

const client = new SecretsManagerClient({ region: "us-east-1" });

// Cache the secret to reduce API calls
let cachedSecret;
const getSecret = async (secretName) => {
  if (cachedSecret) return cachedSecret;
  
  const response = await client.send(new GetSecretValueCommand({
    SecretId: secretName,
    VersionStage: "AWSCURRENT"
  }));
  
  cachedSecret = JSON.parse(response.SecretString);
  return cachedSecret;
};
```

**Automatic Rotation:**
```
- Lambda function rotates secret on schedule
- AWS provides rotation templates for RDS, Redshift, DocumentDB
- Custom function: implement 4 steps
  1. createSecret
  2. setSecret
  3. testSecret
  4. finishSecret
```

---

### 2.4 Cognito

**Cognito User Pools:**
```
- User directory for your app
- Sign up/sign in functionality
- Password policies, MFA
- JWT tokens (ID token, Access token, Refresh token)
- Integrates with API Gateway directly
- Social identity provider federation (Google, Facebook)
- SAML federation

// User Pool Token Types:
ID Token:      User identity claims (email, name, etc.)
Access Token:  Authorization (scopes), for API calls
Refresh Token: Get new ID/Access tokens (30 days default)
```

**Cognito Identity Pools:**
```
- Provide AWS credentials to users
- Federate with User Pools, Google, Facebook, SAML
- Authenticated vs unauthenticated roles
- Grants temporary AWS credentials via STS

Flow:
  User authenticates → gets token (Cognito/Google/etc.)
  → Send token to Identity Pool
  → Get STS credentials
  → Access AWS services directly (S3, DynamoDB, etc.)
```

**User Pool vs Identity Pool:**
```
User Pool:     Authentication → JWT tokens → API Gateway/APIs
Identity Pool: Authorization  → AWS credentials → AWS services

Combined flow:
  App → User Pool (authenticate) → Token
      → Identity Pool (exchange token) → AWS credentials
      → Access DynamoDB/S3 directly
```

---

## Domain 3: Deployment

### 3.1 CodePipeline & CI/CD

**CodeCommit:**
```
- Managed Git repository (like GitHub/GitLab)
- Integrated with IAM for access control
- Supports HTTPS and SSH
- Triggers: Lambda, SNS, CodePipeline

// Clone:
git clone https://git-codecommit.us-east-1.amazonaws.com/v1/repos/MyRepo
```

**CodeBuild:**
```
- Fully managed build service
- No servers to manage
- Runs buildspec.yml for build instructions
- Produces build artifacts (uploaded to S3)
- Docker-based build environments

buildspec.yml:
  version: 0.2
  phases:
    install:
      runtime-versions:
        nodejs: 18
      commands:
        - npm install
    pre_build:
      commands:
        - echo Pre-build started
        - npm run lint
    build:
      commands:
        - echo Build started
        - npm run build
        - npm test
    post_build:
      commands:
        - echo Build complete
  artifacts:
    files:
      - dist/**/*
      - package.json
    base-directory: .
  cache:
    paths:
      - node_modules/**/*
```

**CodeDeploy:**
```
Platforms: EC2/On-premises, Lambda, ECS

Deployment group: Set of instances/Lambda to deploy to

Deployment Strategies:
  EC2/On-Premises:
    AllAtOnce:         All at once, fastest, downtime risk
    HalfAtATime:       50% at a time
    OneAtATime:        One instance at a time, slowest, safest
    Custom:            Your own percentage

  Lambda:
    AllAtOnce:         All traffic at once
    Canary10Percent5Minutes: 10% → 5 min → 100%
    Linear10PercentEvery1Minute: 10% every minute
    Custom: Your own configuration

  ECS:
    Canary:  Like Lambda canary
    Linear:  Like Lambda linear
    AllAtOnce

AppSpec file (appspec.yml):
  version: 0.0
  os: linux
  files:
    - source: /
      destination: /var/www/html
  hooks:
    BeforeInstall:
      - location: scripts/beforeInstall.sh
    AfterInstall:
      - location: scripts/afterInstall.sh
    ApplicationStart:
      - location: scripts/start.sh
    ValidateService:
      - location: scripts/validate.sh
```

**CodePipeline:**
```
Pipeline stages:
  Source:   CodeCommit, GitHub, S3, ECR
  Build:    CodeBuild, Jenkins
  Test:     CodeBuild, Device Farm
  Deploy:   CodeDeploy, Beanstalk, CloudFormation, ECS, S3

- Artifacts stored in S3 between stages
- Can add manual approval stage
- Triggers on source changes automatically
```

---

### 3.2 SAM (Serverless Application Model)

**What is SAM?**
SAM is a CloudFormation extension for serverless resources. Simplifies definition of Lambda, API Gateway, DynamoDB, SQS.

**SAM Template:**
```yaml
AWSTemplateFormatVersion: "2010-09-09"
Transform: AWS::Serverless-2016-10-31  # Required for SAM
Description: My Serverless App

Globals:                              # Shared settings
  Function:
    Timeout: 10
    MemorySize: 256
    Runtime: nodejs18.x
    Environment:
      Variables:
        STAGE: !Ref Stage

Parameters:
  Stage:
    Type: String
    Default: dev

Resources:
  # Lambda function
  MyFunction:
    Type: AWS::Serverless::Function
    Properties:
      Handler: src/handler.main
      Events:
        ApiEvent:
          Type: Api
          Properties:
            Path: /users
            Method: GET
        ScheduledEvent:
          Type: Schedule
          Properties:
            Schedule: rate(5 minutes)
        SQSEvent:
          Type: SQS
          Properties:
            Queue: !GetAtt MyQueue.Arn
            BatchSize: 10

  # DynamoDB Table
  UsersTable:
    Type: AWS::Serverless::SimpleTable
    Properties:
      TableName: Users
      PrimaryKey:
        Name: userId
        Type: String

  # SQS Queue
  MyQueue:
    Type: AWS::SQS::Queue
    Properties:
      VisibilityTimeout: 30

  # API Gateway
  MyApi:
    Type: AWS::Serverless::Api
    Properties:
      StageName: !Ref Stage
      Cors:
        AllowMethods: "'GET,POST,PUT,DELETE'"
        AllowHeaders: "'Content-Type,Authorization'"
        AllowOrigin: "'*'"
```

**SAM CLI Commands:**
```bash
# Initialize new SAM project
sam init

# Build application
sam build

# Test locally
sam local invoke MyFunction --event event.json
sam local start-api           # Start local API Gateway

# Deploy
sam deploy --guided           # First time (creates samconfig.toml)
sam deploy                    # Subsequent deploys

# Validate template
sam validate

# View logs
sam logs -n MyFunction --stack-name my-app --tail

# Sync (watch mode deploy)
sam sync --stack-name my-app --watch
```

---

### 3.3 Containers (ECR, ECS, EKS)

**ECR (Elastic Container Registry):**
```bash
# Authenticate Docker with ECR
aws ecr get-login-password --region us-east-1 | \
  docker login --username AWS --password-stdin \
  123456789.dkr.ecr.us-east-1.amazonaws.com

# Build and push image
docker build -t my-app .
docker tag my-app:latest 123456789.dkr.ecr.us-east-1.amazonaws.com/my-app:latest
docker push 123456789.dkr.ecr.us-east-1.amazonaws.com/my-app:latest

# Lifecycle policies: auto-delete old images
```

**ECS (Elastic Container Service):**
```
Launch Types:
  EC2:     You manage EC2 instances (more control, cheaper at scale)
  Fargate: Serverless containers (AWS manages infra, easier, per-task billing)

Key Concepts:
  Task Definition: Blueprint for a container group (like docker-compose)
  Task:          Running instances of task definition
  Service:       Maintain desired count of tasks (like deployment)
  Cluster:       Group of tasks/services

Task Definition:
  - Container image
  - CPU/Memory
  - Port mappings
  - Environment variables
  - IAM Task Role (what the container can access)
  - Logging configuration
```

**ECS Task Roles:**
```
Task Execution Role: What ECS can do (pull ECR image, send logs)
Task Role:           What the application code can do (access S3, DynamoDB)
```

---

## Domain 4: Troubleshooting & Optimization

### 4.1 CloudWatch

**CloudWatch Metrics:**
```
- Default metrics every 5 minutes (free)
- Detailed monitoring every 1 minute (extra cost)
- Custom metrics: send your own via API
- Retention: 15 months, resolution decreases over time

Namespace: Category of metrics (e.g., AWS/Lambda, AWS/EC2)
Dimension: Key-value pair to identify metric (e.g., FunctionName=myFunc)
Metric:    Actual data point

Standard Resolution: 1-minute granularity
High Resolution:     1-second granularity
```

**CloudWatch Logs:**
```
Log Group:   Container for log streams (e.g., /aws/lambda/my-function)
Log Stream:  Sequence of events from single source
Log Events:  Individual log entries

Metric Filters:
  - Extract metrics from log data
  - Create alarms from log patterns
  - Example: Count ERROR occurrences

Insights:
  - Interactive log querying (similar to SQL)
  - Search across log groups

// CloudWatch Logs Query (Insights syntax)
fields @timestamp, @message
| filter @message like /ERROR/
| sort @timestamp desc
| limit 20
```

**Custom Metrics:**
```javascript
const { CloudWatchClient, PutMetricDataCommand } = require("@aws-sdk/client-cloudwatch");

const cw = new CloudWatchClient({ region: "us-east-1" });

await cw.send(new PutMetricDataCommand({
  Namespace: "MyApp/Performance",
  MetricData: [
    {
      MetricName: "RequestLatency",
      Dimensions: [
        { Name: "Environment", Value: "production" },
        { Name: "Endpoint", Value: "/api/users" }
      ],
      Value: 150,  // milliseconds
      Unit: "Milliseconds",
      Timestamp: new Date()
    },
    {
      MetricName: "ActiveUsers",
      Value: 1500,
      Unit: "Count",
      Timestamp: new Date()
    }
  ]
}));
```

**CloudWatch Alarms:**
```
States: OK, ALARM, INSUFFICIENT_DATA

Actions:
  - SNS notification (email, Slack, etc.)
  - Lambda function
  - Auto Scaling action
  - EC2 action (reboot, stop, recover)

Composite Alarms:
  - Combine multiple alarms with AND/OR logic
  - Reduce alarm noise
```

**CloudWatch Events / EventBridge:**
```
EventBridge Rules:
  - Schedule (cron/rate expressions)
  - Event pattern (match specific events)

Targets:
  - Lambda
  - SQS
  - SNS
  - Step Functions
  - ECS Task
  - Kinesis Stream
  - Many more

// Rate expression
rate(5 minutes)
rate(1 hour)
rate(1 day)

// Cron expression (UTC)
cron(0 12 * * ? *)        # Every day at 12:00 UTC
cron(0/5 * * * ? *)        # Every 5 minutes
cron(0 18 ? * MON-FRI *)  # Weekdays at 6 PM UTC
```

---

### 4.2 X-Ray (Distributed Tracing)

**X-Ray Concepts:**
```
Trace:     End-to-end request journey across services
Segment:   Work done by a single service
Subsegment: Granular breakdown of segment work
Annotations: Key-value pairs (indexed, searchable, filterable)
Metadata:   Key-value pairs (not indexed, just informational)
Sampling:   % of requests to trace (reduce cost/noise)
```

**X-Ray Instrumentation (Node.js):**
```javascript
const AWSXRay = require('aws-xray-sdk');
const AWS = AWSXRay.captureAWS(require('aws-sdk'));
// All AWS SDK calls now automatically traced

// HTTP capture
const https = AWSXRay.captureHTTPs(require('https'));

// Manual segments
exports.handler = async (event) => {
  const segment = AWSXRay.getSegment();

  // Create subsegment
  const subsegment = segment.addNewSubsegment('database-query');
  try {
    await queryDatabase();
    // Add metadata (not searchable)
    subsegment.addMetadata('query-result', { count: 100 });
    // Add annotation (searchable)
    subsegment.addAnnotation('endpoint', '/users');
  } catch (error) {
    subsegment.addError(error);
    throw error;
  } finally {
    subsegment.close();
  }
};
```

**X-Ray with Lambda:**
```yaml
# SAM template - enable X-Ray
MyFunction:
  Type: AWS::Serverless::Function
  Properties:
    Tracing: Active     # PassThrough = disable X-Ray
```

**X-Ray Sampling Rules:**
```
Default: 1 request/sec + 5% additional
Custom rules: Define rate, reservoir, priority, conditions

Purpose: Control cost and volume of trace data
```

**X-Ray Service Map:**
```
Visual representation of all services and connections
Shows latency, errors, throughput between services
Identify bottlenecks and error sources
```

---

### 4.3 ElastiCache

**Redis vs Memcached:**
```
Feature           | Redis                    | Memcached
------------------|--------------------------|------------------
Data Structures   | Strings, Hashes, Lists,  | Strings only
                  | Sets, Sorted Sets, etc.  |
Persistence       | Yes (AOF, RDB)           | No
Replication       | Yes (Multi-AZ)           | No
Pub/Sub           | Yes                      | No
Cluster mode      | Yes (sharding)           | Yes (multi-thread)
Transactions      | Yes                      | No
Lua Scripting     | Yes                      | No
Use case          | Complex, persistent cache| Simple cache, multi-thread
```

**Caching Strategies:**
```
Lazy Loading (Cache-Aside):
  1. App checks cache
  2. Cache miss → fetch from DB
  3. Store in cache
  4. Return data
  + Only caches needed data
  - Cache miss = 3 trips
  - Stale data possible

Write-Through:
  1. On write, update cache AND DB simultaneously
  + Cache always fresh
  - Writes slower
  - Cached data may never be read (waste)

Cache Invalidation:
  - TTL: Auto-expire after time
  - Event-based: Invalidate on update

Session Store: Store user sessions in Redis (scalable, shared across EC2)
```

**ElastiCache with Lambda:**
```javascript
// VPC configuration required for ElastiCache
const redis = require('ioredis');

// Create outside handler for connection reuse (warm starts)
const client = new redis({
  host: process.env.REDIS_HOST,
  port: 6379,
  lazyConnect: true
});

exports.handler = async (event) => {
  await client.connect();
  
  const key = `user:${event.userId}`;
  
  // Try cache first
  const cached = await client.get(key);
  if (cached) {
    return JSON.parse(cached);
  }
  
  // Cache miss - fetch from DB
  const user = await db.getUser(event.userId);
  
  // Cache with 1 hour TTL
  await client.set(key, JSON.stringify(user), 'EX', 3600);
  
  return user;
};
```

---

## Core AWS Services Deep Dive

### Step Functions
```
- Orchestrate serverless workflows as state machines
- Visual workflow editor in console

State Types:
  Task:      Execute Lambda, ECS task, SNS, SQS, DynamoDB, etc.
  Choice:    Branch based on condition
  Wait:      Add delay (seconds, timestamp)
  Parallel:  Execute branches simultaneously
  Map:       Iterate over array items
  Pass:      Pass input to output
  Succeed:   Stop execution - success
  Fail:      Stop execution - failure

Workflow Types:
  Standard: Long-running (up to 1 year), exactly-once, auditable
  Express:  High-volume (up to 5 minutes), at-least-once processing
```

**State Machine Example:**
```json
{
  "Comment": "Order processing workflow",
  "StartAt": "ValidateOrder",
  "States": {
    "ValidateOrder": {
      "Type": "Task",
      "Resource": "arn:aws:lambda:us-east-1:123:function:ValidateOrder",
      "Next": "ProcessPayment",
      "Catch": [
        {
          "ErrorEquals": ["ValidationError"],
          "Next": "OrderFailed"
        }
      ]
    },
    "ProcessPayment": {
      "Type": "Task",
      "Resource": "arn:aws:lambda:us-east-1:123:function:ProcessPayment",
      "Next": "ShipOrder"
    },
    "ShipOrder": {
      "Type": "Task",
      "Resource": "arn:aws:lambda:us-east-1:123:function:ShipOrder",
      "End": true
    },
    "OrderFailed": {
      "Type": "Fail",
      "Error": "OrderFailed",
      "Cause": "Order validation failed"
    }
  }
}
```

### AppSync (GraphQL)
```
- Managed GraphQL service
- Real-time subscriptions via WebSockets
- Offline sync for mobile apps
- Data sources: DynamoDB, Lambda, RDS, HTTP, Elasticsearch

Resolvers:
  - Unit resolver: Single data source
  - Pipeline resolver: Chain multiple functions
  - Direct Lambda resolver: Maximum flexibility
```

### Cognito Advanced
```
User Pool Triggers (Lambda):
  Pre-Token Generation: Modify JWT token claims
  Pre-Authentication:   Custom auth checks
  Post-Authentication:  Post-login actions
  Custom Authentication: Implement custom auth flows
  
App Clients:
  - Each app gets its own client ID/secret
  - Configure allowed flows, scopes, callback URLs
  - SRP (Secure Remote Password) authentication
```

### API Gateway Advanced
```
Usage Plans + API Keys:
  - Throttle and quota per API key
  - Monetize your API
  - Associate keys with plans

Resource Policy:
  - Control access by AWS account, IP, VPC endpoint
  - Like S3 bucket policy but for API Gateway

Private API:
  - Only accessible from within VPC
  - Requires VPC endpoint (interface endpoint)
  - Resource policy must explicitly allow VPC
```

---

## Practice Questions & Answers

### Section 1: Lambda & Serverless

**Q1. A Lambda function processes messages from an SQS queue. The function fails to process some messages. After the `maxReceiveCount`, where do failed messages go?**

**Answer**: B - Dead Letter Queue (DLQ)

Failed messages after `maxReceiveCount` retries are moved to the configured **Dead Letter Queue (DLQ)**, which can be an SQS queue or SNS topic. This prevents message loss and allows for debugging failed messages.

---

**Q2. Your Lambda function needs credentials to access a DynamoDB table. What is the BEST practice?**

A) Store credentials in environment variables  
B) Hardcode credentials in function code  
C) **Attach an IAM role to the Lambda function**  
D) Pass credentials in the event payload  

**Answer**: C

Lambda should always use an **IAM execution role** to access AWS services. This follows the principle of least privilege and avoids hardcoding credentials. Roles provide temporary credentials automatically via STS.

---

**Q3. A Lambda function has a cold start issue. Which option eliminates cold starts entirely?**

A) Increase Lambda memory  
B) Use Lambda Layers  
C) **Provisioned Concurrency**  
D) Reduce function timeout  

**Answer**: C

**Provisioned Concurrency** pre-warms a specified number of Lambda instances, ensuring they are initialized and ready to respond immediately. This eliminates cold starts for the provisioned instances.

---

**Q4. Your Lambda function handles SQS events. Processing of 8 out of 10 messages succeeds, but 2 fail. What happens by default?**

A) Only the 2 failed messages are retried  
B) **All 10 messages are retried**  
C) The 2 failed messages are sent to DLQ  
D) Processing continues, failures are ignored  

**Answer**: B

By default, if any record in a batch fails, the **entire batch** becomes visible again in SQS for retry. To retry only failed records, implement `batchItemFailures` in the Lambda response (partial batch success feature).

---

**Q5. A company needs a Lambda function to access an RDS database in a private subnet. What is required?**

A) Add Lambda to a public subnet  
B) **Configure Lambda to run in the same VPC and subnet as RDS**  
C) Use VPC peering  
D) Enable public accessibility on RDS  

**Answer**: B

Lambda must be configured with **VPC settings** matching the RDS subnet and security group configuration. Lambda will get a VPC ENI and can then communicate with RDS within the private network.

---

**Q6. Your Lambda function processes one SQS message per invocation. What configuration should you set to improve throughput?**

A) Increase Lambda timeout  
B) **Increase batch size on the SQS event source mapping**  
C) Enable SQS long polling  
D) Switch to FIFO queue  

**Answer**: B

Increasing the **batch size** allows Lambda to process multiple messages (up to 10,000 for standard queues) in a single invocation, significantly improving throughput and reducing cost.

---

### Section 2: DynamoDB

**Q7. An application needs to read the latest version of an item without any delay. Which DynamoDB read type should be used?**

A) Eventually consistent read  
B) **Strongly consistent read**  
C) Transactional read  
D) DAX read  

**Answer**: B

**Strongly consistent reads** return the most up-to-date version of the data, reflecting all writes prior to the read. Cost: 1 RCU per read (vs 0.5 for eventually consistent).

---

**Q8. You want to prevent a DynamoDB PutItem from overwriting an existing item. What should you use?**

A) A Global Secondary Index  
B) DynamoDB Streams  
C) **ConditionExpression with `attribute_not_exists`**  
D) Strongly consistent write  

**Answer**: C

Use `ConditionExpression: "attribute_not_exists(pk)"` in the PutItem call. This causes the operation to fail if the item already exists, implementing optimistic concurrency control.

---

**Q9. Your DynamoDB table needs to store and query data by both user ID and timestamp. What is the optimal key design?**

A) Partition key only (userId)  
B) **Partition key (userId) + Sort key (timestamp)**  
C) GSI with timestamp as partition key  
D) LSI on userId  

**Answer**: B

A **composite primary key** with `userId` as partition key and `timestamp` as sort key enables efficient queries like "get all items for userId X between time A and time B" without a full table scan.

---

**Q10. An application needs to query DynamoDB by an attribute that is NOT the primary key. What is the MOST efficient approach?**

A) Use Scan with FilterExpression  
B) **Create a Global Secondary Index (GSI)**  
C) Create a Local Secondary Index (LSI)  
D) Use DynamoDB Streams  

**Answer**: B

A **GSI** allows efficient querying on non-primary-key attributes. Scan + FilterExpression reads all items first then filters, consuming full table RCU—expensive and slow. LSI requires creation at table creation time and uses same partition key.

---

**Q11. A gaming app needs real-time leaderboard capability. Which DynamoDB structure is BEST?**

A) GSI with scores  
B) Multiple query operations  
C) **DynamoDB with sorted set (score as sort key)**  
D) DynamoDB Streams + Lambda  

**Answer**: C — or consider Redis Sorted Sets via ElastiCache for real-time leaderboards.

Use `score` as the sort key with partition key as `leaderboard_id`. Query in descending order with `ScanIndexForward: false` for top scores.

---

### Section 3: S3

**Q12. A Lambda function needs to generate a downloadable link for a private S3 object that expires after 1 hour. What should you use?**

A) Make the object public  
B) Enable S3 static website hosting  
C) **Generate a pre-signed URL**  
D) Use CloudFront with signed cookies  

**Answer**: C

**Pre-signed URLs** grant temporary access to private S3 objects. The URL inherits the permissions of the IAM entity that signed it. Default max expiry for S3 pre-signed URLs via SDK is 7 days.

---

**Q13. You need to ensure all objects uploaded to an S3 bucket are encrypted. What is the RECOMMENDED approach?**

A) Rely on users to encrypt before upload  
B) Use client-side encryption only  
C) **Set a bucket policy to deny requests without `x-amz-server-side-encryption` header**  
D) Enable versioning  

**Answer**: C

A **bucket policy** that denies PUT requests without the SSE header (`x-amz-server-side-encryption`) enforces encryption at rest. Alternatively, use default bucket encryption + block unencrypted requests.

---

**Q14. Which S3 storage class offers the LOWEST cost for data that is accessed rarely and can tolerate 12-hour retrieval times?**

A) S3 Standard-IA  
B) S3 Glacier Instant Retrieval  
C) S3 Glacier Flexible Retrieval  
D) **S3 Glacier Deep Archive**  

**Answer**: D

**S3 Glacier Deep Archive** is the cheapest S3 storage class, designed for data accessed less than once a year. Retrieval times are 12–48 hours. Perfect for compliance archiving.

---

### Section 4: API Gateway & Security

**Q15. An API needs to authenticate users using JWT tokens from Cognito. What is the SIMPLEST API Gateway authorization method?**

A) Lambda Authorizer  
B) IAM Authorization  
C) **Cognito User Pool Authorizer**  
D) API Key  

**Answer**: C

**Cognito User Pool Authorizer** natively validates JWT tokens from Cognito without any custom code. Lambda Authorizer requires writing and maintaining custom Lambda code.

---

**Q16. A client receives a `502 Bad Gateway` error from API Gateway. What is the MOST likely cause?**

A) Request timeout exceeded  
B) Invalid API key  
C) **Lambda function returned an invalid response format**  
D) API Gateway is down  

**Answer**: C

`502 Bad Gateway` means API Gateway received an invalid response from the backend (Lambda). Common causes: Lambda returning malformed JSON, syntax error in function code, or incompatible response format for proxy integration.

---

**Q17. Your API Gateway endpoint should only be accessible from within your VPC. What should you configure?**

A) Add VPC CIDR to WAF rules  
B) **Create a Private API with VPC endpoint**  
C) Use Lambda VPC configuration  
D) Enable API Gateway caching  

**Answer**: B

**Private API** + **VPC Interface Endpoint (aws.execute-api)** restricts API Gateway access to within the VPC. A resource policy must explicitly allow access from the VPC endpoint ID.

---

**Q18. A developer needs to encrypt data larger than 4 KB using KMS. What technique should be used?**

A) Use a larger KMS key  
B) Split data into 4 KB chunks  
C) Use SSE-C  
D) **Envelope Encryption**  

**Answer**: D

**Envelope Encryption**: Use `GenerateDataKey` to receive a plaintext DEK and encrypted DEK. Use the plaintext DEK to encrypt large data locally. Store encrypted DEK alongside encrypted data. KMS only handles the DEK encryption (< 4 KB).

---

### Section 5: Deployment & CI/CD

**Q19. Your Elastic Beanstalk deployment should have ZERO downtime and the ability to quickly roll back. Which deployment policy is BEST?**

A) All at Once  
B) Rolling  
C) Rolling with additional batch  
D) **Immutable**  

**Answer**: D

**Immutable** deployment creates a new Auto Scaling Group with all new instances. Zero downtime is achieved, and rollback is as simple as terminating new ASG without affecting the original deployment.

---

**Q20. A CodeDeploy deployment to Lambda should route 10% of traffic to the new version for 5 minutes, then the remaining 90%. Which deployment configuration should be used?**

A) Linear10PercentEvery1Minute  
B) AllAtOnce  
C) **Canary10Percent5Minutes**  
D) Linear10PercentEvery10Minutes  

**Answer**: C

**Canary10Percent5Minutes** shifts 10% of traffic to the new Lambda version, waits 5 minutes for monitoring, then shifts 100% of traffic. This is exactly the described behavior.

---

**Q21. Where are CodePipeline artifacts stored between pipeline stages?**

A) CodeCommit  
B) EBS volume  
C) **S3 bucket**  
D) ECR  

**Answer**: C

CodePipeline stores build artifacts and source in an **S3 bucket** (artifact store) between stages. Each stage reads from and writes to this bucket.

---

**Q22. A SAM template deploys a Lambda function with an API Gateway trigger. What CloudFormation transform is required?**

A) AWS::Lambda::Transform  
B) AWS::CloudFormation::Macro  
C) **AWS::Serverless-2016-10-31**  
D) AWS::SAM::Transform  

**Answer**: C

SAM templates require `Transform: AWS::Serverless-2016-10-31` at the template root. This CloudFormation macro transforms SAM resource types into standard CloudFormation resources during deployment.

---

### Section 6: Monitoring & Troubleshooting

**Q23. A Lambda function is intermittently timing out. What is the BEST tool to identify the bottleneck?**

A) CloudWatch Alarms  
B) CloudWatch Logs  
C) **AWS X-Ray**  
D) VPC Flow Logs  

**Answer**: C

**AWS X-Ray** provides distributed tracing showing exactly where time is spent in your function—API calls, database queries, external HTTP calls. This helps pinpoint exactly which subsegment is causing the timeout.

---

**Q24. An application uses ElastiCache to cache DynamoDB reads. After a DynamoDB update, the cache still returns stale data. What caching strategy would prevent this?**

A) Lazy loading  
B) **Write-through caching**  
C) Increase cache TTL  
D) Enable DynamoDB Streams  

**Answer**: B

**Write-through** updates the cache every time a write occurs in DynamoDB, keeping the cache always consistent with the database. Lazy loading only refreshes on cache miss, allowing stale data.

---

**Q25. A CloudWatch alarm should trigger when average CPU exceeds 80% for 3 consecutive 5-minute periods. What should the alarm evaluation period and datapoints-to-alarm be?**

A) Period: 5 min, Datapoints: 1/1  
B) Period: 15 min, Datapoints: 1/1  
C) **Period: 5 min, Evaluation Periods: 3, Datapoints to Alarm: 3**  
D) Period: 1 min, Evaluation Periods: 15  

**Answer**: C

Set evaluation period to 5 minutes, evaluation periods to 3, and datapoints to alarm to 3 (M of N evaluation). This means 3 out of 3 consecutive 5-minute periods must breach the threshold.

---

**Q26. Your Lambda function logs show `Task timed out after 3.00 seconds`. What are the TWO BEST actions?**

A) Increase function memory only  
B) **Increase function timeout AND optimize the function code**  
C) Switch to EC2  
D) Enable provisioned concurrency  

**Answer**: B

Increasing the **timeout setting** provides more execution time and **optimizing code** (removing unnecessary operations, using connection pooling, reducing external calls) addresses the root cause.

---

**Q27. An SQS message has been received 4 times but never deleted. The maxReceiveCount is 3. Where is the message now?**

A) Still in the main queue  
B) Permanently deleted  
C) **In the Dead Letter Queue**  
D) In a retry queue  

**Answer**: C

After the `maxReceiveCount` (3 in this case) is reached, SQS moves the message to the configured **Dead Letter Queue (DLQ)**. This prevents a poison-pill message from blocking the queue indefinitely.

---

**Q28. What X-Ray component should you use to add business-level searchable attributes to traces?**

A) Metadata  
B) Subsegments  
C) Error  
D) **Annotations**  

**Answer**: D

**Annotations** are indexed key-value pairs attached to X-Ray segments/subsegments. They are searchable and filterable in the X-Ray service map and trace list. Metadata is informational but not indexed/searchable.

---

**Q29. A developer wants to store database passwords in AWS with automatic 30-day rotation. What service should be used?**

A) SSM Parameter Store  
B) **AWS Secrets Manager**  
C) KMS  
D) IAM  

**Answer**: B

**AWS Secrets Manager** supports automatic secret rotation via Lambda functions. It has built-in rotation templates for RDS, Redshift, and DocumentDB. SSM Parameter Store does not support automatic rotation natively.

---

**Q30. Your application needs to temporarily escalate permissions to perform a sensitive operation. What AWS service provides temporary credentials?**

A) IAM  
B) KMS  
C) Cognito  
D) **STS (Security Token Service)**  

**Answer**: D

**STS AssumeRole** provides temporary security credentials (Access Key, Secret Key, Session Token) with an expiration time. This is the standard way to grant temporary elevated permissions.

---

### Section 7: Advanced (Harder Questions)

**Q31. A stateless REST API on Lambda needs to maintain user sessions across requests. What is the BEST approach?**

A) Store sessions in Lambda /tmp  
B) Use Lambda environment variables  
C) **Store session data in ElastiCache (Redis)**  
D) Store in local memory  

**Answer**: C

Lambda is stateless and may have multiple concurrent instances. **ElastiCache Redis** provides a shared, fast session store accessible across all Lambda instances. /tmp and memory are instance-local; environment variables are static config, not per-user state.

---

**Q32. A DynamoDB table is experiencing hot partitions. What are TWO ways to resolve this?**

A) Increase RCU  
B) **Use a write sharding strategy (add random suffix to PK)**  
C) Enable auto-scaling  
D) **Use DAX for caching reads**  

**Answer**: B and D

**Write sharding** distributes writes across multiple logical partitions by appending a random number to the partition key. **DAX** reduces read load on hot partitions by serving cached reads. Simply adding RCU doesn't fix hot partitions because the hot partition itself is still the bottleneck.

---

**Q33. Which of the following are valid SQS FIFO message deduplication methods? (Choose 2)**

A) Message timestamp  
B) **Content-based deduplication (SHA-256 hash of body)**  
C) Message group ID  
D) **Message deduplication ID (custom ID provided by sender)**  

**Answer**: B and D

FIFO queues support two deduplication methods: **content-based deduplication** (SHA-256 hash of message body, enabled at queue level) and **message deduplication ID** (custom unique ID provided by the sender in each message).

---

**Q34. An application stores sensitive data in SSM Parameter Store. The data must be encrypted. Which parameter type should be used?**

A) String  
B) StringList  
C) **SecureString**  
D) EncryptedString  

**Answer**: C

**SecureString** parameters are encrypted using KMS. AWS-managed key (free) or Customer Managed Key (CMK) can be used. String and StringList are stored in plaintext.

---

**Q35. A Cognito-authenticated user should be able to directly access their own S3 folder `private/{userId}/*`. What is the correct IAM policy condition?**

A) `"Condition": {"StringEquals": {"s3:prefix": ["private/"]}}`  
B) `"Resource": "arn:aws:s3:::bucket/private/*"`  
C) **`"Resource": "arn:aws:s3:::bucket/private/${cognito-identity.amazonaws.com:sub}/*"`**  
D) Use a Lambda authorizer  

**Answer**: C

Using **IAM policy variables** with Cognito, `${cognito-identity.amazonaws.com:sub}` is substituted at runtime with the user's Cognito Identity ID, restricting access to only their own folder.

---

**Q36. A Lambda function behind API Gateway returns `{"statusCode": 200, "body": "..."}` but API Gateway returns a 502 error. What is MOST likely wrong?**

A) Lambda timeout  
B) IAM permissions  
C) **`body` is not a string (not properly serialized with JSON.stringify)**  
D) CORS not configured  

**Answer**: C

With Lambda Proxy Integration, API Gateway requires `body` to be a **string**, not an object. If `body` is a JavaScript object instead of `JSON.stringify(object)`, API Gateway returns 502. Always serialize body: `JSON.stringify({ message: "ok" })`.

---

**Q37. How does CodeDeploy's `ValidateService` hook differ from `ApplicationStart`?**

A) They are identical  
B) **`ApplicationStart` starts the app; `ValidateService` verifies it started correctly**  
C) `ValidateService` runs before deployment  
D) `ApplicationStart` runs after ValidateService  

**Answer**: B

`ApplicationStart` is where you start the application service. `ValidateService` runs after `ApplicationStart` and is used to verify the application is healthy (e.g., run smoke tests, health check endpoint). Failure at `ValidateService` triggers automatic rollback.

---

**Q38. A developer uses DynamoDB Transactions. A `TransactWriteItems` call updates 3 tables. Table 2 fails with a validation error. What happens to the changes in Tables 1 and 3?**

A) They are committed  
B) They are partially rolled back  
C) **They are completely rolled back (all or nothing)**  
D) They are moved to DLQ  

**Answer**: C

DynamoDB Transactions are **ACID-compliant** (all-or-nothing). If any condition fails or any item change fails, the entire transaction is rolled back. Tables 1 and 3 changes are NOT applied.

---

**Q39. Which Elastic Beanstalk deployment policy launches new instances BEFORE terminating old ones, ensuring full capacity is maintained?**

A) All at Once  
B) Rolling  
C) **Rolling with additional batch**  
D) Immutable  

**Answer**: C

**Rolling with additional batch** launches a new batch of instances first (maintaining full capacity), deploys to them, then terminates old instances in batches. This ensures capacity is never reduced during deployment.

---

**Q40. An API Gateway REST API has caching enabled. A client sends a request with `Cache-Control: max-age=0`. What should the backend developer ensure for this to invalidate the cache?**

A) Nothing — it works automatically  
B) Enable detailed logging  
C) Disable caching on the stage  
D) **Grant the IAM permission `execute-api:InvalidateCache` to the client**  

**Answer**: D

To allow clients to invalidate API Gateway cache with the `Cache-Control: max-age=0` header, you must grant them the **`execute-api:InvalidateCache`** IAM permission. Without it, the cache invalidation header is ignored.

---

## Cheat Sheet

### Lambda Quick Reference
```
Max timeout:          15 minutes
Memory range:         128 MB – 10,240 MB
/tmp storage:         512 MB – 10 GB
Deployment size (zip): 50 MB
Unzipped size:        250 MB
Concurrency default:  1,000 (soft limit)
Cold start fix:       Provisioned Concurrency
Async retries:        2 (S3, SNS, EventBridge)
SQS batch size:       1 – 10,000
```

### DynamoDB Quick Reference
```
Max item size:        400 KB
RCU (strong):         1 read/s per 4KB
RCU (eventual):       2 reads/s per 4KB
WCU:                  1 write/s per 1KB
TTL:                  Free, within 48h
DynamoDB Streams:     24h retention
DAX item TTL:         5 minutes default
GSI limit:            20 per table
LSI limit:            5 per table
```

### S3 Quick Reference
```
Max object size:         5 TB
Max single PUT:          5 GB
Multipart recommended:   >100 MB
Multipart required:      >5 GB
Pre-signed URL max:      7 days (604800s)
Versioning:              Once enabled, can't disable (only suspend)
Cross-region replication: Must enable versioning first
Event notifications:     Lambda, SQS, SNS, EventBridge
```

### SQS Quick Reference
```
Max message size:        256 KB
Retention:               1 min – 14 days (default 4 days)
Visibility timeout:      0s – 12h (default 30s)
Long poll max wait:      20 seconds
FIFO throughput:         300 TPS (3,000 with batching)
DLQ maxReceiveCount:     1 – 1,000
Delay queue:             0 – 900 seconds
```

### API Gateway Quick Reference
```
Max timeout:             29 seconds
Cache TTL:               0 – 3600s (default 300s)
Max cache size:          237 GB
Payload limit:           10 MB (REST), 6 MB (HTTP)
WebSocket idle timeout:  10 minutes
500 = Internal Error
502 = Bad Gateway (Lambda bad response)
503 = Service Unavailable
504 = Endpoint request timed out
429 = Throttled
```

### IAM Quick Reference
```
Policy evaluation:       Explicit Deny > Explicit Allow > Implicit Deny
STS AssumeRole:          Cross-account, federated, service roles
Inline policy:           Embedded in identity (not reusable)
AWS managed policy:      Maintained by AWS
Customer managed:        You create/manage
Permission boundary:     Max permissions for IAM entity
SCP:                     Org-level max permissions
```

### KMS Quick Reference
```
Direct encrypt max:      4 KB
Envelope encryption:     For data > 4 KB
AWS Managed Keys:        Free, auto-rotate yearly
CMK:                     $1/month
Key deletion:            7 – 30 day waiting period
Multi-region keys:       Prefix mrk-
```

### CloudWatch Quick Reference
```
Default metric freq:     5 minutes
Detailed monitoring:     1 minute
Custom metric resolution: 1 second (high-res) or 60 seconds
Alarm states:            OK, ALARM, INSUFFICIENT_DATA
Log retention:           1 day – never (configure per group)
Insights query syntax:   fields, filter, sort, stats, limit
```

### Common Port Numbers
```
SSH:     22
HTTP:    80
HTTPS:   443
MySQL:   3306
PostgreSQL: 5432
Redis:   6379
Memcached: 11211
MongoDB: 27017
```

### HTTP Status Codes to Know
```
200 OK
201 Created
204 No Content
301 Moved Permanently
302 Found (redirect)
400 Bad Request
401 Unauthorized (not authenticated)
403 Forbidden (authenticated but not authorized)
404 Not Found
409 Conflict
429 Too Many Requests
500 Internal Server Error
502 Bad Gateway
503 Service Unavailable
504 Gateway Timeout
```

---

## Exam Day Tips

### Before the Exam
1. **Get 8 hours of sleep** — Fatigue kills performance
2. **Don't cram the morning of** — Light review only
3. **Arrive 30 minutes early** (in-person) or log in 15 min early (online)
4. **Have valid photo ID** ready
5. **Water and snack** recommended for 130 minutes

### During the Exam
1. **Read EVERY word** — Keywords like "LEAST cost", "MOST secure", "BEST" matter
2. **Eliminate obviously wrong answers** first — often 2 wrong, 2 plausible
3. **Flag uncertain questions** — Return to them later
4. **Watch for "EXCEPT" / "NOT"** questions — Easy to miss
5. **Budget time** — ~2 minutes per question (130 min ÷ 65 questions)
6. **Don't change answers** unless you have strong evidence

### Key Phrases to Know
```
"Least privilege"          → IAM minimal permissions
"Decouple"                 → SQS/SNS between services
"Serverless"               → Lambda + API Gateway + DynamoDB
"Real-time"                → Kinesis or DynamoDB Streams
"Ordered"                  → SQS FIFO or Kinesis
"Exactly once"             → SQS FIFO
"Cache"                    → ElastiCache (Redis/Memcached) or DAX
"Temporary credentials"    → STS AssumeRole
"Zero downtime deploy"     → Immutable or Blue/Green
"Cost-effective"           → Often serverless or On-Demand
"Highly available"         → Multi-AZ, auto-scaling
"Encrypt at rest"          → KMS + SSE-* variants
"Audit"                    → CloudTrail
"Monitor/Metrics"          → CloudWatch
"Trace/Debug"              → X-Ray
"Rotate secrets"           → Secrets Manager
"Config/Parameters"        → SSM Parameter Store
```

### Common Tricks in Exam
- **S3**: They test knowing when to use pre-signed URLs vs CloudFront signed URLs
- **Lambda**: Cold starts = Provisioned Concurrency; SQS batch failure = partial batch response
- **DynamoDB**: Scan=inefficient; Query=efficient; always prefer GSI over Scan
- **SQS Standard vs FIFO**: At-least-once vs exactly-once; ordering vs high throughput
- **Elastic Beanstalk**: Immutable = safest + zero downtime; All at once = fastest + downtime
- **CodeDeploy**: Canary = %, Linear = increment, AllAtOnce = risky

---

## Resources & Next Steps

### After Getting Certified
```
1. AWS Certified Solutions Architect – Associate
2. AWS Certified DevOps Engineer – Professional
3. AWS Certified Developer – Professional (when available)
4. Specialize: Machine Learning, Security, Database Specialty
```

### Learning Resources
| Resource | Best For | Link |
|----------|----------|------|
| AWS Skill Builder | Official prep | skillbuilder.aws |
| Stephane Maarek (Udemy) | Video learning | udemy.com |
| Tutorials Dojo | Practice exams | tutorialsdojo.com |
| AWS Documentation | Deep dives | docs.aws.amazon.com |
| AWS Workshops | Hands-on | workshops.aws |
| AWS re:Post | Community Q&A | repost.aws |
| AWS Well-Architected | Best practices | aws.amazon.com/architecture |

### Free Practice Resources
- AWS Skill Builder (free tier has practice questions)
- AWS documentation FAQs
- AWS re:Invent videos on YouTube
- AWS Workshops (free labs)
- CloudFormation + CDK samples on GitHub

---

> **Remember**: Hands-on experience is irreplaceable. Build real projects using AWS Free Tier while studying. Every service you actually use sticks far better than any flashcard.

> **Good Luck! You've got this.** 🚀
