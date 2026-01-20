# Azure - Senior Developer to Architect Level Q&A

## Table of Contents
1. [Core Services & Architecture](#core-services--architecture)
2. [Compute Services](#compute-services)
3. [Storage & Databases](#storage--databases)
4. [Networking & Security](#networking--security)
5. [Identity & Access Management](#identity--access-management)
6. [DevOps & Deployment](#devops--deployment)
7. [Monitoring & Governance](#monitoring--governance)

---

## Core Services & Architecture

### Q1: Explain Azure's core architecture concepts: Subscriptions, Resource Groups, and Management Groups.

**Answer:**

**Simple Analogy:**
Think of Azure like an office building:
- **Management Groups** = Different floors
- **Subscriptions** = Departments on each floor
- **Resource Groups** = Teams within departments
- **Resources** = Individual employees

**Hierarchy:**

```
Root Management Group
├── Production Management Group
│   ├── Prod Subscription 1
│   │   ├── RG-WebApp
│   │   │   ├── App Service
│   │   │   └── SQL Database
│   │   └── RG-Infrastructure
│   │       ├── Virtual Network
│   │       └── Load Balancer
│   └── Prod Subscription 2
└── Development Management Group
    └── Dev Subscription
        └── RG-DevEnvironment
```

**1. Management Groups:**
- Organize multiple subscriptions
- Apply policies at scale
- Implement governance

**Example:**
```powershell
# Create management group
New-AzManagementGroup -GroupId "Production" -DisplayName "Production Environment"

# Assign policy to management group
New-AzManagementGroupDeployment `
  -ManagementGroupId "Production" `
  -Location "eastus" `
  -PolicyDefinition (Get-AzPolicyDefinition | Where-Object {$_.Properties.DisplayName -eq "Require tag on resources"})
```

---

**2. Subscriptions:**
- Billing boundary
- Access control boundary
- Resource limit boundary

**Example:**
```bash
# List subscriptions
az account list --output table

# Set active subscription
az account set --subscription "Production-Subscription"

# Create resource group in subscription
az group create \
  --name myResourceGroup \
  --location eastus \
  --tags Environment=Production Team=Backend
```

---

**3. Resource Groups:**
- Logical container for resources
- Lifecycle management
- RBAC scope
- Tagging and cost management

**Example:**
```powershell
# Create resource group
New-AzResourceGroup `
  -Name "RG-WebApp" `
  -Location "EastUS" `
  -Tag @{
    Environment="Production"
    CostCenter="Engineering"
    Owner="john@company.com"
  }

# Deploy resources to resource group
New-AzResourceGroupDeployment `
  -ResourceGroupName "RG-WebApp" `
  -TemplateFile "./template.json" `
  -TemplateParameterFile "./parameters.json"

# Delete entire resource group (deletes all resources)
Remove-AzResourceGroup -Name "RG-WebApp" -Force
```

---

**ARM Template Example:**
```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "appServicePlanName": {
      "type": "string"
    },
    "webAppName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Web/serverfarms",
      "apiVersion": "2021-02-01",
      "name": "[parameters('appServicePlanName')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "S1",
        "tier": "Standard"
      },
      "properties": {}
    },
    {
      "type": "Microsoft.Web/sites",
      "apiVersion": "2021-02-01",
      "name": "[parameters('webAppName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', parameters('appServicePlanName'))]"
      ],
      "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', parameters('appServicePlanName'))]",
        "siteConfig": {
          "appSettings": [
            {
              "name": "WEBSITE_NODE_DEFAULT_VERSION",
              "value": "18-lts"
            }
          ]
        }
      }
    }
  ]
}
```

---

### Q2: Compare Azure regions, availability zones, and how to design for high availability.

**Answer:**

**Architecture Hierarchy:**

```
Geography (e.g., United States)
├── Region (e.g., East US)
│   ├── Availability Zone 1
│   │   └── Data Center 1, 2
│   ├── Availability Zone 2
│   │   └── Data Center 3, 4
│   └── Availability Zone 3
│       └── Data Center 5, 6
└── Region (e.g., West US)
```

**1. Regions:**
- Geographic location with multiple data centers
- 60+ regions worldwide
- Latency optimization

**Example:**
```bash
# List available regions
az account list-locations --output table

# Create resources in specific region
az vm create \
  --resource-group myRG \
  --name myVM \
  --location eastus \
  --image Ubuntu2204 \
  --size Standard_B2s
```

---

**2. Availability Zones:**
- Physically separate data centers within a region
- Independent power, cooling, networking
- 99.99% SLA

**Example:**
```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2021-03-01",
  "name": "myVM",
  "location": "eastus",
  "zones": ["1"],
  "properties": {
    "hardwareProfile": {
      "vmSize": "Standard_D2s_v3"
    }
  }
}
```

---

**3. High Availability Architecture:**

```
Azure Front Door (Global Load Balancing)
├── East US (Primary Region)
│   ├── AZ-1: App Gateway → VMSS (2 VMs)
│   ├── AZ-2: App Gateway → VMSS (2 VMs)
│   └── AZ-3: App Gateway → VMSS (2 VMs)
│       └── Azure SQL (Zone Redundant)
└── West US (Secondary Region - DR)
    └── Same setup with geo-replication
```

**Implementation:**
```bicep
// Bicep template for HA deployment
resource appGateway 'Microsoft.Network/applicationGateways@2021-05-01' = {
  name: 'myAppGateway'
  location: location
  zones: ['1', '2', '3']  // Zone redundant
  properties: {
    sku: {
      name: 'Standard_v2'
      tier: 'Standard_v2'
    }
    backendAddressPools: [
      {
        name: 'backendPool'
        properties: {
          backendAddresses: [
            {
              fqdn: vmss1.properties.virtualMachineProfile.networkProfile
            }
          ]
        }
      }
    ]
  }
}

// VMSS with availability zones
resource vmss 'Microsoft.Compute/virtualMachineScaleSets@2021-07-01' = {
  name: 'myVMSS'
  location: location
  zones: ['1', '2', '3']
  sku: {
    name: 'Standard_D2s_v3'
    tier: 'Standard'
    capacity: 6  // 2 per zone
  }
  properties: {
    upgradePolicy: {
      mode: 'Automatic'
    }
    virtualMachineProfile: {
      storageProfile: {
        imageReference: {
          publisher: 'Canonical'
          offer: 'UbuntuServer'
          sku: '18.04-LTS'
          version: 'latest'
        }
      }
    }
  }
}

// Zone-redundant Azure SQL
resource sqlServer 'Microsoft.Sql/servers@2021-05-01-preview' = {
  name: 'mySqlServer'
  location: location
  properties: {
    administratorLogin: 'sqladmin'
    administratorLoginPassword: sqlPassword
  }
}

resource sqlDatabase 'Microsoft.Sql/servers/databases@2021-05-01-preview' = {
  parent: sqlServer
  name: 'myDatabase'
  location: location
  sku: {
    name: 'S3'
    tier: 'Standard'
  }
  properties: {
    zoneRedundant: true  // Replicated across zones
  }
}
```

---

**4. Disaster Recovery (Cross-Region):**

```powershell
# Azure Site Recovery for DR
$vault = New-AzRecoveryServicesVault `
  -Name "myVault" `
  -ResourceGroupName "myRG" `
  -Location "EastUS"

# Enable replication to secondary region
New-AzRecoveryServicesAsrProtectionContainer `
  -Name "primary-container" `
  -RecoveryVault $vault

# Failover to secondary region
Start-AzRecoveryServicesAsrPlannedFailoverJob `
  -ProtectionEntity $protectionEntity `
  -Direction PrimaryToRecovery
```

---

## Compute Services

### Q3: Compare Azure VMs, App Service, Container Instances, AKS, and Functions. When should you use each?

**Answer:**

**1. Virtual Machines (IaaS):**

**When to use:**
- Full control over OS
- Legacy applications
- Custom software
- Lift-and-shift migrations

**Example:**
```bash
# Create VM
az vm create \
  --resource-group myRG \
  --name myVM \
  --image Ubuntu2204 \
  --size Standard_D2s_v3 \
  --admin-username azureuser \
  --generate-ssh-keys \
  --public-ip-sku Standard

# SSH into VM
az vm run-command invoke \
  --resource-group myRG \
  --name myVM \
  --command-id RunShellScript \
  --scripts "sudo apt update && sudo apt install -y nginx"

# Auto-shutdown (cost savings)
az vm auto-shutdown \
  --resource-group myRG \
  --name myVM \
  --time 1900
```

**Pros:**
- Complete control
- Any software
- Flexible

**Cons:**
- Manage OS
- Manual scaling
- Higher operational overhead

---

**2. App Service (PaaS):**

**When to use:**
- Web applications
- REST APIs
- Mobile backends
- Don't want to manage servers

**Example:**
```bash
# Create App Service Plan
az appservice plan create \
  --name myAppServicePlan \
  --resource-group myRG \
  --sku S1 \
  --is-linux

# Create Web App
az webapp create \
  --name myUniqueWebApp \
  --resource-group myRG \
  --plan myAppServicePlan \
  --runtime "NODE:18-lts"

# Deploy from GitHub
az webapp deployment source config \
  --name myUniqueWebApp \
  --resource-group myRG \
  --repo-url https://github.com/user/repo \
  --branch main \
  --manual-integration

# Configure app settings
az webapp config appsettings set \
  --name myUniqueWebApp \
  --resource-group myRG \
  --settings \
    NODE_ENV=production \
    DATABASE_URL=postgresql://server/db
```

**Auto-scaling:**
```json
{
  "profiles": [{
    "name": "Auto created scale condition",
    "capacity": {
      "minimum": "2",
      "maximum": "10",
      "default": "2"
    },
    "rules": [
      {
        "metricTrigger": {
          "metricName": "CpuPercentage",
          "operator": "GreaterThan",
          "threshold": 70,
          "timeAggregation": "Average"
        },
        "scaleAction": {
          "direction": "Increase",
          "value": "1",
          "cooldown": "PT5M"
        }
      }
    ]
  }]
}
```

**Pros:**
- No server management
- Built-in scaling
- Deployment slots (blue-green)
- Easy CI/CD integration

**Cons:**
- Less control
- Language/runtime limitations

---

**3. Container Instances (Serverless Containers):**

**When to use:**
- Quick container deployments
- Burst scenarios
- Batch jobs
- Development/testing

**Example:**
```bash
# Create container instance
az container create \
  --resource-group myRG \
  --name mycontainer \
  --image nginx \
  --dns-name-label mycontainer-unique \
  --ports 80

# With environment variables
az container create \
  --resource-group myRG \
  --name myapp \
  --image myapp:latest \
  --registry-login-server myregistry.azurecr.io \
  --registry-username myregistry \
  --registry-password <password> \
  --environment-variables \
    NODE_ENV=production \
    PORT=3000 \
  --cpu 2 \
  --memory 4
```

**Pros:**
- Fast startup
- No cluster management
- Pay per second

**Cons:**
- No orchestration
- Limited networking options

---

**4. AKS (Azure Kubernetes Service):**

**When to use:**
- Microservices architecture
- Container orchestration
- Complex deployments
- Multi-container applications

**Example:**
```bash
# Create AKS cluster
az aks create \
  --resource-group myRG \
  --name myAKSCluster \
  --node-count 3 \
  --node-vm-size Standard_D2s_v3 \
  --enable-managed-identity \
  --enable-addons monitoring \
  --generate-ssh-keys

# Get credentials
az aks get-credentials \
  --resource-group myRG \
  --name myAKSCluster

# Deploy application
kubectl apply -f - <<EOF
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: myapp
        image: myregistry.azurecr.io/myapp:v1
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
---
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: myapp
EOF

# Auto-scaling
kubectl autoscale deployment myapp \
  --cpu-percent=70 \
  --min=2 \
  --max=10
```

**Pros:**
- Full Kubernetes features
- Complex orchestration
- Multi-cloud portable

**Cons:**
- Learning curve
- Management overhead

---

**5. Azure Functions (Serverless):**

**When to use:**
- Event-driven processing
- Scheduled tasks
- Webhooks
- Short-lived operations

**Example:**
```javascript
// function.json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": ["get", "post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ]
}

// index.js
module.exports = async function (context, req) {
    context.log('HTTP trigger function processed a request.');
    
    const name = req.query.name || (req.body && req.body.name);
    
    // Process image from blob storage
    if (req.body.imageUrl) {
        // Resize image
        const resized = await resizeImage(req.body.imageUrl);
        
        // Save to blob
        await saveToBlobStorage(resized);
    }
    
    context.res = {
        status: 200,
        body: { message: `Hello ${name}!` }
    };
};
```

**Durable Functions (Orchestration):**
```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function* (context) {
    // Fan-out/fan-in pattern
    const tasks = [];
    
    for (let i = 0; i < 10; i++) {
        tasks.push(context.df.callActivity("ProcessItem", i));
    }
    
    // Wait for all to complete
    const results = yield context.df.Task.all(tasks);
    
    // Aggregate results
    return results.reduce((sum, val) => sum + val, 0);
});
```

**Pros:**
- No server management
- Auto-scaling
- Pay per execution
- Easy event integration

**Cons:**
- Cold starts
- Timeout limits (5-10 minutes)
- Stateless (use Durable Functions for state)

---

**Comparison:**

| Service | Type | Control | Scaling | Best For | Cost |
|---------|------|---------|---------|----------|------|
| VMs | IaaS | Full | Manual | Legacy apps | High |
| App Service | PaaS | Medium | Auto | Web apps | Medium |
| Container Instances | Serverless | Low | Manual | Quick containers | Low |
| AKS | CaaS | High | Auto | Microservices | Medium |
| Functions | Serverless | None | Auto | Event-driven | Very Low |

---

## Storage & Databases

### Q4: Compare Azure Storage options: Blob, File, Queue, Table, and Disk.

**Answer:**

**1. Blob Storage (Object Storage):**

**When to use:**
- Images, videos, documents
- Backups and archives
- Data lakes
- Static websites

**Storage Tiers:**
- **Hot**: Frequently accessed (highest cost, lowest access cost)
- **Cool**: Infrequently accessed (30+ days)
- **Archive**: Rarely accessed (180+ days, lowest cost)

**Example:**
```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;

// Create blob client
BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);
BlobContainerClient containerClient = blobServiceClient.GetBlobContainerClient("images");

// Upload blob
await containerClient.CreateIfNotExistsAsync();
BlobClient blobClient = containerClient.GetBlobClient("photo.jpg");

using (FileStream uploadFileStream = File.OpenRead("photo.jpg"))
{
    await blobClient.UploadAsync(uploadFileStream, overwrite: true);
}

// Set access tier
await blobClient.SetAccessTierAsync(AccessTier.Cool);

// Generate SAS token (temporary access)
BlobSasBuilder sasBuilder = new BlobSasBuilder()
{
    BlobContainerName = "images",
    BlobName = "photo.jpg",
    Resource = "b",
    StartsOn = DateTimeOffset.UtcNow,
    ExpiresOn = DateTimeOffset.UtcNow.AddHours(1)
};
sasBuilder.SetPermissions(BlobSasPermissions.Read);

Uri sasUri = blobClient.GenerateSasUri(sasBuilder);
```

**Lifecycle Management:**
```json
{
  "rules": [
    {
      "name": "moveToCool",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": ["blockBlob"],
          "prefixMatch": ["logs/"]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": {
              "daysAfterModificationGreaterThan": 30
            },
            "tierToArchive": {
              "daysAfterModificationGreaterThan": 90
            },
            "delete": {
              "daysAfterModificationGreaterThan": 365
            }
          }
        }
      }
    }
  ]
}
```

---

**2. Azure Files (File Shares):**

**When to use:**
- Shared file access (SMB/NFS)
- Lift-and-shift applications
- Configuration files
- Replace on-premises file servers

**Example:**
```bash
# Create file share
az storage share create \
  --name myshare \
  --account-name mystorageaccount \
  --quota 100

# Mount on Windows
net use Z: \\mystorageaccount.file.core.windows.net\myshare /u:AZURE\mystorageaccount <key>

# Mount on Linux
sudo mount -t cifs //mystorageaccount.file.core.windows.net/myshare /mnt/myshare \
  -o vers=3.0,username=mystorageaccount,password=<key>,dir_mode=0777,file_mode=0777
```

```python
# Python SDK
from azure.storage.fileshare import ShareFileClient

file_client = ShareFileClient.from_connection_string(
    connection_string,
    share_name="myshare",
    file_path="documents/report.pdf"
)

# Upload file
with open("report.pdf", "rb") as source_file:
    file_client.upload_file(source_file)

# Download file
with open("downloaded_report.pdf", "wb") as file_handle:
    data = file_client.download_file()
    data.readinto(file_handle)
```

---

**3. Queue Storage (Message Queue):**

**When to use:**
- Asynchronous processing
- Decouple components
- Work queue pattern
- Load leveling

**Example:**
```csharp
using Azure.Storage.Queues;

QueueClient queueClient = new QueueClient(connectionString, "orders");
await queueClient.CreateIfNotExistsAsync();

// Send message
await queueClient.SendMessageAsync("Process order #12345");

// Receive and process messages
QueueMessage[] messages = await queueClient.ReceiveMessagesAsync(maxMessages: 10);

foreach (QueueMessage message in messages)
{
    Console.WriteLine($"Processing: {message.MessageText}");
    
    // Process the message
    await ProcessOrder(message.MessageText);
    
    // Delete message after processing
    await queueClient.DeleteMessageAsync(message.MessageId, message.PopReceipt);
}
```

**Poison Queue Pattern:**
```csharp
// Retry failed messages
if (message.DequeueCount > 5)
{
    // Move to poison queue
    QueueClient poisonQueue = new QueueClient(connectionString, "orders-poison");
    await poisonQueue.SendMessageAsync(message.MessageText);
    await queueClient.DeleteMessageAsync(message.MessageId, message.PopReceipt);
}
```

---

**4. Table Storage (NoSQL):**

**When to use:**
- Structured NoSQL data
- High scale requirements
- Semi-structured data
- Cost-effective NoSQL

**Example:**
```csharp
using Azure.Data.Tables;

TableClient tableClient = new TableClient(connectionString, "Users");
await tableClient.CreateIfNotExistsAsync();

// Insert entity
var user = new TableEntity("USA", "user123")
{
    { "Name", "John Doe" },
    { "Email", "john@example.com" },
    { "Age", 30 }
};
await tableClient.AddEntityAsync(user);

// Query entities
var users = tableClient.QueryAsync<TableEntity>(
    filter: $"PartitionKey eq 'USA' and Age gt 25"
);

await foreach (var entity in users)
{
    Console.WriteLine($"{entity["Name"]}: {entity["Email"]}");
}
```

---

**5. Managed Disks:**

**When to use:**
- VM storage
- Database files
- High IOPS workloads

**Disk Types:**
- **Ultra Disk**: Highest performance (160,000 IOPS)
- **Premium SSD**: Production workloads (20,000 IOPS)
- **Standard SSD**: Web servers (6,000 IOPS)
- **Standard HDD**: Backup, dev/test (500 IOPS)

**Example:**
```bash
# Create managed disk
az disk create \
  --resource-group myRG \
  --name myDataDisk \
  --size-gb 128 \
  --sku Premium_LRS

# Attach to VM
az vm disk attach \
  --resource-group myRG \
  --vm-name myVM \
  --name myDataDisk
```

---

**Comparison:**

| Storage | Type | Protocol | Use Case | Max Size |
|---------|------|----------|----------|----------|
| Blob | Object | HTTP/REST | Unstructured data | 5 PB |
| Files | File | SMB/NFS | Shared files | 100 TB |
| Queue | Message | HTTP/REST | Async processing | Unlimited |
| Table | NoSQL | HTTP/REST | Structured data | Unlimited |
| Disk | Block | N/A (attached) | VM storage | 32 TB |

---

### Q5: Compare Azure SQL, Cosmos DB, and PostgreSQL. When should you use each?

**Answer:**

**1. Azure SQL Database:**

**When to use:**
- Traditional SQL workloads
- ACID transactions
- Complex queries with joins
- Existing SQL Server applications

**Example:**
```sql
-- Create database with zone redundancy
CREATE DATABASE MyDatabase
(
    EDITION = 'GeneralPurpose',
    SERVICE_OBJECTIVE = 'GP_Gen5_2',
    MAXSIZE = 100 GB,
    ZONE_REDUNDANT = ON
);

-- Auto-tuning
ALTER DATABASE SCOPED CONFIGURATION SET AUTOMATIC_TUNING = AUTO;

-- Query performance insights
SELECT 
    query_stats.query_hash,
    SUM(query_stats.total_worker_time) / SUM(query_stats.execution_count) AS avg_cpu_time,
    MIN(query_stats.statement_text) AS query_text
FROM sys.dm_exec_query_stats AS query_stats
CROSS APPLY sys.dm_exec_sql_text(query_stats.sql_handle) AS sql_text
GROUP BY query_stats.query_hash
ORDER BY avg_cpu_time DESC;
```

**Serverless option:**
```bash
az sql db create \
  --resource-group myRG \
  --server myserver \
  --name mydb \
  --edition GeneralPurpose \
  --compute-model Serverless \
  --family Gen5 \
  --min-capacity 0.5 \
  --max-capacity 4 \
  --auto-pause-delay 60  # Auto-pause after 60 minutes
```

**Features:**
- Automatic backups (35 days)
- Point-in-time restore
- Geo-replication
- Elastic pools (share resources)
- Built-in intelligence

---

**2. Cosmos DB (Multi-model NoSQL):**

**When to use:**
- Global distribution
- Low latency (<10ms)
- Flexible schema
- Massive scale

**APIs supported:**
- **SQL API** (Document)
- **MongoDB API**
- **Cassandra API**
- **Gremlin API** (Graph)
- **Table API**

**Example:**
```csharp
using Microsoft.Azure.Cosmos;

// Create client
CosmosClient client = new CosmosClient(endpoint, key);
Database database = await client.CreateDatabaseIfNotExistsAsync("ecommerce");

// Create container with partition key
Container container = await database.CreateContainerIfNotExistsAsync(
    id: "orders",
    partitionKeyPath: "/customerId",
    throughput: 400  // RU/s
);

// Insert document
var order = new {
    id = Guid.NewGuid().ToString(),
    customerId = "customer123",
    orderDate = DateTime.UtcNow,
    items = new[] {
        new { productId = "prod1", quantity = 2, price = 29.99 },
        new { productId = "prod2", quantity = 1, price = 49.99 }
    },
    total = 109.97
};

await container.CreateItemAsync(order, new PartitionKey("customer123"));

// Query with SQL syntax
var query = "SELECT * FROM c WHERE c.customerId = @customerId AND c.total > @amount";
var queryDefinition = new QueryDefinition(query)
    .WithParameter("@customerId", "customer123")
    .WithParameter("@amount", 100);

using FeedIterator<dynamic> iterator = container.GetItemQueryIterator<dynamic>(queryDefinition);
while (iterator.HasMoreResults)
{
    foreach (var item in await iterator.ReadNextAsync())
    {
        Console.WriteLine(item);
    }
}
```

**Global Distribution:**
```bash
# Add read regions
az cosmosdb update \
  --name mycosmosdb \
  --resource-group myRG \
  --locations regionName=eastus failoverPriority=0 \
  --locations regionName=westus failoverPriority=1 \
  --locations regionName=northeurope failoverPriority=2
```

**Consistency Levels:**
1. **Strong**: Linearizability (highest latency)
2. **Bounded Staleness**: Configurable lag
3. **Session**: Within same session
4. **Consistent Prefix**: Reads never see out-of-order writes
5. **Eventual**: Lowest latency

---

**3. Azure Database for PostgreSQL:**

**When to use:**
- Open-source preference
- Need PostgreSQL features (JSONB, full-text search)
- Complex queries
- Existing PostgreSQL applications

**Deployment options:**
- **Single Server**: Basic option
- **Flexible Server**: Production workloads
- **Hyperscale (Citus)**: Distributed PostgreSQL

**Example:**
```bash
# Create Flexible Server
az postgres flexible-server create \
  --resource-group myRG \
  --name mypostgres \
  --location eastus \
  --admin-user myadmin \
  --admin-password <password> \
  --sku-name Standard_D2s_v3 \
  --tier GeneralPurpose \
  --storage-size 128 \
  --version 14

# Enable high availability
az postgres flexible-server update \
  --resource-group myRG \
  --name mypostgres \
  --high-availability Enabled \
  --standby-availability-zone 2
```

**PostgreSQL Features:**
```sql
-- JSONB for flexible schema
CREATE TABLE products (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100),
    attributes JSONB
);

INSERT INTO products (name, attributes) VALUES
('Laptop', '{"brand": "Dell", "ram": "16GB", "storage": "512GB"}'),
('Phone', '{"brand": "Apple", "model": "iPhone 14", "color": "black"}');

-- Query JSONB
SELECT * FROM products
WHERE attributes->>'brand' = 'Dell'
  AND (attributes->>'ram')::text LIKE '%16%';

-- Full-text search
CREATE INDEX idx_products_search ON products
USING GIN (to_tsvector('english', name || ' ' || attributes::text));

SELECT * FROM products
WHERE to_tsvector('english', name || ' ' || attributes::text) @@ to_tsquery('Dell & 16GB');

-- Array types
CREATE TABLE orders (
    id SERIAL PRIMARY KEY,
    product_ids INT[],
    tags TEXT[]
);

INSERT INTO orders (product_ids, tags) VALUES
(ARRAY[1, 2, 3], ARRAY['urgent', 'priority']);

SELECT * FROM orders
WHERE 2 = ANY(product_ids)
  AND 'urgent' = ANY(tags);
```

---

**Comparison:**

| Database | Type | Scale | Global | Use Case | Cost |
|----------|------|-------|--------|----------|------|
| Azure SQL | SQL | High | Read replicas | OLTP | Medium |
| Cosmos DB | NoSQL | Unlimited | Multi-master | Global apps | High |
| PostgreSQL | SQL | Very High | Read replicas | Open source | Low-Medium |

**Decision Tree:**
```
Need SQL?
├─ Yes → Need Azure features?
│         ├─ Yes → Azure SQL
│         └─ No → PostgreSQL
└─ No → Need global distribution?
          ├─ Yes → Cosmos DB
          └─ No → Table Storage
```

---

## Networking & Security

### Q6: Explain Virtual Networks, NSGs, Application Gateway, and how to design secure network architecture.

**Answer:**

**Network Architecture:**

```
Internet
    ↓
Azure Front Door (Global)
    ↓
Application Gateway (Regional, WAF)
    ↓
Virtual Network (10.0.0.0/16)
├── Frontend Subnet (10.0.1.0/24)
│   ├── NSG: Allow 80, 443
│   └── VMSS (Web Servers)
├── Backend Subnet (10.0.2.0/24)
│   ├── NSG: Allow 3000 from Frontend
│   └── VMSS (App Servers)
├── Database Subnet (10.0.3.0/24)
│   ├── NSG: Allow 5432 from Backend
│   └── Azure SQL (Private Endpoint)
└── Management Subnet (10.0.4.0/24)
    ├── NSG: Allow 22, 3389 from Corporate IP
    └── Bastion Host
```

**1. Virtual Network (VNet):**
```bicep
resource vnet 'Microsoft.Network/virtualNetworks@2021-05-01' = {
  name: 'myVNet'
  location: location
  properties: {
    addressSpace: {
      addressPrefixes: ['10.0.0.0/16']
    }
    subnets: [
      {
        name: 'frontend'
        properties: {
          addressPrefix: '10.0.1.0/24'
          networkSecurityGroup: {
            id: frontendNSG.id
          }
        }
      }
      {
        name: 'backend'
        properties: {
          addressPrefix: '10.0.2.0/24'
          networkSecurityGroup: {
            id: backendNSG.id
          }
          serviceEndpoints: [
            {
              service: 'Microsoft.Sql'
            }
          ]
        }
      }
      {
        name: 'database'
        properties: {
          addressPrefix: '10.0.3.0/24'
          privateEndpointNetworkPolicies: 'Disabled'
        }
      }
    ]
  }
}
```

---

**2. Network Security Groups (NSGs):**
```bicep
// Frontend NSG
resource frontendNSG 'Microsoft.Network/networkSecurityGroups@2021-05-01' = {
  name: 'frontend-nsg'
  location: location
  properties: {
    securityRules: [
      {
        name: 'AllowHTTPS'
        properties: {
          priority: 100
          direction: 'Inbound'
          access: 'Allow'
          protocol: 'Tcp'
          sourcePortRange: '*'
          destinationPortRange: '443'
          sourceAddressPrefix: 'Internet'
          destinationAddressPrefix: '*'
        }
      }
      {
        name: 'DenyAllInbound'
        properties: {
          priority: 4096
          direction: 'Inbound'
          access: 'Deny'
          protocol: '*'
          sourcePortRange: '*'
          destinationPortRange: '*'
          sourceAddressPrefix: '*'
          destinationAddressPrefix: '*'
        }
      }
    ]
  }
}

// Backend NSG
resource backendNSG 'Microsoft.Network/networkSecurityGroups@2021-05-01' = {
  name: 'backend-nsg'
  location: location
  properties: {
    securityRules: [
      {
        name: 'AllowFrontend'
        properties: {
          priority: 100
          direction: 'Inbound'
          access: 'Allow'
          protocol: 'Tcp'
          sourcePortRange: '*'
          destinationPortRange: '3000'
          sourceAddressPrefix: '10.0.1.0/24'  // Frontend subnet
          destinationAddressPrefix: '*'
        }
      }
    ]
  }
}
```

---

**3. Application Gateway (Layer 7 Load Balancer + WAF):**
```bicep
resource appGateway 'Microsoft.Network/applicationGateways@2021-05-01' = {
  name: 'myAppGateway'
  location: location
  properties: {
    sku: {
      name: 'WAF_v2'
      tier: 'WAF_v2'
      capacity: 2
    }
    gatewayIPConfigurations: [
      {
        name: 'appGatewayIpConfig'
        properties: {
          subnet: {
            id: frontendSubnet.id
          }
        }
      }
    ]
    frontendIPConfigurations: [
      {
        name: 'appGatewayFrontendIP'
        properties: {
          publicIPAddress: {
            id: publicIP.id
          }
        }
      }
    ]
    frontendPorts: [
      {
        name: 'port80'
        properties: {
          port: 80
        }
      }
      {
        name: 'port443'
        properties: {
          port: 443
        }
      }
    ]
    backendAddressPools: [
      {
        name: 'backendPool'
        properties: {
          backendAddresses: [
            {
              ipAddress: '10.0.1.10'
            }
          ]
        }
      }
    ]
    httpListeners: [
      {
        name: 'httpListener'
        properties: {
          frontendIPConfiguration: {
            id: resourceId('Microsoft.Network/applicationGateways/frontendIPConfigurations', 'myAppGateway', 'appGatewayFrontendIP')
          }
          frontendPort: {
            id: resourceId('Microsoft.Network/applicationGateways/frontendPorts', 'myAppGateway', 'port443')
          }
          protocol: 'Https'
          sslCertificate: {
            id: resourceId('Microsoft.Network/applicationGateways/sslCertificates', 'myAppGateway', 'appGatewaySslCert')
          }
        }
      }
    ]
    webApplicationFirewallConfiguration: {
      enabled: true
      firewallMode: 'Prevention'
      ruleSetType: 'OWASP'
      ruleSetVersion: '3.2'
    }
  }
}
```

---

**4. Azure Bastion (Secure RDP/SSH):**
```bicep
resource bastion 'Microsoft.Network/bastionHosts@2021-05-01' = {
  name: 'myBastion'
  location: location
  properties: {
    ipConfigurations: [
      {
        name: 'bastionConfig'
        properties: {
          subnet: {
            id: '${vnet.id}/subnets/AzureBastionSubnet'
          }
          publicIPAddress: {
            id: bastionPublicIP.id
          }
        }
      }
    ]
  }
}
```

---

**5. Private Endpoints (Access Azure services privately):**
```bash
# Create private endpoint for SQL
az network private-endpoint create \
  --name sqlPrivateEndpoint \
  --resource-group myRG \
  --vnet-name myVNet \
  --subnet database \
  --private-connection-resource-id /subscriptions/.../Microsoft.Sql/servers/myserver \
  --group-id sqlServer \
  --connection-name sqlConnection

# Create private DNS zone
az network private-dns zone create \
  --resource-group myRG \
  --name privatelink.database.windows.net

# Link to VNet
az network private-dns link vnet create \
  --resource-group myRG \
  --zone-name privatelink.database.windows.net \
  --name DNSLink \
  --virtual-network myVNet \
  --registration-enabled false
```

---

**6. Azure Firewall (Network + Application rules):**
```bicep
resource firewall 'Microsoft.Network/azureFirewalls@2021-05-01' = {
  name: 'myFirewall'
  location: location
  properties: {
    sku: {
      name: 'AZFW_VNet'
      tier: 'Standard'
    }
    ipConfigurations: [
      {
        name: 'firewallConfig'
        properties: {
          subnet: {
            id: '${vnet.id}/subnets/AzureFirewallSubnet'
          }
          publicIPAddress: {
            id: firewallPublicIP.id
          }
        }
      }
    ]
    networkRuleCollections: [
      {
        name: 'networkRules'
        properties: {
          priority: 100
          action: {
            type: 'Allow'
          }
          rules: [
            {
              name: 'allowDNS'
              protocols: ['UDP']
              sourceAddresses: ['*']
              destinationAddresses: ['*']
              destinationPorts: ['53']
            }
          ]
        }
      }
    ]
    applicationRuleCollections: [
      {
        name: 'appRules'
        properties: {
          priority: 100
          action: {
            type: 'Allow'
          }
          rules: [
            {
              name: 'allowAzure'
              protocols: [
                {
                  protocolType: 'Https'
                  port: 443
                }
              ]
              targetFqdns: ['*.azure.com', '*.microsoft.com']
              sourceAddresses: ['10.0.0.0/16']
            }
          ]
        }
      }
    ]
  }
}
```

---

## Identity & Access Management

### Q7: Explain Azure AD, RBAC, Managed Identities, and how to implement secure authentication.

**Answer:**

**1. Azure Active Directory (Azure AD):**

**Authentication Methods:**
```csharp
// Microsoft Identity Platform (MSAL)
using Microsoft.Identity.Client;

// Create confidential client (backend)
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder
    .Create(clientId)
    .WithClientSecret(clientSecret)
    .WithAuthority(new Uri($"https://login.microsoftonline.com/{tenantId}"))
    .Build();

// Acquire token
string[] scopes = new string[] { "https://graph.microsoft.com/.default" };
AuthenticationResult result = await app.AcquireTokenForClient(scopes)
    .ExecuteAsync();

// Call API with token
HttpClient client = new HttpClient();
client.DefaultRequestHeaders.Authorization = 
    new AuthenticationHeaderValue("Bearer", result.AccessToken);

var response = await client.GetAsync("https://graph.microsoft.com/v1.0/users");
```

**Multi-Factor Authentication:**
```powershell
# Enable MFA for all users
Connect-MsolService

$mfaUsers = Get-MsolUser -All | Where-Object {$_.IsLicensed -eq $true}

foreach ($user in $mfaUsers) {
    $auth = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
    $auth.RelyingParty = "*"
    $auth.State = "Enabled"
    $user.StrongAuthenticationRequirements = $auth
    
    Set-MsolUser -UserPrincipalName $user.UserPrincipalName `
                  -StrongAuthenticationRequirements $auth
}
```

---

**2. Role-Based Access Control (RBAC):**

**Built-in roles:**
- **Owner**: Full access
- **Contributor**: Manage resources, can't grant access
- **Reader**: View only
- **User Access Administrator**: Manage access only

**Custom Role:**
```json
{
  "Name": "Custom Deployment Role",
  "Description": "Can deploy resources but not delete",
  "Actions": [
    "Microsoft.Resources/deployments/*",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Compute/virtualMachines/read",
    "Microsoft.Compute/virtualMachines/write",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action"
  ],
  "NotActions": [
    "Microsoft.Compute/virtualMachines/delete"
  ],
  "AssignableScopes": [
    "/subscriptions/{subscription-id}"
  ]
}
```

```bash
# Create custom role
az role definition create --role-definition custom-role.json

# Assign role
az role assignment create \
  --assignee user@company.com \
  --role "Custom Deployment Role" \
  --scope /subscriptions/{sub-id}/resourceGroups/myRG
```

---

**3. Managed Identities (No credentials in code!):**

**System-Assigned Identity:**
```bash
# Enable for App Service
az webapp identity assign \
  --resource-group myRG \
  --name myWebApp

# Grant access to Key Vault
az keyvault set-policy \
  --name myKeyVault \
  --object-id <managed-identity-id> \
  --secret-permissions get list
```

**User-Assigned Identity:**
```bash
# Create identity
az identity create \
  --resource-group myRG \
  --name myUserManagedIdentity

# Assign to multiple resources
az vm identity assign \
  --resource-group myRG \
  --name myVM \
  --identities /subscriptions/.../myUserManagedIdentity
```

**Using Managed Identity in Code:**
```csharp
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
using Azure.Storage.Blobs;

// Key Vault
var keyVaultClient = new SecretClient(
    new Uri("https://mykeyvault.vault.azure.net/"),
    new DefaultAzureCredential()  // Uses managed identity!
);

KeyVaultSecret secret = await keyVaultClient.GetSecretAsync("DatabasePassword");
string password = secret.Value;

// Storage
var blobServiceClient = new BlobServiceClient(
    new Uri("https://mystorageaccount.blob.core.windows.net"),
    new DefaultAzureCredential()  // No connection string needed!
);
```

---

**4. Azure Key Vault:**
```bash
# Create Key Vault
az keyvault create \
  --name myKeyVault \
  --resource-group myRG \
  --location eastus \
  --enable-soft-delete true \
  --enable-purge-protection true

# Store secret
az keyvault secret set \
  --vault-name myKeyVault \
  --name DatabasePassword \
  --value "SuperSecretPassword123!"

# Use in App Service
az webapp config appsettings set \
  --resource-group myRG \
  --name myWebApp \
  --settings DATABASE_PASSWORD="@Microsoft.KeyVault(SecretUri=https://mykeyvault.vault.azure.net/secrets/DatabasePassword)"
```

---

**5. Conditional Access:**
```powershell
# Require MFA for risky sign-ins
New-AzureADMSConditionalAccessPolicy `
  -DisplayName "Require MFA for Risky Sign-ins" `
  -State "Enabled" `
  -Conditions @{
    SignInRiskLevels = @("high", "medium")
    Users = @{
      IncludeUsers = "All"
    }
    Applications = @{
      IncludeApplications = "All"
    }
  } `
  -GrantControls @{
    BuiltInControls = @("mfa")
    Operator = "OR"
  }
```

---

## DevOps & Deployment

### Q8: How do you implement CI/CD with Azure DevOps?

**Answer:**

**Azure DevOps Pipeline Architecture:**

```
GitHub/Azure Repos → Azure Pipelines → Build → Test → Release
                                           ↓
                                    Azure Container Registry
                                           ↓
                    ┌──────────────────────┴───────────────────┐
                    ↓                                          ↓
              Dev Environment                          Prod Environment
              (App Service)                            (AKS)
```

**Example: Multi-stage Pipeline**

```yaml
# azure-pipelines.yml

trigger:
  branches:
    include:
      - main
      - develop

variables:
  azureSubscription: 'Azure-Subscription'
  containerRegistry: 'myregistry.azurecr.io'
  imageName: 'myapp'
  kubernetesCluster: 'myAKSCluster'
  resourceGroup: 'myRG'

stages:
  # Build Stage
  - stage: Build
    displayName: 'Build and Push'
    jobs:
      - job: BuildJob
        pool:
          vmImage: 'ubuntu-latest'
        steps:
          - task: Docker@2
            displayName: 'Build Docker Image'
            inputs:
              command: 'build'
              repository: '$(containerRegistry)/$(imageName)'
              dockerfile: 'Dockerfile'
              tags: |
                $(Build.BuildId)
                latest

          - task: Docker@2
            displayName: 'Push to ACR'
            inputs:
              command: 'push'
              repository: '$(containerRegistry)/$(imageName)'
              tags: |
                $(Build.BuildId)
                latest

  # Test Stage
  - stage: Test
    displayName: 'Run Tests'
    dependsOn: Build
    jobs:
      - job: TestJob
        pool:
          vmImage: 'ubuntu-latest'
        steps:
          - script: |
              npm install
              npm test
            displayName: 'Unit Tests'

          - script: |
              npm run test:integration
            displayName: 'Integration Tests'

          - task: PublishTestResults@2
            inputs:
              testResultsFormat: 'JUnit'
              testResultsFiles: '**/test-results.xml'

  # Deploy to Dev
  - stage: DeployDev
    displayName: 'Deploy to Dev'
    dependsOn: Test
    condition: and(succeeded(), eq(variables['Build.SourceBranch'], 'refs/heads/develop'))
    jobs:
      - deployment: DeployDevJob
        environment: 'Development'
        pool:
          vmImage: 'ubuntu-latest'
        strategy:
          runOnce:
            deploy:
              steps:
                - task: AzureWebAppContainer@1
                  displayName: 'Deploy to Dev App Service'
                  inputs:
                    azureSubscription: '$(azureSubscription)'
                    appName: 'myapp-dev'
                    containers: '$(containerRegistry)/$(imageName):$(Build.BuildId)'

  # Deploy to Prod (with approval)
  - stage: DeployProd
    displayName: 'Deploy to Production'
    dependsOn: Test
    condition: and(succeeded(), eq(variables['Build.SourceBranch'], 'refs/heads/main'))
    jobs:
      - deployment: DeployProdJob
        environment: 'Production'  # Requires manual approval in Azure DevOps
        pool:
          vmImage: 'ubuntu-latest'
        strategy:
          runOnce:
            deploy:
              steps:
                - task: Kubernetes@1
                  displayName: 'Deploy to AKS'
                  inputs:
                    connectionType: 'Azure Resource Manager'
                    azureSubscriptionEndpoint: '$(azureSubscription)'
                    azureResourceGroup: '$(resourceGroup)'
                    kubernetesCluster: '$(kubernetesCluster)'
                    command: 'apply'
                    useConfigurationFile: true
                    configuration: 'k8s/deployment.yaml'
                    arguments: '--set image=$(containerRegistry)/$(imageName):$(Build.BuildId)'

                - script: |
                    kubectl rollout status deployment/myapp -n production
                  displayName: 'Check Deployment Status'
```

**Kubernetes Deployment:**
```yaml
# k8s/deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
  namespace: production
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 0
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: myapp
        image: myregistry.azurecr.io/myapp:latest  # Replaced by pipeline
        ports:
        - containerPort: 3000
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 500m
            memory: 512Mi
        livenessProbe:
          httpGet:
            path: /health
            port: 3000
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /ready
            port: 3000
          initialDelaySeconds: 5
          periodSeconds: 5
---
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
  namespace: production
spec:
  type: LoadBalancer
  ports:
  - port: 80
    targetPort: 3000
  selector:
    app: myapp
```

---

## Monitoring & Governance

### Q9: How do you implement monitoring, logging, and cost management in Azure?

**Answer:**

**1. Azure Monitor:**

```csharp
// Application Insights
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;

public class MyService
{
    private readonly TelemetryClient _telemetry;

    public MyService(TelemetryClient telemetry)
    {
        _telemetry = telemetry;
    }

    public async Task<User> GetUser(string id)
    {
        var operation = _telemetry.StartOperation<RequestTelemetry>("GetUser");
        operation.Telemetry.Properties["userId"] = id;

        try
        {
            // Track custom event
            _telemetry.TrackEvent("UserRequested", new Dictionary<string, string>
            {
                { "userId", id },
                { "timestamp", DateTime.UtcNow.ToString() }
            });

            var user = await _userRepository.GetUserAsync(id);

            // Track metric
            _telemetry.TrackMetric("UserLoadTime", operation.Telemetry.Duration.TotalMilliseconds);

            return user;
        }
        catch (Exception ex)
        {
            // Track exception
            _telemetry.TrackException(ex);
            operation.Telemetry.Success = false;
            throw;
        }
        finally
        {
            _telemetry.StopOperation(operation);
        }
    }
}
```

**2. Log Analytics (KQL Queries):**

```kql
// Find errors in last 24 hours
traces
| where timestamp > ago(24h)
| where severityLevel >= 3
| summarize count() by cloud_RoleName, message
| order by count_ desc

// Performance analysis
requests
| where timestamp > ago(1h)
| summarize 
    avg(duration), 
    percentile(duration, 95),
    percentile(duration, 99)
    by operation_Name
| order by avg_duration desc

// Track exceptions
exceptions
| where timestamp > ago(7d)
| summarize count() by type, outerMessage
| top 10 by count_

// Custom metrics
customMetrics
| where name == "UserLoadTime"
| summarize avg(value), max(value), min(value) by bin(timestamp, 1h)
| render timechart
```

**3. Alerts:**

```bash
# Create metric alert
az monitor metrics alert create \
  --name "High CPU Alert" \
  --resource-group myRG \
  --scopes /subscriptions/.../Microsoft.Compute/virtualMachines/myVM \
  --condition "avg Percentage CPU > 80" \
  --window-size 5m \
  --evaluation-frequency 1m \
  --action /subscriptions/.../actionGroups/myActionGroup

# Create log alert
az monitor scheduled-query create \
  --name "Error Rate Alert" \
  --resource-group myRG \
  --scopes /subscriptions/.../Microsoft.Insights/components/myAppInsights \
  --condition "count > 10" \
  --condition-query "traces | where severityLevel >= 3 | count" \
  --window-size 5m \
  --evaluation-frequency 5m
```

**4. Cost Management:**

```powershell
# Get cost by resource group
Get-AzConsumptionUsageDetail -StartDate "2026-01-01" -EndDate "2026-01-31" |
  Group-Object ResourceGroup |
  Select-Object Name, @{Name="TotalCost";Expression={($_.Group | Measure-Object -Property PretaxCost -Sum).Sum}} |
  Sort-Object TotalCost -Descending

# Create budget
New-AzConsumptionBudget `
  -Name "Monthly-Budget" `
  -Amount 1000 `
  -Category Cost `
  -TimeGrain Monthly `
  -StartDate "2026-01-01" `
  -Notification @{
    Enabled = $true
    Threshold = 80
    ContactEmails = @("team@company.com")
  }
```

**5. Azure Policy (Governance):**

```json
{
  "policyRule": {
    "if": {
      "allOf": [
        {
          "field": "type",
          "equals": "Microsoft.Storage/storageAccounts"
        },
        {
          "field": "Microsoft.Storage/storageAccounts/enableHttpsTrafficOnly",
          "equals": "false"
        }
      ]
    },
    "then": {
      "effect": "deny"
    }
  }
}
```

```bash
# Assign policy
az policy assignment create \
  --name "EnforceHTTPS" \
  --policy "EnforceHTTPSStoragePolicy" \
  --scope /subscriptions/{subscription-id}/resourceGroups/myRG
```

---

## Summary

**Azure Best Practices:**

1. **Organization**: Management Groups → Subscriptions → Resource Groups
2. **High Availability**: Availability Zones, multi-region deployment
3. **Compute**: Choose right service (VMs, App Service, AKS, Functions)
4. **Storage**: Match requirements (Blob, Files, Queue, Table, Disk)
5. **Databases**: SQL for relational, Cosmos DB for global NoSQL
6. **Networking**: Secure with NSGs, Application Gateway, Private Endpoints
7. **Security**: Azure AD, RBAC, Managed Identities, Key Vault
8. **DevOps**: Azure Pipelines for CI/CD automation
9. **Monitoring**: Application Insights, Log Analytics, Alerts
10. **Governance**: Azure Policy, Cost Management, Tagging

**Key Differences from AWS:**

| Feature | AWS | Azure |
|---------|-----|-------|
| Regions | Regions | Regions + Availability Zones |
| VM | EC2 | Virtual Machines |
| Serverless | Lambda | Functions |
| Container Orchestration | EKS | AKS |
| Object Storage | S3 | Blob Storage |
| Relational DB | RDS | Azure SQL |
| NoSQL | DynamoDB | Cosmos DB |
| Identity | IAM | Azure AD + RBAC |
| CI/CD | CodePipeline | Azure Pipelines |

**Remember:** Azure has 200+ services. Master core services first, then expand based on your project needs!
