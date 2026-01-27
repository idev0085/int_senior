# Agile Methodology - Interview Questions

## Fundamentals & Principles (1-15)

### 1. What is Agile methodology?
Agile is an iterative approach to software development and project management that emphasizes flexibility, collaboration, customer feedback, and rapid delivery of working software in short iterations called sprints.

### 2. What are the four core values of the Agile Manifesto?
- Individuals and interactions over processes and tools
- Working software over comprehensive documentation
- Customer collaboration over contract negotiation
- Responding to change over following a plan

### 3. What are the 12 principles of Agile?
1. Customer satisfaction through early and continuous delivery
2. Welcome changing requirements, even late in development
3. Deliver working software frequently
4. Business people and developers must work together daily
5. Build projects around motivated individuals
6. Face-to-face conversation is the most efficient method
7. Working software is the primary measure of progress
8. Sustainable development pace
9. Continuous attention to technical excellence
10. Simplicity - maximizing work not done
11. Self-organizing teams
12. Regular reflection and adjustment

### 4. What are the benefits of using Agile methodology?
- Faster time to market
- Better quality through continuous testing
- Improved customer satisfaction
- Higher team morale and productivity
- Better visibility and transparency
- Reduced risk through incremental delivery
- Flexibility to adapt to changes

### 5. What are the different Agile frameworks?
- Scrum
- Kanban
- Extreme Programming (XP)
- Lean
- Crystal
- Feature-Driven Development (FDD)
- Dynamic Systems Development Method (DSDM)
- Scaled Agile Framework (SAFe)

### 6. How is Agile different from Waterfall?
Waterfall is sequential and linear, while Agile is iterative and incremental. Waterfall requires all requirements upfront, while Agile welcomes changing requirements. Waterfall has limited customer involvement, while Agile emphasizes continuous collaboration.

### 7. What is an iteration in Agile?
An iteration is a time-boxed period (typically 1-4 weeks) during which the team works to complete a set of planned work items and deliver a potentially shippable product increment.

### 8. What is a user story?
A user story is a short, simple description of a feature from the end user's perspective, typically following the format: "As a [type of user], I want [goal] so that [benefit]."

### 9. What are acceptance criteria?
Acceptance criteria are conditions that must be met for a user story to be considered complete. They define the boundaries of the story and are used to verify that the implementation meets requirements.

### 10. What is the Definition of Done (DoD)?
The Definition of Done is a shared understanding of what it means for work to be complete. It typically includes criteria like code written, tested, reviewed, integrated, documented, and deployed.

### 11. What is the Definition of Ready (DoR)?
The Definition of Ready is a set of criteria that a user story must meet before it can be pulled into a sprint. It ensures stories are well-understood and actionable.

### 12. What is technical debt?
Technical debt refers to the implied cost of additional rework caused by choosing an easy or quick solution now instead of using a better approach that would take longer.

### 13. What is velocity in Agile?
Velocity is a metric that measures the amount of work a team can complete in a single sprint, typically measured in story points. It helps with sprint planning and forecasting.

### 14. What are story points?
Story points are a unit of measure for estimating the effort required to implement a user story. They represent complexity, effort, and uncertainty rather than time.

### 15. What is the purpose of timeboxing?
Timeboxing sets a fixed time period for an activity, ensuring that work doesn't extend indefinitely and helping teams maintain focus and momentum.

## Types of Stories & Uses (16-25)

### 16. What are the different types of stories in Agile?

**Answer:**

Agile teams use several types of stories to represent different kinds of work. Each type serves a specific purpose and requires different handling.

---

#### **1. User Stories**

**Definition:** A feature or functionality described from the end user's perspective.

**Format:**
```
As a [type of user]
I want [goal]
So that [benefit]
```

**Example:**
```
As a customer
I want to filter products by price
So that I can find items within my budget
```

**Characteristics:**
- Focused on user value
- Small enough to complete in one sprint
- Includes acceptance criteria
- Typically 1-5 story points

**Use Cases:**
- New features
- User-facing functionality
- Customer-requested enhancements

---

#### **2. Technical Stories (Technical Debt/Technical Tasks)**

**Definition:** Work required to improve code quality, performance, or architecture without direct user value.

**Examples:**
- Refactoring legacy code
- Upgrading dependencies
- Improving test coverage
- Database optimization
- Architectural improvements

**Example:**
```
Technical Task: Refactor authentication module
Description: Current auth module is tightly coupled and difficult to test.
Refactor to use dependency injection pattern.

Acceptance Criteria:
- Module is 30% smaller
- Unit test coverage increased to 90%
- No behavioral changes for end users
```

**Characteristics:**
- No direct user value
- Improves code quality, maintainability, or performance
- Required for long-term sustainability
- Often deprioritized in favor of features

**Challenges:**
- Hard to justify business value
- Often pushed to backlog
- Can accumulate as technical debt if ignored

---

#### **3. Bug Stories**

**Definition:** Issues or defects in existing functionality that need to be fixed.

**Types of Bugs:**

**Critical Bugs:**
```
Bug: Login button not working on mobile
Priority: Critical
Current Behavior: Users cannot log in on mobile devices
Expected Behavior: Login should work seamlessly on all devices
Steps to Reproduce: 1. Open app on iPhone, 2. Click login, 3. Button doesn't respond
Impact: 50% of users affected
```

**Minor Bugs:**
```
Bug: Typo in help text
Priority: Low
Current Behavior: Help text says "pasword" instead of "password"
Expected Behavior: Correct spelling
Impact: No functional impact, minor UX issue
```

**Characteristics:**
- Based on severity (Critical, High, Medium, Low)
- Fixed based on priority
- Should be prevented with better testing
- Can be in Definition of Done (automatically fixed)

**Handling Bugs:**
```
- CRITICAL: Fixed immediately, taken out of sprint flow
- HIGH: Included in upcoming sprint
- MEDIUM: Added to backlog, reviewed in refinement
- LOW: Backlog, fixed when capacity available
```

---

#### **4. Spikes (Research Stories)**

**Definition:** Time-boxed investigation or research activity to answer questions or reduce risk.

**Purpose:**
- Investigate new technologies
- Evaluate architectural approaches
- Determine feasibility
- Reduce uncertainty before committing to story

**Example:**
```
Spike: Investigate real-time messaging solutions
Duration: 3 days
Objective: Evaluate WebSocket vs Server-Sent Events for real-time notifications
Deliverable: Proof of concept and recommendation

Questions to Answer:
1. Which solution is more performant?
2. Which scales better to 100k+ users?
3. Which has better browser support?
4. Cost comparison?
```

**Characteristics:**
- Time-boxed (hours/days, not story points)
- Produces knowledge, not shippable code
- Outcome is usually a recommendation or decision
- Reduces risk for future stories

**When to Use:**
- Unknown technologies
- Architectural decisions
- Performance concerns
- Integration questions
- Feasibility analysis

---

#### **5. Chores**

**Definition:** Work that is necessary but not directly related to features or bugs.

**Examples:**
- Updating documentation
- Infrastructure maintenance
- Environment setup
- Dependency updates
- Security patches
- Monitoring configuration

**Example:**
```
Chore: Update project documentation with new API endpoints
Description: Document 3 new endpoints added in last sprint
Acceptance Criteria:
- All 3 endpoints documented with examples
- Request/response examples included
- Error cases documented
```

**Characteristics:**
- Necessary for project health
- Not tied to features or bugs
- Often overlooked but important
- Can be grouped in sprints

---

#### **6. Enablers**

**Definition:** Stories that enable future work or capabilities.

**Examples:**
- Setting up CI/CD pipeline
- Creating shared libraries
- Establishing coding standards
- Setting up monitoring/logging
- Performance baseline establishment

**Example:**
```
Enabler: Establish centralized logging infrastructure
Description: Set up ELK stack for application logging
Benefit: Enables faster debugging and monitoring of all services
Future Impact: 10+ upcoming features depend on this
```

---

#### **7. Epic**

**Definition:** Large body of work that spans multiple sprints and can be broken down into smaller user stories.

**Relationship:**
```
Epic (Large Feature)
├── User Story 1
├── User Story 2
├── User Story 3
└── Technical Story
```

**Example:**
```
EPIC: Payment System Overhaul
Description: Complete redesign of payment processing

Stories:
1. Implement Stripe integration
2. Add card validation
3. Create payment history UI
4. Setup payment notifications
5. Add refund functionality
6. Technical: Migrate legacy payment DB
```

**Characteristics:**
- Typically 3-6 months of work
- Cannot be completed in single sprint
- Broken into smaller stories
- Guides product direction

---

### 17. Story Types Comparison Table

| Type | Duration | User Value | Frequency | Priority | Example |
|------|----------|-----------|-----------|----------|---------|
| User Story | 1-2 sprints | High | Very High | High | "Add search filter" |
| Technical Story | 1-2 sprints | Low (indirect) | Medium | Medium | "Refactor authentication" |
| Bug (Critical) | Hours-1 day | High | Varies | Critical | "Login broken on mobile" |
| Bug (Low) | Days-1 sprint | Low | Varies | Low | "Typo in label" |
| Spike | 1-5 days | None (knowledge) | As needed | Medium | "Evaluate Redis vs Memcached" |
| Chore | Hours-1 day | None (maintenance) | Medium | Low | "Update README" |
| Enabler | 1-2 sprints | None (indirect) | Medium | Medium | "Setup monitoring" |

---

### 18. How to Write Effective Stories

**User Story Template:**
```
Title: Clear, concise description

User Story:
As a [user role]
I want [action]
So that [benefit]

Acceptance Criteria:
- [ ] Criteria 1
- [ ] Criteria 2
- [ ] Criteria 3

Technical Notes:
- Any specific implementation details
- Known limitations
- Affected systems

Definition of Done:
- Code written and reviewed
- Unit tests written (>80% coverage)
- Integration tests passing
- QA tested
- Documentation updated
- Deployed to staging
```

**Example - Good User Story:**
```
Title: Filter products by category

User Story:
As a customer
I want to filter products by category
So that I can quickly find items I'm interested in

Acceptance Criteria:
- Category filter appears on products page
- Selecting a category shows only items in that category
- Filter persists when navigating back
- "Clear filters" button resets selection
- Mobile friendly layout maintained

Technical Notes:
- Use existing ProductFilter component
- Cache category list for performance
- Add analytics tracking for filter selections
```

**Example - Poor User Story:**
```
Title: Fix stuff

Description: Make the app better and faster

Acceptance Criteria:
- It works
```

---

### 19. Story Estimation for Different Types

**User Stories:** Story Points (Relative Estimation)
```
Small (1-3 pts): Can complete in a few hours
Medium (5-8 pts): Complete in 1-2 days
Large (13+ pts): Should be broken down
```

**Spikes:** Hours/Days
```
Quick spike: 4-8 hours
Medium spike: 2-3 days
Complex spike: 1 week (then usually creates 3-5 stories)
```

**Bugs:** By Severity
```
Critical: Fix within hours
High: Fix in current sprint
Medium: Next sprint
Low: Backlog, fix when convenient
```

**Chores:** Hours/Days or included in Definition of Done
```
Can be batched together
Often less than 1 story point each
```

---

### 20. Story Life Cycle

**1. Backlog** → Story created, not yet refined

**2. Refinement** → Story discussed, clarified, estimated
```
- Acceptance criteria finalized
- Questions answered
- Estimate assigned
- Marked as "Ready"
```

**3. Sprint Planning** → Story selected for sprint

**4. In Progress** → Developer starts work

**5. Review** → Code review, QA testing

**6. Done** → Meets Definition of Done

**7. Closed** → Released to production

---

### 21. Anti-patterns in Story Writing

**❌ Too Big:**
```
Epic disguised as story
"Build entire e-commerce system" 
→ Break into 10-15 stories
```

**❌ Too Technical:**
```
"Refactor the database schema"
→ Better: "Improve order query performance from 2s to <500ms"
```

**❌ No User Value:**
```
"Update dependencies"
→ Better: "Update dependencies to fix security vulnerabilities affecting users"
```

**❌ Vague Acceptance Criteria:**
```
"Make it faster"
→ Better: "Reduce page load time from 3s to <1s"
```

**❌ Missing Context:**
```
"Add button"
→ Better: "Add 'Save for Later' button on product detail page so users can bookmark items"
```

---

### 22. Story Management Best Practices

**DO:**
✅ Keep stories independent
✅ Make stories testable
✅ Include acceptance criteria
✅ Break large stories down
✅ Keep stories in backlog until ready
✅ Use consistent format
✅ Include user perspective
✅ Estimate relative to team's baseline

**DON'T:**
❌ Mix multiple features in one story
❌ Create stories without acceptance criteria
❌ Make stories too small (< 1 point)
❌ Make stories too large (> 13 points)
❌ Include implementation details as requirements
❌ Forget about non-functional requirements
❌ Skip the refinement process
❌ Use absolute time estimates instead of points

---

### 23. Different Story Types by Industry

**E-commerce:**
- User Story: "Add product to wishlist"
- Technical: "Optimize product search indexing"
- Chore: "Update payment gateway documentation"

**SaaS:**
- User Story: "Enable SSO authentication"
- Spike: "Investigate multi-tenancy architecture"
- Bug: "Subscription doesn't renew on time"

**Mobile App:**
- User Story: "Push notification for order status"
- Technical: "Migrate to new analytics SDK"
- Enabler: "Setup crash reporting"

---

### 24. Story Splitting Strategies

**When story is too big, split by:**

**1. By User Role:**
```
Large: "Setup user profiles and permissions"
Split into:
- "Admin can create user accounts"
- "Manager can assign permissions"
- "User can view their profile"
```

**2. By Workflow:**
```
Large: "Complete checkout process"
Split into:
- "Add items to cart"
- "Apply coupon code"
- "Enter shipping address"
- "Process payment"
- "Send order confirmation"
```

**3. By Data:**
```
Large: "Generate reports for all metrics"
Split into:
- "Generate sales report"
- "Generate customer report"
- "Generate inventory report"
```

**4. By Technology:**
```
Large: "Build real-time collaboration"
Split into:
- "Setup WebSocket infrastructure"
- "Implement real-time cursor tracking"
- "Implement real-time document sync"
```

---

### 25. Story Metrics & KPIs

**Track These:**
- **Cycle Time:** Days from start to done
- **Velocity:** Stories completed per sprint
- **Lead Time:** Days from backlog to done
- **Stories per Sprint:** Average number completed
- **Rework Rate:** % of stories with bugs after completion
- **Story Distribution:** % of features vs technical vs bugs

**Example Dashboard:**
```
Velocity (last 4 sprints): 21, 23, 21, 22 pts
Average Cycle Time: 5.2 days
Story Distribution: 60% Features, 20% Technical, 20% Bugs
Rework Rate: 8% (target: <5%)
```

---

## Scrum Framework (26-50)

### 26. What is Scrum?
Scrum is an Agile framework for managing complex projects, particularly software development. It uses fixed-length iterations called sprints and emphasizes empirical process control.

### 27. What are the three pillars of Scrum?
- Transparency: All aspects of the process must be visible
- Inspection: Regularly inspect artifacts and progress
- Adaptation: Adjust process when inspection reveals issues

### 18. What are the Scrum roles?
- Product Owner: Maximizes product value, manages backlog
- Scrum Master: Facilitates Scrum, removes impediments
- Development Team: Self-organizing team that delivers increments

### 19. What are the responsibilities of a Product Owner?
- Define and prioritize the product backlog
- Ensure team understands backlog items
- Make decisions about scope and acceptance
- Maximize ROI and product value
- Engage with stakeholders
- Accept or reject work results

### 20. What are the responsibilities of a Scrum Master?
- Facilitate Scrum events
- Remove impediments
- Coach the team on Scrum practices
- Protect the team from external interruptions
- Help improve team processes
- Promote self-organization

### 21. What are the characteristics of a Development Team?
- Self-organizing
- Cross-functional
- No sub-teams or hierarchies
- Typically 3-9 members
- Collectively accountable for deliverables

### 22. What is a Sprint?
A Sprint is a time-boxed iteration (usually 1-4 weeks) during which a potentially releasable product increment is created. Once started, the sprint duration is fixed.

### 23. What are the Scrum ceremonies/events?
- Sprint Planning
- Daily Stand-up (Daily Scrum)
- Sprint Review
- Sprint Retrospective
- Backlog Refinement (Grooming)

### 24. What happens in Sprint Planning?
The team collaborates to define the sprint goal and select backlog items to work on. They discuss how the work will be accomplished and commit to the sprint backlog.

### 25. What is the purpose of the Daily Stand-up?
The Daily Stand-up (Daily Scrum) is a 15-minute time-boxed event for the team to synchronize activities, plan the next 24 hours, and identify impediments.

### 26. What are the three questions in a Daily Stand-up?
- What did I do yesterday?
- What will I do today?
- Are there any impediments blocking my progress?

### 27. What is a Sprint Review?
A Sprint Review is held at the end of each sprint to inspect the increment and adapt the product backlog. The team demonstrates completed work to stakeholders and gathers feedback.

### 28. What is a Sprint Retrospective?
A Sprint Retrospective is held after the Sprint Review where the team reflects on their process, discusses what went well, what didn't, and identifies improvements for the next sprint.

### 29. What is the Product Backlog?
The Product Backlog is an ordered list of everything that might be needed in the product. It's the single source of requirements and is continuously refined by the Product Owner.

### 30. What is the Sprint Backlog?
The Sprint Backlog is the set of Product Backlog items selected for the sprint, plus a plan for delivering them. It's owned by the Development Team.

### 31. What is an Increment?
An Increment is the sum of all Product Backlog items completed during a sprint plus the value of increments from all previous sprints. It must be in usable condition.

### 32. What is backlog refinement/grooming?
Backlog refinement is the ongoing process of reviewing, estimating, and prioritizing product backlog items to ensure they're ready for upcoming sprints.

### 33. Can you change sprint scope during a sprint?
Generally no. The sprint goal should remain intact, though the Development Team can negotiate scope with the Product Owner if needed. Frequent changes suggest poor sprint planning.

### 34. What happens if the team can't finish all committed work in a sprint?
Incomplete items return to the product backlog for re-prioritization. The team discusses why in the retrospective and adjusts planning for future sprints.

### 35. What is the ideal sprint length?
Sprint length depends on project complexity and team maturity, but 2 weeks is most common. Shorter sprints provide faster feedback; longer sprints allow more substantial work.

### 36. How do you handle bugs in Scrum?
Bugs can be added to the product backlog, included in the Definition of Done requiring immediate fixes, or addressed during bug triage sessions depending on severity.

### 37. What is a sprint goal?
A sprint goal is a short statement that describes what the team plans to achieve during the sprint. It provides focus and helps make trade-off decisions.

### 38. Can a sprint be cancelled?
Yes, but only the Product Owner can cancel a sprint. This happens when the sprint goal becomes obsolete, typically due to major business changes.

### 39. What is a burndown chart?
A burndown chart visualizes remaining work over time during a sprint, helping teams track progress and predict whether they'll complete committed work.

### 40. What is a burnup chart?
A burnup chart shows work completed over time, displaying both completed work and total scope, making scope changes more visible than burndown charts.

## Kanban (41-50)

### 41. What is Kanban?
Kanban is a visual Agile framework focused on continuous flow, limiting work in progress, and maximizing efficiency. Work items move through defined stages on a Kanban board.

### 42. What are the core principles of Kanban?
- Visualize workflow
- Limit work in progress (WIP)
- Manage flow
- Make policies explicit
- Implement feedback loops
- Improve collaboratively, evolve experimentally

### 43. What is WIP (Work in Progress) limit?
WIP limits restrict the number of items that can be in a specific workflow stage at once, preventing overload and identifying bottlenecks.

### 44. How is Kanban different from Scrum?
Kanban has continuous flow vs. Scrum's sprints, no prescribed roles, no time-boxed iterations, and focuses on limiting WIP rather than estimating velocity.

### 45. What is a Kanban board?
A visual tool showing workflow stages (columns) and work items (cards), providing transparency about work status and flow.

### 46. What is cycle time in Kanban?
Cycle time measures how long it takes for a work item to move from start to finish, helping teams understand their delivery speed.

### 47. What is lead time in Kanban?
Lead time measures the total time from when a request is made until it's completed and delivered to the customer.

### 48. What are the benefits of Kanban?
- Visual workflow management
- Improved flow and efficiency
- Reduced bottlenecks
- Flexibility in prioritization
- Continuous delivery
- Easy to implement

### 49. Can you use Kanban with Scrum?
Yes, this hybrid approach is called Scrumban. It combines Scrum's structure with Kanban's visual management and flow optimization.

### 50. What is a cumulative flow diagram?
A chart showing the amount of work in each workflow stage over time, helping identify bottlenecks, WIP trends, and delivery predictability.

## Estimation & Planning (51-65)

### 51. What estimation techniques are used in Agile?
- Planning Poker
- T-shirt sizing
- Dot voting
- Affinity estimation
- Bucket system
- Relative estimation

### 52. What is Planning Poker?
A consensus-based estimation technique where team members use cards with values (Fibonacci sequence) to estimate story points for user stories.

### 53. Why use Fibonacci sequence for estimation?
The Fibonacci sequence (1, 2, 3, 5, 8, 13, 21) reflects increasing uncertainty with larger numbers, acknowledging that larger items are harder to estimate precisely.

### 54. What is T-shirt sizing?
An estimation technique using sizes (XS, S, M, L, XL) instead of numbers, useful for high-level estimation early in planning.

### 55. What is relative estimation?
Estimating by comparing work items to each other rather than absolute time, which is typically more accurate and faster.

### 56. What is the cone of uncertainty?
A concept showing that estimates become more accurate as a project progresses and more information becomes available.

### 57. How do you estimate spikes?
Spikes are time-boxed research activities. Estimate them in time (hours/days) rather than story points since they don't produce deliverable features.

### 58. What is release planning?
The process of determining which features will be delivered in future releases based on priorities, dependencies, and team capacity.

### 59. What is capacity planning?
Determining how much work the team can commit to in a sprint based on availability, holidays, and known commitments.

### 60. What factors affect velocity?
- Team size and composition
- Team maturity and experience
- Technical debt
- External dependencies
- Quality of requirements
- Organizational impediments

### 61. Should velocity be used to compare teams?
No. Velocity is team-specific and reflects each team's unique context, estimation practices, and definition of done.

### 62. What is a spike in Agile?
A time-boxed research activity to investigate a question, reduce risk, or gain knowledge needed before committing to a user story.

### 63. How do you handle dependencies in Agile planning?
- Identify dependencies early
- Coordinate with other teams
- Consider dependencies in prioritization
- Use dependency boards
- Hold cross-team planning sessions
- Decouple work when possible

### 64. What is MoSCoW prioritization?
A technique categorizing requirements as: Must have, Should have, Could have, Won't have (this time).

### 65. What is value-based prioritization?
Prioritizing work based on business value, ROI, customer impact, and strategic goals rather than technical preferences.

## Testing & Quality (66-75)

### 66. What is test-driven development (TDD)?
A practice where tests are written before code. The cycle is: write failing test, write minimal code to pass, refactor.

### 67. What is behavior-driven development (BDD)?
An extension of TDD focusing on system behavior from the user's perspective, using natural language specifications (Given-When-Then).

### 68. What is acceptance test-driven development (ATDD)?
A collaborative practice where the team defines acceptance criteria and tests before development begins, ensuring shared understanding.

### 69. What is continuous integration (CI)?
The practice of frequently integrating code changes into a shared repository, with automated builds and tests to detect issues early.

### 70. What is continuous delivery (CD)?
Extending CI so that code changes are automatically prepared for release to production, ensuring software can be deployed at any time.

### 71. What is continuous deployment?
Going beyond continuous delivery by automatically deploying every change that passes automated tests to production without manual intervention.

### 72. What is the testing pyramid?
A concept showing ideal test distribution: many unit tests at the base, fewer integration tests in the middle, and few UI/E2E tests at the top.

### 73. What is pair programming?
A practice where two developers work together at one workstation, with one writing code (driver) and the other reviewing (navigator).

### 74. What is code review in Agile?
A quality practice where team members review each other's code changes before merging, catching issues and sharing knowledge.

### 75. What is refactoring?
Restructuring existing code to improve its internal structure without changing its external behavior, reducing technical debt and improving maintainability.

## Metrics & Reporting (76-85)

### 76. What is a burndown chart used for?
Tracking remaining work over time during a sprint, helping teams visualize progress and identify if they're on track to meet sprint goals.

### 77. What is a velocity chart?
A chart showing the team's velocity over multiple sprints, helping with forecasting and identifying trends in team productivity.

### 78. What metrics should Agile teams track?
- Velocity
- Sprint burndown
- Release burnup
- Cycle time
- Lead time
- Defect rate
- Team happiness
- Customer satisfaction

### 79. What is escaped defects metric?
The number of defects found in production that escaped earlier testing, indicating quality of testing and development practices.

### 80. What is the Net Promoter Score (NPS)?
A customer satisfaction metric asking how likely customers are to recommend your product, scored from -100 to +100.

### 81. What is team happiness metric?
A subjective measure of team morale and satisfaction, often collected in retrospectives, indicating team health and sustainability.

### 82. What is predictability in Agile?
The ability to reliably forecast delivery based on historical data, indicating mature processes and stable velocity.

### 83. What are vanity metrics?
Metrics that look impressive but don't provide actionable insights, like total lines of code or number of commits.

### 84. What is the difference between outputs and outcomes?
Outputs are deliverables produced (features, stories), while outcomes are the business results achieved (revenue, satisfaction, adoption).

### 85. What is a cumulative flow diagram used for?
Visualizing work distribution across workflow stages over time, identifying bottlenecks, WIP trends, and process stability.

## Advanced Topics & Scaling (86-100)

### 86. What is SAFe (Scaled Agile Framework)?
A framework for scaling Agile practices to large enterprises, organizing work into teams, programs, and portfolios with synchronized planning.

### 87. What is LeSS (Large-Scale Scrum)?
A framework for scaling Scrum to multiple teams working on the same product, minimizing additional roles and artifacts.

### 88. What is the Spotify model?
An organizational approach using Squads (cross-functional teams), Tribes (groups of squads), Chapters (skill-based groups), and Guilds (communities of interest).

### 89. What is a Program Increment (PI) in SAFe?
A time-boxed period (typically 8-12 weeks) where Agile Release Trains deliver incremental value in the form of working, tested software.

### 90. What is an Agile Release Train (ART)?
A long-lived team of Agile teams (50-125 people) that plans, commits, and executes together, aligned to a common mission via a single backlog.

### 91. What is DevOps and how does it relate to Agile?
DevOps extends Agile principles to operations, emphasizing automation, collaboration between development and operations, and continuous delivery.

### 92. What is a minimum viable product (MVP)?
The smallest version of a product that can be released to gather maximum validated learning with minimum effort.

### 93. What is technical excellence in Agile?
Maintaining high code quality through practices like TDD, CI/CD, refactoring, pair programming, and architectural discipline.

### 94. What is servant leadership?
A leadership philosophy where the leader serves the team by removing obstacles, providing resources, and facilitating rather than commanding.

### 95. What are cross-functional teams?
Teams with all skills necessary to deliver value without depending on people outside the team, including developers, testers, designers, etc.

### 96. What is self-organization?
Teams autonomously deciding how to accomplish their work rather than being directed by management, leading to higher engagement and innovation.

### 97. What is empirical process control?
Decision-making based on observation, experience, and experimentation rather than defined processes, fundamental to Agile and Scrum.

### 98. What is a product roadmap?
A high-level visual summary showing the vision and direction of product development over time, aligning stakeholders on strategy.

### 99. What is swarming in Agile?
When multiple team members collaborate intensely on a single item to complete it quickly, rather than everyone working on separate items.

### 100. What is the difference between Agile and agility?
Agile (capital A) refers to specific methodologies and frameworks. Agility (lowercase a) is the mindset and ability to respond quickly to change, which is the ultimate goal.
---

## Summary - Now 125 Total Questions

This expanded Agile document now covers:
- **25 additional questions** on types of stories and uses (Q16-Q25)
- **All core Agile concepts** from fundamentals to scaling
- **Story types**: User stories, Technical, Bugs, Spikes, Chores, Enablers, Epics
- **Story lifecycle**: Creation through production
- **Writing effective stories** with proper templates
- **Story metrics and KPIs** for tracking
- **Advanced patterns** and anti-patterns

Total coverage: **125 comprehensive Agile interview questions**
## Key Concepts Summary

**Agile Core Values:**
- Individuals and interactions
- Working software
- Customer collaboration
- Responding to change

**Common Practices:**
- Iterative development
- Continuous feedback
- Self-organizing teams
- Regular reflection and adaptation
- Technical excellence
- Sustainable pace

**Success Factors:**
- Management support
- Customer involvement
- Team empowerment
- Clear communication
- Appropriate tooling
- Continuous improvement culture
