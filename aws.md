# AWS - Senior Developer to Architect Level Q&A

## Table of Contents
1. [Core Services & Architecture](#core-services--architecture)
2. [Compute Services](#compute-services)
3. [Storage & Databases](#storage--databases)
4. [Networking & Content Delivery](#networking--content-delivery)
5. [Security & Identity](#security--identity)
6. [DevOps & Deployment](#devops--deployment)
7. [Cost Optimization](#cost-optimization)

---

## Core Services & Architecture

### Q1: Explain the AWS Well-Architected Framework and its five pillars.

**Answer:**

**Simple Analogy:**
Think of building a house - you need a solid foundation, security, efficiency, reliability, and it shouldn't cost a fortune.

**Five Pillars:**

**1. Operational Excellence:**
Run and monitor systems to deliver business value.

**Key Principles:**
- Infrastructure as Code (CloudFormation, Terraform)
- Automated deployment pipelines
- Monitoring and logging
- Regular improvements

**Example:**
```yaml
# CloudFormation template
Resources:
  MyEC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      ImageId: ami-0c55b159cbfafe1f0
      InstanceType: t3.micro
      UserData:
        Fn::Base64: |
          #!/bin/bash
          yum update -y
          yum install -y httpd
          systemctl start httpd

  MyLogGroup:
    Type: AWS::Logs::LogGroup
    Properties:
      LogGroupName: /aws/ec2/myapp
      RetentionInDays: 7
```

---

**2. Security:**
Protect data, systems, and assets.

**Key Principles:**
- Identity and Access Management (IAM)
- Encryption at rest and in transit
- Network isolation (VPC, Security Groups)
- Audit logging (CloudTrail)

**Example:**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:PutObject"
      ],
      "Resource": "arn:aws:s3:::my-bucket/*",
      "Condition": {
        "IpAddress": {
          "aws:SourceIp": "203.0.113.0/24"
        }
      }
    }
  ]
}
```

---

**3. Reliability:**
Ensure workload performs as expected.

**Key Principles:**
- Multi-AZ deployment
- Auto Scaling
- Backup and disaster recovery
- Fault tolerance

**Example:**
```yaml
# Multi-AZ RDS
Resources:
  MyDB:
    Type: AWS::RDS::DBInstance
    Properties:
      Engine: postgres
      MultiAZ: true  # High availability
      BackupRetentionPeriod: 7
      PreferredBackupWindow: "03:00-04:00"
```

---

**4. Performance Efficiency:**
Use computing resources efficiently.

**Key Principles:**
- Right-sizing instances
- Caching (CloudFront, ElastiCache)
- Serverless when appropriate
- Global deployment

**Example:**
```python
# Use DynamoDB DAX for caching
import amazondax

dax_endpoint = 'mycluster.dax-clusters.us-east-1.amazonaws.com'
dax = amazondax.AmazonDaxClient(endpoint_url=dax_endpoint)

# Cached read (microseconds)
response = dax.get_item(
    TableName='Users',
    Key={'userId': {'S': '123'}}
)
```

---

**5. Cost Optimization:**
Avoid unnecessary costs.

**Key Principles:**
- Reserved Instances / Savings Plans
- Right-sizing
- Spot Instances for non-critical workloads
- S3 lifecycle policies

**Example:**
```json
{
  "Rules": [{
    "Id": "Move to cheaper storage",
    "Status": "Enabled",
    "Transitions": [
      {
        "Days": 30,
        "StorageClass": "STANDARD_IA"
      },
      {
        "Days": 90,
        "StorageClass": "GLACIER"
      }
    ],
    "Expiration": {
      "Days": 365
    }
  }]
}
```

---

### Q2: How do you design a highly available and scalable architecture on AWS?

**Answer:**

**Architecture Pattern:**

```
                     Route 53 (DNS)
                          |
                   CloudFront (CDN)
                          |
              Application Load Balancer
                     /          \
                   /              \
         AZ 1 (us-east-1a)    AZ 2 (us-east-1b)
         /      \              /      \
       EC2    EC2            EC2    EC2
        |      |              |      |
        +------+              +------+
               |              |
         RDS Primary ←→ RDS Standby
               |
         ElastiCache Cluster
```

**Implementation:**

**1. Multi-AZ Deployment:**

```yaml
# Auto Scaling Group across multiple AZs
Resources:
  MyASG:
    Type: AWS::AutoScaling::AutoScalingGroup
    Properties:
      VPCZoneIdentifier:
        - subnet-1a  # AZ 1
        - subnet-1b  # AZ 2
        - subnet-1c  # AZ 3
      LaunchTemplate:
        LaunchTemplateId: !Ref LaunchTemplate
        Version: !GetAtt LaunchTemplate.LatestVersionNumber
      MinSize: 2
      MaxSize: 10
      DesiredCapacity: 4
      TargetGroupARNs:
        - !Ref TargetGroup
      HealthCheckType: ELB
      HealthCheckGracePeriod: 300
```

---

**2. Load Balancing:**

```yaml
# Application Load Balancer
Resources:
  ALB:
    Type: AWS::ElasticLoadBalancingV2::LoadBalancer
    Properties:
      Type: application
      Scheme: internet-facing
      Subnets:
        - subnet-public-1a
        - subnet-public-1b
      SecurityGroups:
        - !Ref ALBSecurityGroup

  TargetGroup:
    Type: AWS::ElasticLoadBalancingV2::TargetGroup
    Properties:
      Port: 80
      Protocol: HTTP
      VpcId: !Ref VPC
      HealthCheckPath: /health
      HealthCheckIntervalSeconds: 30
      HealthCheckTimeoutSeconds: 5
      HealthyThresholdCount: 2
      UnhealthyThresholdCount: 3
```

---

**3. Auto Scaling:**

```yaml
# Scaling policies
Resources:
  ScaleUpPolicy:
    Type: AWS::AutoScaling::ScalingPolicy
    Properties:
      AutoScalingGroupName: !Ref MyASG
      PolicyType: TargetTrackingScaling
      TargetTrackingConfiguration:
        PredefinedMetricSpecification:
          PredefinedMetricType: ASGAverageCPUUtilization
        TargetValue: 70.0

  ScaleDownPolicy:
    Type: AWS::AutoScaling::ScalingPolicy
    Properties:
      AutoScalingGroupName: !Ref MyASG
      ScalingAdjustment: -1
      AdjustmentType: ChangeInCapacity
```

---

**4. Database High Availability:**

```python
# RDS Multi-AZ with Read Replicas
import boto3

rds = boto3.client('rds')

# Create Multi-AZ database
rds.create_db_instance(
    DBInstanceIdentifier='mydb',
    Engine='postgres',
    DBInstanceClass='db.t3.medium',
    MultiAZ=True,  # Automatic failover
    BackupRetentionPeriod=7,
    StorageEncrypted=True
)

# Create read replica
rds.create_db_instance_read_replica(
    DBInstanceIdentifier='mydb-replica',
    SourceDBInstanceIdentifier='mydb',
    PubliclyAccessible=False
)
```

---

**5. Caching Layer:**

```python
# ElastiCache Redis cluster
import redis

# Connect to ElastiCache
cache = redis.Redis(
    host='mycluster.cache.amazonaws.com',
    port=6379,
    decode_responses=True
)

# Cache data
def get_user(user_id):
    # Try cache first
    cached = cache.get(f'user:{user_id}')
    if cached:
        return json.loads(cached)
    
    # Cache miss - query database
    user = db.query('SELECT * FROM users WHERE id = %s', user_id)
    
    # Store in cache (1 hour)
    cache.setex(f'user:{user_id}', 3600, json.dumps(user))
    
    return user
```

---

**6. CDN (Content Delivery):**

```javascript
// CloudFront distribution
const cloudfront = new AWS.CloudFront();

const params = {
  DistributionConfig: {
    CallerReference: Date.now().toString(),
    Origins: {
      Quantity: 1,
      Items: [{
        Id: 'S3-my-website',
        DomainName: 'my-website.s3.amazonaws.com',
        S3OriginConfig: {
          OriginAccessIdentity: ''
        }
      }]
    },
    DefaultCacheBehavior: {
      TargetOriginId: 'S3-my-website',
      ViewerProtocolPolicy: 'redirect-to-https',
      MinTTL: 0,
      DefaultTTL: 86400,  // 24 hours
      MaxTTL: 31536000     // 1 year
    },
    Enabled: true
  }
};

cloudfront.createDistribution(params);
```

---

## Compute Services

### Q3: Compare EC2, Lambda, Fargate, and ECS. When should you use each?

**Answer:**

**1. EC2 (Virtual Machines):**

**When to use:**
- Full control over OS
- Long-running applications
- Legacy applications
- Custom software requirements

**Example:**
```bash
# Launch EC2 instance
aws ec2 run-instances \
  --image-id ami-0c55b159cbfafe1f0 \
  --instance-type t3.micro \
  --key-name my-key \
  --security-group-ids sg-12345678 \
  --subnet-id subnet-12345678 \
  --user-data file://startup-script.sh

# startup-script.sh
#!/bin/bash
yum update -y
yum install -y docker
systemctl start docker
docker run -p 80:3000 myapp:latest
```

**Pros:**
- Full control
- Any software
- Persistent storage

**Cons:**
- Manage OS patches
- Manual scaling
- Always running (costly)

---

**2. Lambda (Serverless Functions):**

**When to use:**
- Event-driven tasks
- Short-lived processes (< 15 minutes)
- Unpredictable traffic
- Pay per execution

**Example:**
```javascript
// Lambda function
exports.handler = async (event) => {
    // Process S3 upload event
    const bucket = event.Records[0].s3.bucket.name;
    const key = event.Records[0].s3.object.key;
    
    // Resize image
    const image = await s3.getObject({ Bucket: bucket, Key: key }).promise();
    const resized = await sharp(image.Body).resize(200, 200).toBuffer();
    
    // Save thumbnail
    await s3.putObject({
        Bucket: bucket,
        Key: `thumbnails/${key}`,
        Body: resized
    }).promise();
    
    return { statusCode: 200 };
};

// Trigger: S3 upload
aws lambda create-event-source-mapping \
  --function-name image-processor \
  --event-source-arn arn:aws:s3:::my-bucket
```

**Pros:**
- No server management
- Automatic scaling
- Pay per execution
- Built-in high availability

**Cons:**
- 15-minute timeout
- Cold starts
- Limited resources

---

**3. ECS (Elastic Container Service):**

**When to use:**
- Docker containers
- Microservices
- Need orchestration
- Mixed workload types

**Example:**
```json
{
  "family": "my-app",
  "networkMode": "awsvpc",
  "containerDefinitions": [{
    "name": "web",
    "image": "myapp:latest",
    "cpu": 256,
    "memory": 512,
    "portMappings": [{
      "containerPort": 3000,
      "protocol": "tcp"
    }],
    "environment": [
      {"name": "NODE_ENV", "value": "production"}
    ],
    "logConfiguration": {
      "logDriver": "awslogs",
      "options": {
        "awslogs-group": "/ecs/my-app",
        "awslogs-region": "us-east-1"
      }
    }
  }],
  "requiresCompatibilities": ["FARGATE"],
  "cpu": "256",
  "memory": "512"
}
```

**Pros:**
- Container orchestration
- AWS integration
- Flexible

**Cons:**
- Manage cluster (EC2 mode)
- Complex setup

---

**4. Fargate (Serverless Containers):**

**When to use:**
- Docker containers
- Don't want to manage servers
- Predictable load
- Consistent workloads

**Example:**
```bash
# Create Fargate service
aws ecs create-service \
  --cluster my-cluster \
  --service-name my-service \
  --task-definition my-app:1 \
  --desired-count 3 \
  --launch-type FARGATE \
  --network-configuration "awsvpcConfiguration={subnets=[subnet-12345],securityGroups=[sg-12345]}" \
  --load-balancers "targetGroupArn=arn:aws:elasticloadbalancing:...,containerName=web,containerPort=3000"
```

**Pros:**
- No server management
- Container flexibility
- Automatic scaling

**Cons:**
- More expensive than EC2
- Less control than ECS on EC2

---

**Comparison Table:**

| Feature | EC2 | Lambda | ECS | Fargate |
|---------|-----|--------|-----|---------|
| Control | Full | None | Medium | Low |
| Scaling | Manual | Automatic | Manual/Auto | Automatic |
| Cost | Medium | Low (pay-per-use) | Low-Medium | Medium-High |
| Startup | Minutes | Milliseconds | Seconds | Seconds |
| Best for | Always-on | Event-driven | Microservices | Serverless containers |

---

## Storage & Databases

### Q4: Compare S3, EBS, EFS, and when to use each storage service.

**Answer:**

**1. S3 (Simple Storage Service) - Object Storage:**

**When to use:**
- Static files (images, videos, documents)
- Backups and archives
- Data lakes
- Static website hosting

**Example:**
```python
import boto3

s3 = boto3.client('s3')

# Upload file
s3.upload_file('photo.jpg', 'my-bucket', 'images/photo.jpg')

# Public URL
url = f"https://my-bucket.s3.amazonaws.com/images/photo.jpg"

# Set lifecycle policy
s3.put_bucket_lifecycle_configuration(
    Bucket='my-bucket',
    LifecycleConfiguration={
        'Rules': [{
            'Id': 'Move to Glacier',
            'Status': 'Enabled',
            'Transitions': [{
                'Days': 90,
                'StorageClass': 'GLACIER'
            }]
        }]
    }
)

# Versioning
s3.put_bucket_versioning(
    Bucket='my-bucket',
    VersioningConfiguration={'Status': 'Enabled'}
)
```

**Storage Classes:**
- **S3 Standard**: Frequently accessed
- **S3 Intelligent-Tiering**: Unknown access patterns
- **S3 Standard-IA**: Infrequently accessed
- **S3 Glacier**: Archive (minutes to hours retrieval)
- **S3 Glacier Deep Archive**: Long-term archive (12 hours retrieval)

---

**2. EBS (Elastic Block Store) - Block Storage:**

**When to use:**
- EC2 instance storage
- Databases on EC2
- High IOPS workloads
- Need file system

**Example:**
```bash
# Create EBS volume
aws ec2 create-volume \
  --size 100 \
  --volume-type gp3 \
  --iops 3000 \
  --availability-zone us-east-1a

# Attach to EC2
aws ec2 attach-volume \
  --volume-id vol-12345678 \
  --instance-id i-12345678 \
  --device /dev/sdf

# Snapshot for backup
aws ec2 create-snapshot \
  --volume-id vol-12345678 \
  --description "Daily backup"
```

**Volume Types:**
- **gp3/gp2**: General purpose SSD
- **io2/io1**: Provisioned IOPS SSD (databases)
- **st1**: Throughput optimized HDD (big data)
- **sc1**: Cold HDD (infrequent access)

---

**3. EFS (Elastic File System) - Shared File Storage:**

**When to use:**
- Multiple EC2 instances need shared storage
- Kubernetes persistent volumes
- Content management
- Web serving

**Example:**
```yaml
# EFS for multiple EC2 instances
Resources:
  MyEFS:
    Type: AWS::EFS::FileSystem
    Properties:
      Encrypted: true
      PerformanceMode: generalPurpose

  MountTarget1:
    Type: AWS::EFS::MountTarget
    Properties:
      FileSystemId: !Ref MyEFS
      SubnetId: subnet-1a
      SecurityGroups: [!Ref EFSSecurityGroup]

# Mount on EC2
#!/bin/bash
sudo yum install -y amazon-efs-utils
sudo mkdir /mnt/efs
sudo mount -t efs fs-12345678:/ /mnt/efs
```

---

**4. FSx (Managed File Systems):**

**When to use:**
- Windows file shares (FSx for Windows)
- High-performance computing (FSx for Lustre)
- NetApp ONTAP workloads

---

**Comparison:**

| Feature | S3 | EBS | EFS | FSx |
|---------|----|----|-----|-----|
| Type | Object | Block | File | File |
| Access | HTTP/API | Mounted | Mounted | Mounted |
| Sharing | ✅ | ❌ | ✅ | ✅ |
| Size | Unlimited | 16 TB | Unlimited | Up to PBs |
| Use Case | Static files | Databases | Shared files | Enterprise |
| Cost | Low | Medium | High | Very High |

---

### Q5: Compare RDS, DynamoDB, Aurora, and Redshift. When should you use each?

**Answer:**

**1. RDS (Relational Database Service):**

**When to use:**
- Traditional SQL databases
- ACID transactions
- Complex queries with joins
- Existing applications

**Supported engines:**
- PostgreSQL, MySQL, MariaDB, Oracle, SQL Server

**Example:**
```python
import psycopg2

# Connect to RDS
conn = psycopg2.connect(
    host='mydb.us-east-1.rds.amazonaws.com',
    database='myapp',
    user='admin',
    password='password'
)

# Query
cursor = conn.cursor()
cursor.execute('''
    SELECT u.name, COUNT(o.id) as order_count
    FROM users u
    LEFT JOIN orders o ON u.id = o.user_id
    WHERE u.created_at > NOW() - INTERVAL '30 days'
    GROUP BY u.id
''')
results = cursor.fetchall()
```

**Features:**
- Automated backups
- Multi-AZ deployment
- Read replicas
- Point-in-time recovery

---

**2. Aurora (MySQL/PostgreSQL compatible):**

**When to use:**
- Need better performance than RDS
- High availability requirements
- Read-heavy workloads

**Example:**
```python
# Aurora Serverless
import boto3

rds = boto3.client('rds')

# Create serverless cluster
rds.create_db_cluster(
    DBClusterIdentifier='my-aurora-cluster',
    Engine='aurora-postgresql',
    EngineMode='serverless',
    ScalingConfiguration={
        'MinCapacity': 2,
        'MaxCapacity': 16,
        'AutoPause': True,
        'SecondsUntilAutoPause': 300
    }
)
```

**Benefits:**
- 5x faster than MySQL
- Up to 15 read replicas
- Global database (cross-region)
- Serverless option (auto-scaling)

---

**3. DynamoDB (NoSQL):**

**When to use:**
- Key-value or document data
- Massive scale (millions of requests/second)
- Flexible schema
- Low latency required

**Example:**
```python
import boto3

dynamodb = boto3.resource('dynamodb')
table = dynamodb.Table('Users')

# Put item
table.put_item(
    Item={
        'userId': '123',
        'name': 'John Doe',
        'email': 'john@example.com',
        'settings': {
            'theme': 'dark',
            'notifications': True
        }
    }
)

# Query with sort key
response = table.query(
    KeyConditionExpression='userId = :userId AND createdAt > :date',
    ExpressionAttributeValues={
        ':userId': '123',
        ':date': '2026-01-01'
    }
)

# Global Secondary Index for different queries
table.query(
    IndexName='email-index',
    KeyConditionExpression='email = :email',
    ExpressionAttributeValues={
        ':email': 'john@example.com'
    }
)
```

**Features:**
- Single-digit millisecond latency
- Auto-scaling
- Global tables (multi-region)
- DynamoDB Streams (change capture)

---

**4. Redshift (Data Warehouse):**

**When to use:**
- Analytics and reporting
- Large datasets (petabytes)
- Complex analytical queries
- Business intelligence

**Example:**
```sql
-- Create Redshift cluster
CREATE TABLE sales (
    sale_id BIGINT,
    product_id INT,
    customer_id INT,
    sale_date DATE,
    amount DECIMAL(10,2)
)
DISTKEY(customer_id)
SORTKEY(sale_date);

-- Analytical query
SELECT 
    DATE_TRUNC('month', sale_date) as month,
    product_id,
    SUM(amount) as total_sales,
    AVG(amount) as avg_sale,
    COUNT(*) as sale_count
FROM sales
WHERE sale_date >= '2025-01-01'
GROUP BY month, product_id
ORDER BY month, total_sales DESC;

-- Load data from S3
COPY sales
FROM 's3://my-bucket/sales-data/'
IAM_ROLE 'arn:aws:iam::123456789012:role/RedshiftRole'
CSV;
```

---

**Comparison:**

| Database | Type | Use Case | Scale | Query | Cost |
|----------|------|----------|-------|-------|------|
| RDS | SQL | OLTP | Medium | Complex SQL | Medium |
| Aurora | SQL | OLTP | High | Complex SQL | Medium-High |
| DynamoDB | NoSQL | Key-value | Very High | Simple | Low-Medium |
| Redshift | SQL | Analytics | Very High | Complex SQL | High |

**Decision Tree:**
```
Need SQL? 
├─ Yes → Need analytics?
│         ├─ Yes → Redshift
│         └─ No → Need high performance?
│                  ├─ Yes → Aurora
│                  └─ No → RDS
└─ No → DynamoDB
```

---

## Networking & Content Delivery

### Q6: Explain VPC, subnets, route tables, and how to design a secure network architecture.

**Answer:**

**VPC Architecture:**

```
VPC (10.0.0.0/16)
├── Public Subnet (10.0.1.0/24) - AZ-1a
│   ├── NAT Gateway
│   └── Load Balancer
├── Public Subnet (10.0.2.0/24) - AZ-1b
│   └── Load Balancer
├── Private Subnet (10.0.10.0/24) - AZ-1a
│   ├── Application Servers
│   └── Web Servers
├── Private Subnet (10.0.11.0/24) - AZ-1b
│   ├── Application Servers
│   └── Web Servers
├── Database Subnet (10.0.20.0/24) - AZ-1a
│   └── RDS Primary
└── Database Subnet (10.0.21.0/24) - AZ-1b
    └── RDS Standby
```

**Implementation:**

```yaml
# CloudFormation VPC
Resources:
  # VPC
  MyVPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 10.0.0.0/16
      EnableDnsHostnames: true
      EnableDnsSupport: true

  # Internet Gateway
  InternetGateway:
    Type: AWS::EC2::InternetGateway

  AttachGateway:
    Type: AWS::EC2::VPCGatewayAttachment
    Properties:
      VpcId: !Ref MyVPC
      InternetGatewayId: !Ref InternetGateway

  # Public Subnet
  PublicSubnet1:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref MyVPC
      CidrBlock: 10.0.1.0/24
      AvailabilityZone: us-east-1a
      MapPublicIpOnLaunch: true

  # Private Subnet
  PrivateSubnet1:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref MyVPC
      CidrBlock: 10.0.10.0/24
      AvailabilityZone: us-east-1a

  # NAT Gateway
  NATGateway:
    Type: AWS::EC2::NatGateway
    Properties:
      AllocationId: !GetAtt EIP.AllocationId
      SubnetId: !Ref PublicSubnet1

  EIP:
    Type: AWS::EC2::EIP
    Properties:
      Domain: vpc

  # Route Tables
  PublicRouteTable:
    Type: AWS::EC2::RouteTable
    Properties:
      VpcId: !Ref MyVPC

  PublicRoute:
    Type: AWS::EC2::Route
    Properties:
      RouteTableId: !Ref PublicRouteTable
      DestinationCidrBlock: 0.0.0.0/0
      GatewayId: !Ref InternetGateway

  PrivateRouteTable:
    Type: AWS::EC2::RouteTable
    Properties:
      VpcId: !Ref MyVPC

  PrivateRoute:
    Type: AWS::EC2::Route
    Properties:
      RouteTableId: !Ref PrivateRouteTable
      DestinationCidrBlock: 0.0.0.0/0
      NatGatewayId: !Ref NATGateway

  # Security Groups
  ALBSecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: ALB Security Group
      VpcId: !Ref MyVPC
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIp: 0.0.0.0/0
        - IpProtocol: tcp
          FromPort: 443
          ToPort: 443
          CidrIp: 0.0.0.0/0

  AppSecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: Application Security Group
      VpcId: !Ref MyVPC
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 3000
          ToPort: 3000
          SourceSecurityGroupId: !Ref ALBSecurityGroup

  DBSecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: Database Security Group
      VpcId: !Ref MyVPC
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 5432
          ToPort: 5432
          SourceSecurityGroupId: !Ref AppSecurityGroup
```

**Security Best Practices:**

**1. Network ACLs (Stateless):**
```yaml
NetworkACL:
  Type: AWS::EC2::NetworkAcl
  Properties:
    VpcId: !Ref MyVPC

InboundRule:
  Type: AWS::EC2::NetworkAclEntry
  Properties:
    NetworkAclId: !Ref NetworkACL
    RuleNumber: 100
    Protocol: 6  # TCP
    RuleAction: allow
    CidrBlock: 0.0.0.0/0
    PortRange:
      From: 443
      To: 443
```

**2. VPC Flow Logs:**
```python
import boto3

ec2 = boto3.client('ec2')

ec2.create_flow_logs(
    ResourceIds=['vpc-12345678'],
    ResourceType='VPC',
    TrafficType='ALL',
    LogDestinationType='cloud-watch-logs',
    LogGroupName='/aws/vpc/flowlogs'
)
```

**3. PrivateLink (Internal services):**
```yaml
VPCEndpoint:
  Type: AWS::EC2::VPCEndpoint
  Properties:
    VpcId: !Ref MyVPC
    ServiceName: com.amazonaws.us-east-1.s3
    RouteTableIds:
      - !Ref PrivateRouteTable
```

---

## Security & Identity

### Q7: Explain IAM best practices and how to implement least privilege access.

**Answer:**

**IAM Hierarchy:**
```
AWS Account (Root)
├── IAM Users (Individuals)
├── IAM Groups (Collections of users)
├── IAM Roles (Temporary credentials)
└── IAM Policies (Permissions)
```

**Best Practices:**

**1. Never use Root Account:**
```bash
# Create admin user instead
aws iam create-user --user-name admin

# Attach admin policy
aws iam attach-user-policy \
  --user-name admin \
  --policy-arn arn:aws:iam::aws:policy/AdministratorAccess

# Enable MFA
aws iam enable-mfa-device \
  --user-name admin \
  --serial-number arn:aws:iam::123456789012:mfa/admin \
  --authentication-code1 123456 \
  --authentication-code2 789012
```

---

**2. Use Groups:**
```json
// Create groups
{
  "Developers": {
    "Policies": [
      "AmazonEC2ReadOnlyAccess",
      "AmazonS3FullAccess",
      "CloudWatchLogsReadOnlyAccess"
    ]
  },
  "DevOps": {
    "Policies": [
      "PowerUserAccess",
      "IAMReadOnlyAccess"
    ]
  },
  "ReadOnly": {
    "Policies": [
      "ViewOnlyAccess"
    ]
  }
}
```

---

**3. Least Privilege Policies:**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowS3ListBucket",
      "Effect": "Allow",
      "Action": "s3:ListBucket",
      "Resource": "arn:aws:s3:::my-app-bucket"
    },
    {
      "Sid": "AllowS3GetPutObject",
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:PutObject"
      ],
      "Resource": "arn:aws:s3:::my-app-bucket/uploads/*"
    },
    {
      "Sid": "DenyDeleteObject",
      "Effect": "Deny",
      "Action": "s3:DeleteObject",
      "Resource": "arn:aws:s3:::my-app-bucket/*"
    }
  ]
}
```

---

**4. Use Roles for Services:**
```yaml
# EC2 instance role
Resources:
  EC2Role:
    Type: AWS::IAM::Role
    Properties:
      AssumeRolePolicyDocument:
        Version: '2012-10-17'
        Statement:
          - Effect: Allow
            Principal:
              Service: ec2.amazonaws.com
            Action: 'sts:AssumeRole'
      ManagedPolicyArns:
        - arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess
      Policies:
        - PolicyName: DynamoDBAccess
          PolicyDocument:
            Version: '2012-10-17'
            Statement:
              - Effect: Allow
                Action:
                  - 'dynamodb:GetItem'
                  - 'dynamodb:Query'
                Resource: 'arn:aws:dynamodb:*:*:table/MyTable'

  InstanceProfile:
    Type: AWS::IAM::InstanceProfile
    Properties:
      Roles:
        - !Ref EC2Role
```

---

**5. Cross-Account Access:**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::111122223333:root"
      },
      "Action": "sts:AssumeRole",
      "Condition": {
        "StringEquals": {
          "sts:ExternalId": "unique-external-id"
        }
      }
    }
  ]
}
```

---

**6. Policy Conditions:**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "s3:*",
      "Resource": "*",
      "Condition": {
        "IpAddress": {
          "aws:SourceIp": "203.0.113.0/24"
        },
        "DateGreaterThan": {
          "aws:CurrentTime": "2026-01-01T00:00:00Z"
        },
        "Bool": {
          "aws:SecureTransport": "true"
        }
      }
    }
  ]
}
```

---

**7. AWS Organizations (Multi-account):**
```
Root
├── Production OU
│   ├── Prod Account 1
│   └── Prod Account 2
├── Development OU
│   ├── Dev Account 1
│   └── Dev Account 2
└── Security OU
    └── Security Audit Account

Service Control Policies (SCPs):
- Deny all in Production except approved services
- Require encryption
- Enforce MFA
```

---

## DevOps & Deployment

### Q8: How do you implement CI/CD on AWS?

**Answer:**

**CI/CD Pipeline Architecture:**

```
GitHub → CodePipeline → CodeBuild → CodeDeploy → EC2/ECS/Lambda
                ↓            ↓            ↓
            CodeCommit   ECR/S3    CloudFormation
```

**Full Example:**

**1. Source (CodeCommit):**
```bash
# Create repository
aws codecommit create-repository --repository-name my-app

# Clone and push
git clone https://git-codecommit.us-east-1.amazonaws.com/v1/repos/my-app
cd my-app
git add .
git commit -m "Initial commit"
git push
```

---

**2. Build (CodeBuild):**
```yaml
# buildspec.yml
version: 0.2

phases:
  pre_build:
    commands:
      - echo Logging in to Amazon ECR...
      - aws ecr get-login-password | docker login --username AWS --password-stdin $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com
      - COMMIT_HASH=$(echo $CODEBUILD_RESOLVED_SOURCE_VERSION | cut -c 1-7)
      - IMAGE_TAG=${COMMIT_HASH:=latest}
  
  build:
    commands:
      - echo Build started on `date`
      - echo Building the Docker image...
      - docker build -t $IMAGE_REPO_NAME:$IMAGE_TAG .
      - docker tag $IMAGE_REPO_NAME:$IMAGE_TAG $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$IMAGE_REPO_NAME:$IMAGE_TAG
  
  post_build:
    commands:
      - echo Build completed on `date`
      - echo Pushing the Docker image...
      - docker push $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$IMAGE_REPO_NAME:$IMAGE_TAG
      - echo Writing image definitions file...
      - printf '[{"name":"web","imageUri":"%s"}]' $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$IMAGE_REPO_NAME:$IMAGE_TAG > imagedefinitions.json

artifacts:
  files:
    - imagedefinitions.json
```

---

**3. Deploy (CodeDeploy):**
```yaml
# appspec.yml (EC2/On-premises)
version: 0.0
os: linux
files:
  - source: /
    destination: /var/www/html
hooks:
  BeforeInstall:
    - location: scripts/install_dependencies.sh
      timeout: 300
  ApplicationStart:
    - location: scripts/start_server.sh
      timeout: 300
  ValidateService:
    - location: scripts/validate_service.sh
      timeout: 300

# For ECS
{
  "version": 1,
  "Resources": [
    {
      "TargetService": {
        "Type": "AWS::ECS::Service",
        "Properties": {
          "TaskDefinition": "<TASK_DEFINITION>",
          "LoadBalancerInfo": {
            "ContainerName": "web",
            "ContainerPort": 80
          }
        }
      }
    }
  ]
}
```

---

**4. Pipeline (CodePipeline):**
```yaml
# CloudFormation template
Resources:
  Pipeline:
    Type: AWS::CodePipeline::Pipeline
    Properties:
      RoleArn: !GetAtt PipelineRole.Arn
      ArtifactStore:
        Type: S3
        Location: !Ref ArtifactBucket
      Stages:
        # Source Stage
        - Name: Source
          Actions:
            - Name: SourceAction
              ActionTypeId:
                Category: Source
                Owner: AWS
                Provider: CodeCommit
                Version: 1
              Configuration:
                RepositoryName: my-app
                BranchName: main
              OutputArtifacts:
                - Name: SourceOutput

        # Build Stage
        - Name: Build
          Actions:
            - Name: BuildAction
              ActionTypeId:
                Category: Build
                Owner: AWS
                Provider: CodeBuild
                Version: 1
              Configuration:
                ProjectName: !Ref CodeBuildProject
              InputArtifacts:
                - Name: SourceOutput
              OutputArtifacts:
                - Name: BuildOutput

        # Test Stage
        - Name: Test
          Actions:
            - Name: TestAction
              ActionTypeId:
                Category: Test
                Owner: AWS
                Provider: CodeBuild
                Version: 1
              Configuration:
                ProjectName: !Ref TestProject
              InputArtifacts:
                - Name: BuildOutput

        # Manual Approval
        - Name: Approval
          Actions:
            - Name: ManualApproval
              ActionTypeId:
                Category: Approval
                Owner: AWS
                Provider: Manual
                Version: 1
              Configuration:
                CustomData: "Approve production deployment?"

        # Deploy Stage
        - Name: Deploy
          Actions:
            - Name: DeployAction
              ActionTypeId:
                Category: Deploy
                Owner: AWS
                Provider: ECS
                Version: 1
              Configuration:
                ClusterName: my-cluster
                ServiceName: my-service
                FileName: imagedefinitions.json
              InputArtifacts:
                - Name: BuildOutput
```

---

## Cost Optimization

### Q9: How do you optimize AWS costs for a production application?

**Answer:**

**Cost Optimization Strategies:**

**1. Right-Sizing Instances:**
```python
# Use AWS Cost Explorer API
import boto3

ce = boto3.client('ce')

# Get rightsizing recommendations
response = ce.get_rightsizing_recommendation(
    Service='AmazonEC2',
    Configuration={
        'RecommendationTarget': 'SAME_INSTANCE_FAMILY',
        'BenefitsConsidered': True
    }
)

for recommendation in response['RightsizingRecommendations']:
    print(f"Instance: {recommendation['CurrentInstance']}")
    print(f"Recommended: {recommendation['RightsizingType']}")
    print(f"Estimated Savings: ${recommendation['EstimatedMonthlySavings']}")
```

---

**2. Reserved Instances / Savings Plans:**
```bash
# 1-year commitment saves ~40%
# 3-year commitment saves ~60%

# Compute Savings Plan (most flexible)
aws savingsplans purchase-savings-plan \
  --savings-plan-offering-id <offering-id> \
  --commitment 100  # $100/hour commitment
  --upfront-payment-amount 0  # No upfront
```

---

**3. Spot Instances:**
```yaml
# Use Spot for non-critical workloads
Resources:
  SpotFleet:
    Type: AWS::EC2::SpotFleet
    Properties:
      SpotFleetRequestConfigData:
        AllocationStrategy: lowestPrice
        IamFleetRole: !GetAtt SpotFleetRole.Arn
        TargetCapacity: 10
        SpotPrice: "0.05"
        LaunchSpecifications:
          - ImageId: ami-12345678
            InstanceType: c5.large
          - ImageId: ami-12345678
            InstanceType: c5.xlarge
```

**Example savings:**
- On-Demand: $0.85/hour
- Spot: $0.25/hour
- Savings: 70%!

---

**4. S3 Lifecycle Policies:**
```json
{
  "Rules": [
    {
      "Id": "LogArchival",
      "Status": "Enabled",
      "Filter": {
        "Prefix": "logs/"
      },
      "Transitions": [
        {
          "Days": 30,
          "StorageClass": "STANDARD_IA"
        },
        {
          "Days": 90,
          "StorageClass": "GLACIER"
        },
        {
          "Days": 180,
          "StorageClass": "DEEP_ARCHIVE"
        }
      ],
      "Expiration": {
        "Days": 365
      }
    }
  ]
}
```

**Cost comparison:**
- S3 Standard: $0.023/GB
- S3 IA: $0.0125/GB (45% cheaper)
- Glacier: $0.004/GB (83% cheaper)
- Deep Archive: $0.00099/GB (96% cheaper!)

---

**5. Lambda vs EC2:**
```python
# Cost comparison

# EC2 t3.micro (always on)
ec2_monthly = 0.0104 * 24 * 30  # $7.49/month

# Lambda (1M requests, 128MB, 200ms avg)
lambda_requests = 1_000_000 * 0.20 / 1000  # $0.20
lambda_compute = 1_000_000 * 0.200 * 0.125 * 0.0000166667  # $0.42
lambda_monthly = lambda_requests + lambda_compute  # $0.62/month

# Lambda is 92% cheaper!
```

---

**6. CloudWatch Costs:**
```python
# Reduce log retention
import boto3

logs = boto3.client('logs')

# Set retention to 7 days
logs.put_retention_policy(
    logGroupName='/aws/lambda/my-function',
    retentionInDays=7
)

# Delete old log groups
response = logs.describe_log_groups()
for group in response['logGroups']:
    if 'lastEventTime' in group:
        age_days = (now - group['lastEventTime']) / (1000 * 86400)
        if age_days > 90:
            logs.delete_log_group(logGroupName=group['logGroupName'])
```

---

**7. Cost Monitoring:**
```python
# Set budget alerts
import boto3

budgets = boto3.client('budgets')

budgets.create_budget(
    AccountId='123456789012',
    Budget={
        'BudgetName': 'Monthly-Budget',
        'BudgetLimit': {
            'Amount': '1000',
            'Unit': 'USD'
        },
        'TimeUnit': 'MONTHLY',
        'BudgetType': 'COST'
    },
    NotificationsWithSubscribers=[
        {
            'Notification': {
                'NotificationType': 'ACTUAL',
                'ComparisonOperator': 'GREATER_THAN',
                'Threshold': 80,
                'ThresholdType': 'PERCENTAGE'
            },
            'Subscribers': [
                {
                    'SubscriptionType': 'EMAIL',
                    'Address': 'team@example.com'
                }
            ]
        }
    ]
)
```

---

## Summary

**AWS Best Practices:**

1. **Architecture**: Well-Architected Framework (5 pillars)
2. **Compute**: Choose right service (EC2, Lambda, Fargate)
3. **Storage**: Use appropriate storage (S3, EBS, EFS)
4. **Database**: Match workload (RDS, Aurora, DynamoDB, Redshift)
5. **Networking**: Secure VPC design
6. **Security**: IAM least privilege, encryption
7. **Monitoring**: CloudWatch, X-Ray, CloudTrail
8. **CI/CD**: CodePipeline automation
9. **Cost**: Reserved Instances, Spot, rightsizing
10. **High Availability**: Multi-AZ, Auto Scaling

**Remember:** AWS offers 200+ services. Master the core services first, then expand based on your needs!
