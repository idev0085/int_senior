# Jenkins - Senior Developer to Architect Level Q&A

## Table of Contents
1. [Core Concepts & Architecture](#core-concepts--architecture)
2. [Pipeline as Code](#pipeline-as-code)
3. [Plugins & Integrations](#plugins--integrations)
4. [Security & Access Control](#security--access-control)
5. [Scalability & Performance](#scalability--performance)
6. [Best Practices](#best-practices)

---

## Core Concepts & Architecture

### Q1: Explain Jenkins architecture. How does Master-Agent configuration work?

**Answer:**

**Simple Analogy:**
Jenkins Master is like a project manager who assigns work, while Agents are workers who do the actual tasks.

**Architecture:**

```
Jenkins Master (Controller)
    ├── Schedules builds
    ├── Monitors agents
    ├── Stores configurations
    ├── Serves UI
    └── Distributes jobs to agents

Jenkins Agents (Workers)
    ├── Agent 1 (Linux)
    ├── Agent 2 (Windows)
    ├── Agent 3 (Docker)
    └── Agent 4 (macOS)
```

**Master Responsibilities:**
- Schedule build jobs
- Dispatch builds to agents
- Monitor agents
- Record and present build results
- Serve Jenkins UI

**Agent Responsibilities:**
- Execute build jobs
- Report back to master
- Can be permanent or ephemeral (Docker, Kubernetes)

**Communication:**
```
Master ←→ Agent (via JNLP or SSH)
```

**Setup Agent:**

**1. Via SSH:**
```groovy
// Jenkins UI: Manage Jenkins → Manage Nodes → New Node

Node Configuration:
- Name: linux-agent-1
- Remote root directory: /home/jenkins
- Labels: linux docker
- Usage: Use this node as much as possible
- Launch method: Launch agents via SSH
  - Host: agent1.mycompany.com
  - Credentials: jenkins-ssh-key
```

**2. Via Docker:**
```yaml
# docker-compose.yml
version: '3'
services:
  jenkins-master:
    image: jenkins/jenkins:lts
    ports:
      - "8080:8080"
      - "50000:50000"
    volumes:
      - jenkins_home:/var/jenkins_home
  
  jenkins-agent:
    image: jenkins/inbound-agent
    environment:
      - JENKINS_URL=http://jenkins-master:8080
      - JENKINS_SECRET=${AGENT_SECRET}
      - JENKINS_AGENT_NAME=docker-agent

volumes:
  jenkins_home:
```

**3. Kubernetes Agent (Dynamic):**
```groovy
podTemplate(
  label: 'k8s-agent',
  containers: [
    containerTemplate(
      name: 'maven',
      image: 'maven:3.8-jdk-11',
      command: 'sleep',
      args: '99d'
    ),
    containerTemplate(
      name: 'docker',
      image: 'docker:latest',
      command: 'sleep',
      args: '99d'
    )
  ],
  volumes: [
    hostPathVolume(hostPath: '/var/run/docker.sock', mountPath: '/var/run/docker.sock')
  ]
) {
  node('k8s-agent') {
    stage('Build') {
      container('maven') {
        sh 'mvn clean package'
      }
    }
    stage('Docker Build') {
      container('docker') {
        sh 'docker build -t myapp .'
      }
    }
  }
}
```

---

### Q2: What are the different types of Jenkins jobs and when should you use each?

**Answer:**

**1. Freestyle Project (Simple, GUI-based):**

**When to use:**
- Simple build tasks
- Legacy projects
- Teams not familiar with code

**Example Configuration:**
```
Source Code Management: Git
  Repository URL: https://github.com/user/repo.git
  Branch: */main

Build Triggers:
  ☑ Poll SCM: H/5 * * * *

Build Environment:
  ☑ Delete workspace before build starts

Build:
  Execute Shell:
    npm install
    npm test
    npm run build

Post-build Actions:
  Archive artifacts: dist/**/*
  Publish JUnit test results: test-results/*.xml
```

---

**2. Pipeline (Code-based, Modern):**

**When to use:**
- Complex workflows
- Version-controlled pipelines
- Modern CI/CD practices

**Scripted Pipeline:**
```groovy
node {
    stage('Checkout') {
        checkout scm
    }
    
    stage('Build') {
        sh 'npm install'
        sh 'npm run build'
    }
    
    stage('Test') {
        sh 'npm test'
        junit 'test-results/*.xml'
    }
    
    stage('Deploy') {
        if (env.BRANCH_NAME == 'main') {
            sh './deploy.sh'
        }
    }
}
```

**Declarative Pipeline:**
```groovy
pipeline {
    agent any
    
    environment {
        NODE_ENV = 'production'
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Build') {
            steps {
                sh 'npm install'
                sh 'npm run build'
            }
        }
        
        stage('Test') {
            steps {
                sh 'npm test'
            }
            post {
                always {
                    junit 'test-results/*.xml'
                }
            }
        }
        
        stage('Deploy') {
            when {
                branch 'main'
            }
            steps {
                sh './deploy.sh'
            }
        }
    }
    
    post {
        success {
            slackSend(color: 'good', message: "Build Successful: ${env.JOB_NAME} ${env.BUILD_NUMBER}")
        }
        failure {
            slackSend(color: 'danger', message: "Build Failed: ${env.JOB_NAME} ${env.BUILD_NUMBER}")
        }
    }
}
```

---

**3. Multibranch Pipeline:**

**When to use:**
- Multiple branches (feature branches, PR builds)
- Automatic discovery of new branches

**Jenkinsfile in repo:**
```groovy
pipeline {
    agent any
    
    stages {
        stage('Build') {
            steps {
                echo "Building branch: ${env.BRANCH_NAME}"
                sh 'npm install'
                sh 'npm run build'
            }
        }
        
        stage('Test') {
            steps {
                sh 'npm test'
            }
        }
        
        stage('Deploy to Dev') {
            when {
                branch 'develop'
            }
            steps {
                sh './deploy-dev.sh'
            }
        }
        
        stage('Deploy to Production') {
            when {
                branch 'main'
            }
            steps {
                input message: 'Deploy to production?', ok: 'Deploy'
                sh './deploy-prod.sh'
            }
        }
    }
}
```

**Configuration:**
```
Branch Sources: GitHub
  Repository: https://github.com/user/repo
  Credentials: github-token
  Behaviors:
    - Discover branches
    - Discover pull requests
Build Configuration:
  Mode: by Jenkinsfile
  Script Path: Jenkinsfile
Scan Repository Triggers:
  Periodically: 1 hour
```

---

**4. Organization Folder:**

**When to use:**
- Multiple repositories in organization
- Automatic discovery of new repos

---

**Comparison:**

| Job Type | Complexity | Version Control | Multi-Branch | Best For |
|----------|------------|-----------------|--------------|----------|
| Freestyle | Low | No | No | Simple builds |
| Pipeline | Medium | Yes | No | Single-branch projects |
| Multibranch | Medium-High | Yes | Yes | Feature branch workflow |
| Organization | High | Yes | Yes | Multiple repos |

---

## Pipeline as Code

### Q3: What are the differences between Scripted and Declarative pipelines? Which should you use?

**Answer:**

**Declarative Pipeline (Recommended):**

**Pros:**
- Easier to read and write
- Built-in syntax validation
- Better error messages
- Enforces best practices
- Restart from stage

**Cons:**
- Less flexible
- Limited to predefined structure

**Example:**
```groovy
pipeline {
    agent {
        docker {
            image 'node:18'
            label 'docker-agent'
        }
    }
    
    environment {
        APP_VERSION = '1.0.0'
        DEPLOY_ENV = 'production'
    }
    
    parameters {
        choice(name: 'ENVIRONMENT', choices: ['dev', 'staging', 'prod'], description: 'Deploy environment')
        booleanParam(name: 'RUN_TESTS', defaultValue: true, description: 'Run tests')
    }
    
    options {
        timeout(time: 1, unit: 'HOURS')
        timestamps()
        buildDiscarder(logRotator(numToKeepStr: '10'))
    }
    
    triggers {
        cron('H 2 * * *')  // Nightly build
        pollSCM('H/5 * * * *')  // Poll every 5 minutes
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Build') {
            steps {
                sh 'npm install'
                sh 'npm run build'
            }
        }
        
        stage('Test') {
            when {
                expression { params.RUN_TESTS == true }
            }
            steps {
                sh 'npm test'
            }
        }
        
        stage('Deploy') {
            when {
                branch 'main'
            }
            steps {
                sh "deploy.sh ${params.ENVIRONMENT}"
            }
        }
    }
    
    post {
        always {
            cleanWs()
        }
        success {
            echo 'Pipeline succeeded'
        }
        failure {
            echo 'Pipeline failed'
        }
    }
}
```

---

**Scripted Pipeline (Advanced):**

**Pros:**
- Maximum flexibility
- Full Groovy power
- Complex conditional logic

**Cons:**
- Harder to read
- More prone to errors
- No built-in structure

**Example:**
```groovy
node('docker-agent') {
    try {
        // Variables
        def buildNumber = env.BUILD_NUMBER
        def branch = env.BRANCH_NAME
        
        stage('Checkout') {
            checkout scm
        }
        
        stage('Build') {
            docker.image('node:18').inside {
                sh 'npm install'
                sh 'npm run build'
            }
        }
        
        stage('Test') {
            parallel(
                'Unit Tests': {
                    sh 'npm run test:unit'
                },
                'Integration Tests': {
                    sh 'npm run test:integration'
                },
                'E2E Tests': {
                    sh 'npm run test:e2e'
                }
            )
        }
        
        // Complex conditional logic
        if (branch == 'main') {
            stage('Deploy Production') {
                input message: 'Deploy to production?'
                sh './deploy-prod.sh'
            }
        } else if (branch.startsWith('feature/')) {
            stage('Deploy Dev') {
                sh './deploy-dev.sh'
            }
        }
        
        currentBuild.result = 'SUCCESS'
    } catch (Exception e) {
        currentBuild.result = 'FAILURE'
        throw e
    } finally {
        // Cleanup
        cleanWs()
        
        // Notifications
        if (currentBuild.result == 'FAILURE') {
            mail to: 'team@example.com',
                 subject: "Build Failed: ${env.JOB_NAME}",
                 body: "Check console output"
        }
    }
}
```

---

**When to use each:**

| Use Case | Declarative | Scripted |
|----------|-------------|----------|
| Standard CI/CD | ✅ | ❌ |
| Simple workflows | ✅ | ❌ |
| Complex logic | ❌ | ✅ |
| Dynamic stages | ❌ | ✅ |
| Team with varied skills | ✅ | ❌ |
| Maximum flexibility | ❌ | ✅ |

**Recommendation:** Start with Declarative, use Scripted only when needed.

---

### Q4: How do you implement parallel execution and matrix builds in Jenkins?

**Answer:**

**1. Parallel Stages:**

```groovy
pipeline {
    agent any
    
    stages {
        stage('Parallel Testing') {
            parallel {
                stage('Unit Tests') {
                    steps {
                        sh 'npm run test:unit'
                    }
                }
                
                stage('Integration Tests') {
                    steps {
                        sh 'npm run test:integration'
                    }
                }
                
                stage('E2E Tests') {
                    agent {
                        label 'e2e-agent'
                    }
                    steps {
                        sh 'npm run test:e2e'
                    }
                }
                
                stage('Security Scan') {
                    steps {
                        sh 'npm audit'
                    }
                }
            }
        }
    }
}
```

**Visualized:**
```
Checkout
   ↓
Build
   ↓
┌────────────────┬────────────────┬────────────────┬────────────────┐
│  Unit Tests    │  Integration   │   E2E Tests    │ Security Scan  │
│  (parallel)    │  (parallel)    │   (parallel)   │  (parallel)    │
└────────────────┴────────────────┴────────────────┴────────────────┘
   ↓
Deploy
```

---

**2. Matrix Builds (Multi-configuration):**

```groovy
pipeline {
    agent none
    
    stages {
        stage('Test Matrix') {
            matrix {
                agent {
                    label "${PLATFORM}"
                }
                axes {
                    axis {
                        name 'PLATFORM'
                        values 'linux', 'windows', 'mac'
                    }
                    axis {
                        name 'NODE_VERSION'
                        values '16', '18', '20'
                    }
                    axis {
                        name 'BROWSER'
                        values 'chrome', 'firefox', 'safari'
                    }
                }
                excludes {
                    exclude {
                        axis {
                            name 'PLATFORM'
                            values 'windows'
                        }
                        axis {
                            name 'BROWSER'
                            values 'safari'
                        }
                    }
                }
                stages {
                    stage('Build and Test') {
                        steps {
                            echo "Testing on ${PLATFORM} with Node ${NODE_VERSION} and ${BROWSER}"
                            sh "nvm use ${NODE_VERSION}"
                            sh "npm install"
                            sh "npm test -- --browser=${BROWSER}"
                        }
                    }
                }
            }
        }
    }
}
```

**Result:** 
Creates 27 combinations (3 platforms × 3 node versions × 3 browsers), minus exclusions

---

**3. Dynamic Parallel Execution:**

```groovy
pipeline {
    agent any
    
    stages {
        stage('Parallel Deploy') {
            steps {
                script {
                    def servers = ['server1', 'server2', 'server3', 'server4']
                    def parallelStages = [:]
                    
                    for (server in servers) {
                        // Capture variable for closure
                        def currentServer = server
                        
                        parallelStages[currentServer] = {
                            stage("Deploy to ${currentServer}") {
                                echo "Deploying to ${currentServer}"
                                sh "ssh ${currentServer} './deploy.sh'"
                            }
                        }
                    }
                    
                    parallel parallelStages
                }
            }
        }
    }
}
```

---

**4. Parallel with Different Agents:**

```groovy
pipeline {
    agent none
    
    stages {
        stage('Multi-Platform Build') {
            parallel {
                stage('Linux Build') {
                    agent { label 'linux' }
                    steps {
                        sh './build-linux.sh'
                        archiveArtifacts 'dist/linux/*'
                    }
                }
                
                stage('Windows Build') {
                    agent { label 'windows' }
                    steps {
                        bat 'build-windows.bat'
                        archiveArtifacts 'dist/windows/*'
                    }
                }
                
                stage('macOS Build') {
                    agent { label 'mac' }
                    steps {
                        sh './build-mac.sh'
                        archiveArtifacts 'dist/mac/*'
                    }
                }
            }
        }
    }
}
```

---

**5. Fail-Fast Behavior:**

```groovy
pipeline {
    agent any
    
    stages {
        stage('Parallel Tests') {
            failFast true  // Stop all parallel stages if one fails
            parallel {
                stage('Test Suite 1') {
                    steps {
                        sh 'npm run test:suite1'
                    }
                }
                stage('Test Suite 2') {
                    steps {
                        sh 'npm run test:suite2'
                    }
                }
            }
        }
    }
}
```

---

## Plugins & Integrations

### Q5: What are essential Jenkins plugins and how do you manage them?

**Answer:**

**Essential Plugins:**

**1. Pipeline Plugins:**
- Pipeline
- Pipeline: Stage View
- Blue Ocean (Modern UI)

**2. Source Control:**
- Git
- GitHub
- GitLab
- Bitbucket

**3. Build Tools:**
- NodeJS Plugin
- Maven Integration
- Gradle Plugin
- Docker Pipeline

**4. Testing & Quality:**
- JUnit Plugin
- Code Coverage API
- SonarQube Scanner
- HTML Publisher

**5. Deployment:**
- Kubernetes
- Amazon EC2
- Azure
- SSH Agent

**6. Notifications:**
- Email Extension
- Slack Notification
- Microsoft Teams

**7. Security:**
- Credentials Plugin
- Role-based Authorization
- OWASP Dependency-Check

---

**Managing Plugins:**

**1. Plugin Installation (UI):**
```
Manage Jenkins → Manage Plugins
- Available tab: Search and install
- Updates tab: Update existing
- Installed tab: View and uninstall
```

**2. Plugin Installation (Code):**
```groovy
// plugins.txt
pipeline:2.6
git:4.11.0
docker-workflow:1.28
kubernetes:3600.v144b_cd192ca_a_
slack:2.48
```

```bash
# Install from plugins.txt
jenkins-plugin-cli --plugin-file plugins.txt
```

**3. Configuration as Code (JCasC):**
```yaml
# jenkins.yaml
jenkins:
  systemMessage: "Jenkins configured automatically"
  
credentials:
  system:
    domainCredentials:
      - credentials:
          - usernamePassword:
              scope: GLOBAL
              id: "github-credentials"
              username: "jenkins-bot"
              password: "${GITHUB_TOKEN}"

tool:
  nodejs:
    installations:
      - name: "Node 18"
        properties:
          - installSource:
              installers:
                - nodeJSInstaller:
                    id: "18.16.0"

unclassified:
  slackNotifier:
    teamDomain: "myteam"
    tokenCredentialId: "slack-token"
```

**4. Shared Libraries:**
```groovy
// vars/buildNodeApp.groovy
def call(Map config) {
    pipeline {
        agent any
        stages {
            stage('Build') {
                steps {
                    sh "npm install"
                    sh "npm run build"
                }
            }
            stage('Test') {
                steps {
                    sh "npm test"
                }
            }
            stage('Deploy') {
                when {
                    branch 'main'
                }
                steps {
                    sh "deploy.sh ${config.environment}"
                }
            }
        }
    }
}

// Jenkinsfile
@Library('my-shared-library') _
buildNodeApp(environment: 'production')
```

---

## Security & Access Control

### Q6: How do you implement security best practices in Jenkins?

**Answer:**

**1. Authentication:**

```groovy
// Configure LDAP
jenkins:
  securityRealm:
    ldap:
      configurations:
        - server: "ldap.company.com"
          rootDN: "dc=company,dc=com"
          userSearchBase: "ou=users"

// Or use GitHub OAuth
jenkins:
  securityRealm:
    github:
      githubWebUri: "https://github.com"
      githubApiUri: "https://api.github.com"
      clientID: "${GITHUB_CLIENT_ID}"
      clientSecret: "${GITHUB_CLIENT_SECRET}"
```

---

**2. Authorization (Role-Based Access):**

```groovy
jenkins:
  authorizationStrategy:
    roleBased:
      roles:
        global:
          - name: "admin"
            description: "Jenkins administrators"
            permissions:
              - "Overall/Administer"
            assignments:
              - "admin-group"
          
          - name: "developer"
            description: "Developers"
            permissions:
              - "Overall/Read"
              - "Job/Build"
              - "Job/Read"
            assignments:
              - "dev-group"
          
          - name: "viewer"
            description: "Read-only access"
            permissions:
              - "Overall/Read"
              - "Job/Read"
            assignments:
              - "qa-group"
```

---

**3. Credentials Management:**

```groovy
pipeline {
    agent any
    
    environment {
        // Use Jenkins credentials
        AWS_CREDENTIALS = credentials('aws-credentials')
        DOCKER_CREDENTIALS = credentials('docker-hub')
        SSH_KEY = credentials('deploy-ssh-key')
    }
    
    stages {
        stage('Deploy') {
            steps {
                // AWS credentials automatically exported as:
                // AWS_CREDENTIALS_USR and AWS_CREDENTIALS_PSW
                sh '''
                    aws configure set aws_access_key_id $AWS_CREDENTIALS_USR
                    aws configure set aws_secret_access_key $AWS_CREDENTIALS_PSW
                    aws s3 sync dist/ s3://my-bucket
                '''
            }
        }
        
        stage('Docker Push') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker-hub') {
                        docker.image('myapp').push('latest')
                    }
                }
            }
        }
    }
}
```

**Credential Types:**
- Username/Password
- Secret Text
- Secret File
- SSH Key
- Certificate

---

**4. Script Security:**

```groovy
// Approved scripts (Manage Jenkins → In-process Script Approval)

// ❌ Dangerous - needs approval
@NonCPS
def readFile(path) {
    new File(path).text
}

// ✅ Safe - use built-in steps
def content = readFile('config.json')
```

---

**5. Agent Security:**

```groovy
// Limit what agents can do
pipeline {
    agent {
        kubernetes {
            yaml '''
apiVersion: v1
kind: Pod
spec:
  serviceAccountName: jenkins-limited  # Limited K8s permissions
  containers:
  - name: builder
    image: maven:3.8-jdk-11
    securityContext:
      runAsUser: 1000  # Non-root user
      runAsGroup: 1000
      allowPrivilegeEscalation: false
      readOnlyRootFilesystem: true
'''
        }
    }
}
```

---

**6. Audit Logging:**

```groovy
// Enable audit trail plugin
jenkins:
  auditTrail:
    logBuildCause: true
    pattern: ".*"
    loggers:
      - logFile:
          path: "/var/log/jenkins/audit.log"
          count: 30
```

---

**7. Security Headers:**

```groovy
// Configure security headers
jenkins:
  security:
    contentSecurityPolicy: "default-src 'self'; script-src 'self' 'unsafe-inline'"
    csrf:
      defaultCrumbIssuer:
        enabled: true
        proxyCompatibility: true
```

---

## Scalability & Performance

### Q7: How do you scale Jenkins for large organizations?

**Answer:**

**1. Master-Agent Architecture:**

```
                    Jenkins Master
                          |
      ┌───────────────────┼───────────────────┐
      |                   |                   |
  Agent Pool 1       Agent Pool 2       Agent Pool 3
  (Linux VMs)        (Docker)           (Kubernetes)
   - agent1           - agent5           - dynamic pods
   - agent2           - agent6
   - agent3           - agent7
   - agent4
```

**2. Kubernetes Agents (Dynamic Scaling):**

```groovy
// Configure Kubernetes cloud
jenkins:
  clouds:
    - kubernetes:
        name: "kubernetes"
        serverUrl: "https://kubernetes.default"
        namespace: "jenkins"
        jenkinsUrl: "http://jenkins:8080"
        jenkinsTunnel: "jenkins:50000"
        templates:
          - name: "maven-agent"
            label: "maven"
            nodeUsageMode: EXCLUSIVE
            containers:
              - name: "maven"
                image: "maven:3.8-jdk-11"
                command: "cat"
                tty: true
                resourceRequestCpu: "500m"
                resourceRequestMemory: "1Gi"
                resourceLimitCpu: "2"
                resourceLimitMemory: "4Gi"
```

**Pipeline using K8s agents:**
```groovy
pipeline {
    agent {
        kubernetes {
            label 'maven-build'
            yaml """
apiVersion: v1
kind: Pod
metadata:
  labels:
    jenkins: agent
spec:
  containers:
  - name: maven
    image: maven:3.8-jdk-11
    command: ['cat']
    tty: true
  - name: docker
    image: docker:latest
    command: ['cat']
    tty: true
    volumeMounts:
    - name: docker-sock
      mountPath: /var/run/docker.sock
  volumes:
  - name: docker-sock
    hostPath:
      path: /var/run/docker.sock
"""
        }
    }
    
    stages {
        stage('Build') {
            steps {
                container('maven') {
                    sh 'mvn clean package'
                }
            }
        }
        stage('Docker Build') {
            steps {
                container('docker') {
                    sh 'docker build -t myapp .'
                }
            }
        }
    }
}
```

---

**3. Load Balancing Multiple Masters:**

```
         Load Balancer
              |
    ┌─────────┴─────────┐
    |                   |
Master 1            Master 2
   |                    |
Agents 1-10        Agents 11-20
```

---

**4. Shared Libraries (DRY):**

```groovy
// vars/standardPipeline.groovy
def call(Map config) {
    pipeline {
        agent any
        
        stages {
            stage('Checkout') {
                steps {
                    checkout scm
                }
            }
            
            stage('Build') {
                steps {
                    sh "${config.buildCommand}"
                }
            }
            
            stage('Test') {
                steps {
                    sh "${config.testCommand}"
                }
            }
            
            stage('Deploy') {
                when {
                    branch 'main'
                }
                steps {
                    sh "${config.deployCommand}"
                }
            }
        }
        
        post {
            always {
                junit '**/test-results/*.xml'
            }
        }
    }
}

// Jenkinsfile
@Library('shared-library@main') _

standardPipeline(
    buildCommand: 'npm run build',
    testCommand: 'npm test',
    deployCommand: './deploy.sh'
)
```

---

**5. Build Caching:**

```groovy
pipeline {
    agent any
    
    stages {
        stage('Build') {
            steps {
                script {
                    // Cache dependencies
                    def cacheKey = "npm-cache-${hashFiles('package-lock.json')}"
                    
                    cache(caches: [
                        arbitraryFileCache(
                            path: 'node_modules',
                            cacheValidityDecidingFile: 'package-lock.json'
                        )
                    ]) {
                        sh 'npm ci'
                        sh 'npm run build'
                    }
                }
            }
        }
    }
}
```

---

**6. Distributed Builds:**

```groovy
// Build different parts on different agents
pipeline {
    agent none
    
    stages {
        stage('Parallel Builds') {
            parallel {
                stage('Frontend') {
                    agent { label 'node-agent' }
                    steps {
                        dir('frontend') {
                            sh 'npm run build'
                        }
                    }
                }
                
                stage('Backend') {
                    agent { label 'java-agent' }
                    steps {
                        dir('backend') {
                            sh 'mvn package'
                        }
                    }
                }
                
                stage('Mobile') {
                    agent { label 'mobile-agent' }
                    steps {
                        dir('mobile') {
                            sh 'flutter build'
                        }
                    }
                }
            }
        }
    }
}
```

---

**7. Performance Optimization:**

```groovy
// Jenkins system configuration
jenkins:
  numExecutors: 0  # Master should not execute builds
  quietPeriod: 5
  scmCheckoutRetryCount: 3
  
  globalNodeProperties:
    - envVars:
        env:
          - key: MAVEN_OPTS
            value: "-Xmx2g -XX:MaxPermSize=512m"

// Cleanup old builds
properties([
    buildDiscarder(
        logRotator(
            daysToKeepStr: '30',
            numToKeepStr: '100',
            artifactDaysToKeepStr: '7',
            artifactNumToKeepStr: '10'
        )
    )
])
```

---

## Best Practices

### Q8: What are Jenkins best practices for enterprise environments?

**Answer:**

**1. Pipeline as Code:**
```groovy
// ✅ Good: Jenkinsfile in repo
// Versioned, code-reviewed, tested

// ❌ Bad: UI-configured freestyle jobs
// Hard to track changes, not versioned
```

---

**2. Immutable Agents:**
```groovy
// ✅ Good: Fresh environment every build
pipeline {
    agent {
        docker {
            image 'node:18'
            reuseNode false  // Always fresh container
        }
    }
}

// ❌ Bad: Reusing same agent
// Leftover files, contaminated environment
```

---

**3. Security First:**
```groovy
// ✅ Good: Use credentials plugin
environment {
    AWS_CREDS = credentials('aws-credentials')
}

// ❌ Bad: Hardcoded secrets
environment {
    AWS_ACCESS_KEY = 'AKIAIOSFODNN7EXAMPLE'
}
```

---

**4. Fail Fast:**
```groovy
pipeline {
    options {
        timeout(time: 1, unit: 'HOURS')  // Don't hang forever
    }
    
    stages {
        stage('Quick Checks') {
            steps {
                // Run fast checks first
                sh 'npm run lint'
                sh 'npm run type-check'
            }
        }
        
        stage('Slow Tests') {
            // Only if quick checks pass
            steps {
                sh 'npm run test:e2e'
            }
        }
    }
}
```

---

**5. Notification Strategy:**
```groovy
post {
    failure {
        // Only notify on failures
        slackSend(color: 'danger', message: "Build failed: ${env.JOB_NAME}")
    }
    fixed {
        // Notify when build is fixed
        slackSend(color: 'good', message: "Build fixed: ${env.JOB_NAME}")
    }
}
```

---

**6. Backup & Disaster Recovery:**
```bash
#!/bin/bash
# Backup Jenkins

# Backup configuration
tar -czf jenkins-backup-$(date +%Y%m%d).tar.gz \
    /var/lib/jenkins/jobs \
    /var/lib/jenkins/users \
    /var/lib/jenkins/plugins \
    /var/lib/jenkins/config.xml \
    /var/lib/jenkins/jenkins.yaml

# Upload to S3
aws s3 cp jenkins-backup-$(date +%Y%m%d).tar.gz s3://jenkins-backups/

# Keep only last 30 days
find /backups -name "jenkins-backup-*" -mtime +30 -delete
```

---

**7. Monitoring:**
```groovy
// Install Monitoring plugins:
// - Prometheus Metrics
// - Datadog
// - Build Monitor View

// Metrics to track:
// - Build success rate
// - Average build time
// - Queue time
// - Agent utilization
// - Failed builds by job
```

---

**8. Documentation:**
```groovy
// Document pipelines
/**
 * Build and deploy application
 * 
 * Parameters:
 * - ENVIRONMENT: Target environment (dev/staging/prod)
 * - SKIP_TESTS: Skip test execution
 * 
 * Credentials required:
 * - aws-credentials: AWS access
 * - deploy-ssh-key: SSH key for deployment
 * 
 * Notifications:
 * - Slack: #deployments channel
 * - Email: devops@company.com
 */
pipeline {
    // ...
}
```

---

## Summary

**Jenkins Best Practices:**

1. **Use Pipeline as Code**: Jenkinsfile in repo
2. **Declarative over Scripted**: Easier to maintain
3. **Shared Libraries**: Reusable code
4. **Dynamic Agents**: Kubernetes/Docker
5. **Security**: Credentials plugin, RBAC
6. **Parallel Execution**: Speed up builds
7. **Fail Fast**: Quick feedback
8. **Monitoring**: Track metrics
9. **Backup**: Regular backups
10. **Documentation**: Clear README and comments

**Remember:** Jenkins is powerful but complex. Start simple, add complexity only when needed!
