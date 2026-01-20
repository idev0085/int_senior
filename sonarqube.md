# SonarQube Interview Questions

## Basic Questions

### 1. What is SonarQube?
SonarQube is an open-source platform for continuous inspection of code quality. It performs automatic reviews with static code analysis to detect bugs, code smells, and security vulnerabilities in over 25 programming languages.

### 2. What are the key features of SonarQube?
- Static code analysis
- Bug detection
- Code smell identification
- Security vulnerability detection
- Technical debt tracking
- Code coverage analysis
- Duplicate code detection
- Complexity metrics
- Multi-language support
- Integration with CI/CD pipelines

### 3. What is the difference between SonarQube and SonarLint?
- **SonarQube**: Server-based platform for centralized code quality management
- **SonarLint**: IDE plugin that provides real-time feedback during development
- SonarLint works offline and provides immediate feedback
- SonarQube provides historical data and team-wide metrics

### 4. What are the main components of SonarQube?
- **SonarQube Server**: Web interface and processing engine
- **Database**: Stores configuration and analysis results (PostgreSQL, Oracle, MS SQL)
- **Scanner**: Analyzes source code and sends results to server
- **Plugins**: Extend functionality for different languages and tools

### 5. What is a Quality Gate?
A Quality Gate is a set of conditions that must be met before code can be released. It defines thresholds for metrics like:
- Code coverage
- Duplicated code
- Bug count
- Vulnerabilities
- Code smells
- Technical debt ratio

### 6. What are Code Smells?
Code smells are maintainability issues that make code harder to understand, modify, or extend. Examples:
- Long methods
- Large classes
- Duplicated code
- Complex conditions
- Dead code
- Inconsistent naming

## Intermediate Questions

### 7. What are the different types of issues in SonarQube?
- **Bug**: Code that is demonstrably wrong or highly likely to yield unexpected behavior
- **Vulnerability**: Security-related issue that could be exploited
- **Code Smell**: Maintainability issue
- **Security Hotspot**: Security-sensitive code requiring manual review

### 8. What are Issue Severities in SonarQube?
- **Blocker**: Critical issues that must be fixed immediately
- **Critical**: High-impact issues
- **Major**: Significant issues
- **Minor**: Less significant issues
- **Info**: Informational issues

### 9. Explain Technical Debt in SonarQube
Technical debt represents the effort required to fix all maintainability issues. It's measured in time (minutes, hours, days) based on:
- Code smells
- Complexity
- Duplication
- Lack of documentation

### 10. What is Code Coverage in SonarQube?
Code coverage measures the percentage of code executed by unit tests. SonarQube displays:
- Line coverage
- Branch coverage
- Overall coverage percentage
- Uncovered lines and conditions

### 11. How do you integrate SonarQube with CI/CD?
```bash
# Using SonarScanner
sonar-scanner \
  -Dsonar.projectKey=my-project \
  -Dsonar.sources=. \
  -Dsonar.host.url=http://localhost:9000 \
  -Dsonar.login=your-token
```

Common integrations:
- Jenkins with SonarQube Scanner plugin
- GitLab CI/CD
- GitHub Actions
- Azure DevOps
- CircleCI

### 12. What is sonar-project.properties?
Configuration file for SonarQube analysis:

```properties
sonar.projectKey=my-project
sonar.projectName=My Project
sonar.projectVersion=1.0
sonar.sources=src
sonar.tests=tests
sonar.language=java
sonar.sourceEncoding=UTF-8
sonar.java.binaries=target/classes
```

### 13. What are Quality Profiles?
Quality Profiles are collections of rules for a specific language. They define which rules are active and their severity levels. You can:
- Create custom profiles
- Extend built-in profiles
- Set different profiles for different projects

### 14. How do you exclude files from SonarQube analysis?
```properties
# Exclude specific files
sonar.exclusions=**/test/**,**/migrations/**,**/*.spec.ts

# Exclude from coverage
sonar.coverage.exclusions=**/*.test.js,**/config/**

# Exclude from duplication
sonar.cpd.exclusions=**/generated/**
```

### 15. What are SonarQube Webhooks?
Webhooks notify external systems about Quality Gate status changes. They can trigger:
- CI/CD pipeline decisions
- Notification systems
- Custom integrations
- Automated actions

## Advanced Questions

### 16. How do you handle false positives in SonarQube?
- Mark issue as "Won't Fix" with justification
- Mark as "False Positive"
- Change issue severity
- Disable specific rules
- Use `@SuppressWarnings` annotations (language-specific)
- Adjust Quality Profile

### 17. What is the difference between branches and pull requests in SonarQube?
- **Branches**: Long-lived branches analyzed separately
- **Pull Requests**: Short-lived analysis for code review
- PR analysis shows new issues introduced
- Branch analysis tracks quality over time

### 18. Explain SonarQube Security Hotspots
Security Hotspots are security-sensitive pieces of code that need manual review:
- Password handling
- Cryptographic operations
- File system access
- Database queries
- Network connections

They require developers to review and mark as:
- Safe
- To review
- Fixed

### 19. What are SonarQube Measures and Metrics?
Key metrics:
- **Reliability**: Bugs, bug density
- **Security**: Vulnerabilities, security rating
- **Maintainability**: Code smells, technical debt
- **Coverage**: Line coverage, branch coverage
- **Duplications**: Duplicated lines, blocks
- **Complexity**: Cyclomatic complexity, cognitive complexity
- **Size**: Lines of code, statements

### 20. How does SonarQube calculate Ratings?
Ratings (A to E) based on:
- **Reliability Rating**: Bug density
- **Security Rating**: Vulnerability severity
- **Maintainability Rating**: Technical debt ratio
- **Security Review Rating**: Security hotspots reviewed

### 21. What is the New Code definition?
New Code defines what code is considered "new" for Quality Gate evaluation:
- Previous version
- Number of days
- Specific date
- Reference branch

### 22. How do you optimize SonarQube performance?
- Increase memory allocation
- Use database connection pooling
- Optimize scanner configuration
- Exclude unnecessary files
- Use incremental analysis
- Configure proper indices on database
- Scale horizontally with multiple nodes (Data Center Edition)

### 23. What are SonarQube Plugins?
Plugins extend SonarQube functionality:
- Language analyzers (Python, C++, etc.)
- Integration plugins (LDAP, SAML)
- Report plugins
- Custom rules
- SCM integration

Popular plugins:
- SonarJS, SonarJava, SonarPython
- Community plugins for specific frameworks

## Configuration and Administration

### 24. What are the different editions of SonarQube?
- **Community**: Free, basic features
- **Developer**: Branch analysis, PR decoration, additional languages
- **Enterprise**: Portfolio management, advanced security features
- **Data Center**: High availability, scalability

### 25. How do you backup and restore SonarQube?
- Backup database
- Backup `$SONAR_HOME/data` directory
- Backup `$SONAR_HOME/extensions` directory
- Backup configuration files

### 26. What is Portfolio Management in SonarQube?
Available in Enterprise Edition:
- Group multiple projects
- Aggregate quality metrics
- Track organization-wide quality
- Define governance policies

## Integration and DevOps

### 27. How to integrate SonarQube with Jenkins?
1. Install SonarQube Scanner plugin
2. Configure SonarQube server in Jenkins
3. Add SonarQube scanner in build step
4. Configure Quality Gate check

```groovy
// Jenkinsfile
stage('SonarQube Analysis') {
  withSonarQubeEnv('SonarQube') {
    sh 'mvn sonar:sonar'
  }
}

stage('Quality Gate') {
  timeout(time: 1, unit: 'HOURS') {
    waitForQualityGate abortPipeline: true
  }
}
```

### 28. How to integrate with GitHub/GitLab?
- Configure authentication
- Set up webhooks
- Enable PR/MR decoration
- Configure Quality Gate status checks
- Automatic PR comments with analysis results

### 29. How to generate custom reports?
- Use SonarQube Web API
- Export data to external tools
- Use Community Branch Plugin
- Create custom dashboards
- Use database queries for advanced reporting

### 30. What are common SonarQube best practices?
- Run analysis on every commit
- Enforce Quality Gate in CI/CD
- Configure appropriate rules for your tech stack
- Review and manage technical debt regularly
- Train developers on SonarQube usage
- Customize Quality Profiles for project needs
- Use Security Hotspots reviews
- Monitor and track metrics over time
- Integrate with IDEs using SonarLint
- Regular updates and maintenance
- Set realistic Quality Gate thresholds
- Review false positives regularly
