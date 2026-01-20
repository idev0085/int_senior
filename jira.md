# Jira Interview Questions

## Basic Questions

### 1. What is Jira?
Jira is a project management and issue tracking tool developed by Atlassian. It's widely used for agile software development, bug tracking, and project management. Jira supports Scrum, Kanban, and hybrid methodologies.

### 2. What are the main types of Jira products?
- **Jira Software**: Agile project management for software teams
- **Jira Service Management**: IT service management and customer support
- **Jira Work Management**: Business project management
- **Jira Align**: Enterprise agile planning

### 3. What is an Issue in Jira?
An issue is a work item that needs to be tracked. Issues can represent:
- User stories
- Tasks
- Bugs
- Epics
- Sub-tasks
- Custom issue types

### 4. What are the default Issue Types in Jira?
- **Epic**: Large body of work spanning multiple sprints
- **Story**: Feature or requirement from user perspective
- **Task**: Work item that needs to be completed
- **Bug**: Problem or defect in the software
- **Sub-task**: Breakdown of a larger issue
- **Improvement**: Enhancement to existing functionality

### 5. What is a Workflow in Jira?
A workflow defines the lifecycle of an issue through different statuses. Default statuses:
- **To Do**: Work not yet started
- **In Progress**: Work currently being done
- **Done**: Work completed

Custom workflows can include:
- Code Review
- QA Testing
- Ready for Deployment
- Blocked

### 6. What is a Sprint in Jira?
A sprint is a time-boxed iteration (typically 1-4 weeks) where teams work on a specific set of issues. Sprints are core to Scrum methodology.

### 7. What is a Backlog?
The backlog is a prioritized list of work items (issues) that need to be completed. It includes:
- Product backlog (all items)
- Sprint backlog (items for current sprint)

## Intermediate Questions

### 8. What are Jira Components and Labels?
- **Components**: Subsections of a project (e.g., Frontend, Backend, Database)
- **Labels**: Flexible tags for categorizing issues
- Components have owners, labels don't

### 9. What is an Epic in Jira?
An Epic is a large user story that can be broken down into smaller stories or tasks. It represents a significant feature or initiative that spans multiple sprints.

### 10. What are Story Points?
Story points are a unit of measure for expressing the overall effort required to implement a story. They represent:
- Complexity
- Effort
- Uncertainty

Common scales: Fibonacci (1, 2, 3, 5, 8, 13, 21)

### 11. What is Velocity in Jira?
Velocity is the amount of work (story points) a team completes in a sprint. It helps predict future sprint capacity and improve planning accuracy.

### 12. What are Filters in Jira?
Filters are saved JQL (Jira Query Language) queries that help find specific issues. They can be:
- Shared with teams
- Used in dashboards
- Subscribed to for notifications

```jql
project = "PROJECT" AND status = "In Progress" AND assignee = currentUser()
```

### 13. What is JQL (Jira Query Language)?
JQL is a powerful query language for searching issues in Jira.

Examples:
```jql
# Assigned to me and in progress
assignee = currentUser() AND status = "In Progress"

# High priority bugs
type = Bug AND priority = High

# Created last week
created >= -1w

# Unresolved issues in sprint
sprint in openSprints() AND resolution = Unresolved
```

### 14. What are Jira Boards?
- **Scrum Board**: Displays issues in sprints with backlog
- **Kanban Board**: Continuous flow board with WIP limits
- Boards visualize work progress through workflow columns

### 15. What is a Dashboard in Jira?
A dashboard is a customizable page containing gadgets that display project information, reports, and statistics in real-time.

Popular gadgets:
- Sprint burndown chart
- Issue statistics
- Filter results
- Activity stream
- Pie charts

### 16. What are Jira Schemes?
Schemes define project settings:
- **Permission Scheme**: Who can do what
- **Notification Scheme**: Who gets notified when
- **Issue Type Scheme**: Available issue types
- **Workflow Scheme**: Workflow for each issue type
- **Screen Scheme**: Fields displayed on screens

### 17. What is a Version/Release in Jira?
Versions represent product releases. They help:
- Track work for specific releases
- Generate release notes
- View version progress
- Set release dates

### 18. What are Custom Fields in Jira?
Custom fields extend issue information beyond default fields:
- Text fields
- Number fields
- Date pickers
- Select lists
- Checkboxes
- User pickers

## Advanced Questions

### 19. What is the difference between Scrum and Kanban in Jira?
**Scrum**:
- Fixed-length sprints
- Sprint planning and retrospectives
- Velocity tracking
- Commitment to sprint goals

**Kanban**:
- Continuous flow
- WIP limits
- Focus on cycle time
- No fixed iterations

### 20. What are Automation Rules in Jira?
Automation rules perform actions based on triggers:
- Auto-assign issues
- Transition issues automatically
- Send notifications
- Update fields
- Create subtasks
- Close old issues

Example: Automatically transition issue to "In Review" when PR is created

### 21. What is Portfolio for Jira (Advanced Roadmaps)?
Portfolio provides cross-project planning and tracking:
- Roadmap visualization
- Dependency management
- Resource planning
- Scenario planning
- Capacity planning

### 22. What are Jira Permissions?
Different permission levels:
- **Browse Projects**: View project
- **Create Issues**: Add new issues
- **Edit Issues**: Modify issues
- **Delete Issues**: Remove issues
- **Assign Issues**: Change assignee
- **Administer Projects**: Project configuration
- **Administer Jira**: System-wide settings

### 23. How do you integrate Jira with other tools?
Common integrations:
- **Confluence**: Documentation and knowledge base
- **Bitbucket/GitHub/GitLab**: Source control
- **Jenkins/Bamboo**: CI/CD pipelines
- **Slack/Teams**: Chat notifications
- **Zephyr**: Test management
- **Tempo**: Time tracking

### 24. What is Smart Commits?
Smart commits allow developers to perform Jira actions directly from commit messages:

```bash
git commit -m "PROJECT-123 #time 2h #comment Fixed bug"
```

Actions:
- Comment on issues
- Log work time
- Transition workflow
- Reference issues

### 25. What are Sub-tasks in Jira?
Sub-tasks break down larger issues into smaller pieces:
- Belong to parent issue
- Can be assigned to different users
- Have independent workflows
- Contribute to parent's progress

### 26. What is Jira REST API?
REST API allows programmatic interaction with Jira:
```bash
# Get issue
curl -u user:password https://your-domain.atlassian.net/rest/api/3/issue/PROJECT-123

# Create issue
curl -X POST -H "Content-Type: application/json" \
  -d '{"fields":{"project":{"key":"PROJECT"},"summary":"Issue summary","issuetype":{"name":"Task"}}}' \
  https://your-domain.atlassian.net/rest/api/3/issue/
```

### 27. What are Jira Reports?
Built-in reports:
- **Burndown Chart**: Work remaining in sprint
- **Burnup Chart**: Work completed over time
- **Velocity Chart**: Story points per sprint
- **Cumulative Flow Diagram**: Issue distribution over time
- **Control Chart**: Cycle time and lead time
- **Epic Report**: Progress of epics
- **Version Report**: Release progress

### 28. What is Issue Linking in Jira?
Link related issues with relationship types:
- Blocks/Blocked by
- Relates to
- Duplicates/Duplicated by
- Clones/Cloned by
- Custom link types

### 29. What are Jira Service Management features?
- Ticket management
- SLA tracking
- Knowledge base
- Request types
- Service desk portal
- Incident management
- Change management
- Asset management

### 30. How do you handle Dependencies in Jira?
- Use issue links (blocks/blocked by)
- Advanced Roadmaps for visual dependency mapping
- Add custom fields for dependency tracking
- Create sub-tasks for dependent work
- Use automation to notify when blockers are resolved

## Best Practices

### 31. Jira Best Practices
- Keep issue descriptions clear and concise
- Use consistent naming conventions
- Regularly groom backlog
- Set realistic sprint goals
- Use components and labels effectively
- Close completed sprints promptly
- Review and update workflows regularly
- Use dashboards for visibility
- Integrate with development tools
- Train team members on Jira usage
- Use templates for recurring issue types
- Keep custom fields to minimum
- Document processes in Confluence
- Use automation to reduce manual work
- Regularly archive old projects
- Monitor and optimize performance

### 32. Jira Administration Tips
- Use project templates
- Standardize workflows across similar projects
- Implement proper permission schemes
- Regular backup of data
- Monitor system performance
- Keep Jira updated
- Use groups for permission management
- Document configuration changes
- Implement naming conventions
- Regular cleanup of unused fields/schemes

### 33. Common JQL Functions
- `currentUser()` - Current logged-in user
- `now()` - Current date/time
- `startOfDay()`, `endOfDay()` - Time boundaries
- `openSprints()` - Currently active sprints
- `closedSprints()` - Completed sprints
- `membersOf()` - Members of a group
- `linkedIssues()` - Issues linked to specified issue

### 34. Agile Metrics in Jira
- Velocity - Story points per sprint
- Sprint burndown - Remaining work
- Cycle time - Time from start to completion
- Lead time - Time from creation to completion
- Throughput - Issues completed per time period
- Work in progress (WIP) - Current active items
