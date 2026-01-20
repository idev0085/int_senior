# Git - Senior Developer to Architect Level Q&A

## Table of Contents
1. [Core Concepts & Workflows](#core-concepts--workflows)
2. [Branching Strategies](#branching-strategies)
3. [Advanced Commands](#advanced-commands)
4. [Collaboration & Code Review](#collaboration--code-review)
5. [Troubleshooting & Recovery](#troubleshooting--recovery)
6. [Best Practices](#best-practices)

---

## Core Concepts & Workflows

### Q1: Explain Git's internal architecture. How does Git store data?

**Answer:**

Git stores data like a file system with snapshots, not differences.

**Simple Analogy:**
- **Other VCS**: Save only changes (like editing history in a document)
- **Git**: Save complete snapshots (like taking photos of your entire project)

**Git Objects:**

**1. Blob (Binary Large Object):**
Stores file contents.

```bash
# Create a blob
echo "Hello World" | git hash-object -w --stdin
# Returns: 557db03de997c86a4a028e1ebd3a1ceb225be238

# View blob content
git cat-file -p 557db03
# Output: Hello World
```

**2. Tree:**
Stores directory structure.

```bash
# View tree
git cat-file -p master^{tree}
# Output:
# 100644 blob 557db03... file.txt
# 040000 tree 8b1e657... src/
```

**3. Commit:**
Points to a tree and parent commits.

```bash
git cat-file -p HEAD
# Output:
# tree 8b1e657...
# parent a1b2c3d...
# author John Doe <john@example.com> 1234567890 +0000
# committer John Doe <john@example.com> 1234567890 +0000
#
# Commit message
```

**4. Tag:**
References a specific commit.

**Git Directory Structure:**
```
.git/
├── objects/          # All content (blobs, trees, commits)
├── refs/             # Pointers to commits (branches, tags)
│   ├── heads/        # Local branches
│   └── remotes/      # Remote branches
├── HEAD              # Current branch pointer
├── index             # Staging area
└── config            # Repository config
```

---

### Q2: What is the difference between merge, rebase, and squash? When should each be used?

**Answer:**

**1. Merge (Preserve History):**
Creates a new commit that combines two branches.

```bash
git checkout main
git merge feature-branch

# Result:
#     A---B---C main
#    /         \
#   D---E---F---M feature + merge commit
```

**Pros:**
- Preserves complete history
- Non-destructive
- Shows branch relationships

**Cons:**
- Creates extra merge commits
- History can become cluttered

**When to use:**
- Main branch merges
- When history is important
- Public branches

**2. Rebase (Linear History):**
Moves branch to tip of another branch.

```bash
git checkout feature-branch
git rebase main

# Before:
#     A---B---C main
#    /
#   D---E---F feature-branch

# After:
#     A---B---C main
#              \
#               D'---E'---F' feature-branch (rewritten commits)
```

**Pros:**
- Clean, linear history
- Easier to follow
- No merge commits

**Cons:**
- Rewrites history (dangerous on shared branches)
- Conflicts must be resolved per commit

**When to use:**
- Feature branches before merging
- Personal branches
- Cleaning up local history

**Golden Rule:** Never rebase public/shared branches!

**3. Squash (Combine Commits):**
Combines multiple commits into one.

```bash
git merge --squash feature-branch
git commit -m "Feature: Add login functionality"

# Before:
#   D: WIP commit
#   E: Fix typo
#   F: More changes
#   G: Final changes

# After:
#   M: Feature: Add login functionality (all changes combined)
```

**When to use:**
- Feature has messy commit history
- Want single commit in main branch
- Pull request merges

**Comparison Table:**

| Method | History | Conflicts | Public Branches | Use Case |
|--------|---------|-----------|-----------------|----------|
| Merge | Preserves | Once | Safe | Main branch merges |
| Rebase | Linear | Per commit | Dangerous | Feature branch cleanup |
| Squash | Single commit | Once | Safe | Clean PR merges |

---

## Branching Strategies

### Q3: Compare Git Flow, GitHub Flow, and Trunk-Based Development. Which should you use?

**Answer:**

**1. Git Flow (Complex Projects):**

```
main (production)
  ↓
develop (integration)
  ↓
feature/* (features)
release/* (release prep)
hotfix/* (urgent fixes)
```

**Workflow:**
```bash
# Feature development
git checkout develop
git checkout -b feature/login
# ... make changes
git checkout develop
git merge --no-ff feature/login

# Release
git checkout -b release/1.0.0 develop
# ... final fixes, version bump
git checkout main
git merge --no-ff release/1.0.0
git tag -a v1.0.0
git checkout develop
git merge --no-ff release/1.0.0

# Hotfix
git checkout -b hotfix/security-fix main
# ... fix
git checkout main
git merge --no-ff hotfix/security-fix
git tag -a v1.0.1
git checkout develop
git merge --no-ff hotfix/security-fix
```

**Pros:**
- Well-defined process
- Multiple versions in production
- Clear release cycles

**Cons:**
- Complex
- Merge commits overhead
- Slower

**When to use:**
- Desktop software with scheduled releases
- Mobile apps with app store review
- Multiple production versions

---

**2. GitHub Flow (Simple, Fast):**

```
main (production)
  ↓
feature/* (short-lived)
```

**Workflow:**
```bash
# Create feature branch
git checkout -b feature/add-button main

# Make changes and push
git push -u origin feature/add-button

# Open Pull Request
# Code review happens
# CI/CD tests run

# Merge to main (auto-deployed)
# Delete feature branch
```

**Pros:**
- Simple
- Fast deployment
- Continuous delivery friendly

**Cons:**
- No release branches
- Main must always be deployable
- Not ideal for scheduled releases

**When to use:**
- Web applications
- SaaS products
- Continuous deployment
- Small to medium teams

---

**3. Trunk-Based Development (Fastest):**

```
main (always deployable)
  ↓
short-lived branches (< 1 day)
```

**Workflow:**
```bash
# Create short branch
git checkout -b fix-button main

# Make small change
# Merge quickly (same day)
git checkout main
git merge fix-button
git push

# Or commit directly to main (with CI/CD safety)
git commit -m "Fix button alignment"
git push
```

**Requirements:**
- Feature flags for incomplete features
- Excellent CI/CD
- Comprehensive tests
- Small commits

**Pros:**
- Fastest integration
- No merge hell
- Encourages small changes
- Forces good practices

**Cons:**
- Requires mature CI/CD
- Needs feature flags
- Team discipline required

**When to use:**
- Mature teams
- Strong testing culture
- Continuous deployment
- Google, Facebook scale

---

**Decision Matrix:**

| Project Type | Recommended Strategy |
|--------------|---------------------|
| Enterprise software with releases | Git Flow |
| Web app with continuous deployment | GitHub Flow |
| High-velocity web app | Trunk-Based |
| Open source project | GitHub Flow |
| Mobile app | Git Flow |

---

### Q4: How do you handle feature flags and long-running branches?

**Answer:**

**Problem:** Long-running branches cause merge conflicts and integration issues.

**Solution:** Use feature flags to merge incomplete code to main.

**Implementation:**

```javascript
// config/features.js
module.exports = {
  newCheckout: process.env.FEATURE_NEW_CHECKOUT === 'true',
  betaFeatures: process.env.FEATURE_BETA === 'true',
  // Runtime flags from database
  async getDynamicFlags(userId) {
    return await FeatureFlag.findAll({ where: { userId } });
  }
};

// In code
if (features.newCheckout) {
  return <NewCheckoutComponent />;
} else {
  return <OldCheckoutComponent />;
}

// Gradual rollout
async function isFeatureEnabled(featureName, userId) {
  const flag = await FeatureFlag.findOne({ 
    where: { name: featureName } 
  });
  
  if (!flag.enabled) return false;
  
  // Rollout to 10% of users
  if (flag.rolloutPercentage < 100) {
    const userHash = hashUserId(userId);
    return (userHash % 100) < flag.rolloutPercentage;
  }
  
  return true;
}
```

**Benefits:**
- Merge to main frequently
- No merge conflicts
- Test in production
- Gradual rollout
- Easy rollback

**Feature Flag Lifecycle:**
```bash
# 1. Add flag (disabled)
git checkout main
git checkout -b feature/new-checkout
# Add code with feature flag (disabled by default)
git commit -m "Add new checkout (behind flag)"
git push

# 2. Merge to main
# Feature is in production but disabled!

# 3. Enable for testing (10% users)
UPDATE feature_flags SET rollout_percentage = 10 WHERE name = 'new_checkout';

# 4. Monitor, increase gradually
UPDATE feature_flags SET rollout_percentage = 50 WHERE name = 'new_checkout';

# 5. Full rollout
UPDATE feature_flags SET rollout_percentage = 100 WHERE name = 'new_checkout';

# 6. Remove flag (cleanup)
git checkout -b remove-checkout-flag
# Remove if/else, delete old code
git commit -m "Remove new checkout feature flag"
```

---

## Advanced Commands

### Q5: Explain git rebase --interactive and when to use it.

**Answer:**

Interactive rebase is like editing your commit history before publishing.

**Common Use Cases:**

**1. Squash commits:**
```bash
git log --oneline
# abc123 Fix typo
# def456 More changes
# ghi789 WIP
# jkl012 Start feature

git rebase -i HEAD~4

# Editor opens:
pick jkl012 Start feature
squash ghi789 WIP
squash def456 More changes
squash abc123 Fix typo

# Result: One clean commit!
```

**2. Reorder commits:**
```bash
git rebase -i HEAD~3

# Change order in editor:
pick 222 Add feature B
pick 111 Add feature A
pick 333 Add feature C
```

**3. Edit commit messages:**
```bash
git rebase -i HEAD~3

# Change pick to reword:
reword abc123 Bad message
pick def456 Good message

# Editor opens for each 'reword'
```

**4. Split a commit:**
```bash
git rebase -i HEAD~3

# Change pick to edit:
edit abc123 Large commit

# Git pauses:
git reset HEAD~
git add file1.js
git commit -m "Part 1"
git add file2.js
git commit -m "Part 2"
git rebase --continue
```

**5. Drop commits:**
```bash
git rebase -i HEAD~3

# Delete line or change to 'drop':
drop abc123 Unwanted commit
pick def456 Keep this
```

**Interactive Rebase Commands:**
- `pick` = keep commit as-is
- `reword` = change commit message
- `edit` = pause to amend commit
- `squash` = combine with previous commit
- `fixup` = like squash but discard message
- `drop` = remove commit

**Example Workflow:**
```bash
# Before:
# * abc123 WIP
# * def456 Fix bug
# * ghi789 Oops typo
# * jkl012 Add login feature
# * mno345 Another fix

git rebase -i HEAD~5

# In editor:
pick jkl012 Add login feature
fixup ghi789 Oops typo
fixup abc123 WIP
pick def456 Fix bug
reword mno345 Another fix

# After:
# * pqr678 Improve error handling (reworded)
# * stu901 Fix bug
# * vwx234 Add login feature (combined 3 commits)
```

**Warning:** Only rebase commits that haven't been pushed to shared branches!

---

### Q6: How do you use git bisect to find bugs?

**Answer:**

Git bisect uses binary search to find the commit that introduced a bug.

**Simple Analogy:**
Like playing "higher or lower" to find a number. Git asks "Is the bug here?" and narrows down based on your answer.

**Manual Bisect:**
```bash
# Start bisect
git bisect start

# Mark current commit as bad
git bisect bad

# Mark last known good commit
git bisect good v1.0.0

# Git checks out middle commit
# Test your code...

# If bug exists:
git bisect bad

# If bug doesn't exist:
git bisect good

# Repeat until Git finds the culprit commit
# Bisecting: X revisions left to test after this

# When found:
# abc123 is the first bad commit
# commit abc123...
# Author: John Doe
# Date: ...
# Message: Refactor authentication

# End bisect
git bisect reset
```

**Automated Bisect:**
```bash
# Write a test script
# test.sh
#!/bin/bash
npm test
exit $?

# Make executable
chmod +x test.sh

# Run automated bisect
git bisect start
git bisect bad HEAD
git bisect good v1.0.0
git bisect run ./test.sh

# Git automatically tests each commit!
# Result: Finds bug in minutes
```

**Real-World Example:**
```bash
# Bug: Login stopped working sometime last month

# Find last working version
git log --since="1 month ago" --oneline

# Start bisect
git bisect start HEAD abc123  # current (bad) to old commit (good)

# Test each commit Git shows
# After ~6-7 tests (log2(100) for 100 commits)
# Git finds: commit def456 "Refactor auth middleware"

# Fix the bug
git bisect reset
git checkout main
git revert def456  # or fix and commit
```

**Benefits:**
- Finds bugs in O(log n) time
- Works on thousands of commits
- Automated testing
- No need to remember when bug started

---

## Collaboration & Code Review

### Q7: What are best practices for writing commit messages and PR descriptions?

**Answer:**

**Commit Message Format:**

```
<type>(<scope>): <subject>

<body>

<footer>
```

**Example:**
```bash
git commit -m "feat(auth): add OAuth2 login support

- Integrate with Google and GitHub OAuth providers
- Add user profile creation from OAuth data
- Implement token refresh mechanism

Closes #123
Breaking Change: Removes basic auth endpoint
```

**Types:**
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation
- `style`: Formatting (no code change)
- `refactor`: Code change (no feature/fix)
- `test`: Adding tests
- `chore`: Maintenance (dependencies, build)
- `perf`: Performance improvement

**Good Commit Messages:**
```bash
✅ "fix: prevent crash when user object is null"
✅ "feat: add user profile image upload"
✅ "refactor: extract validation logic to utils"
✅ "perf: optimize database query with indexing"

❌ "fixed stuff"
❌ "updates"
❌ "WIP"
❌ "asdfasdf"
```

**Atomic Commits:**
One logical change per commit.

```bash
# ❌ Bad: Multiple changes in one commit
git add .
git commit -m "Add feature and fix bugs and update docs"

# ✅ Good: Separate commits
git add src/feature.js
git commit -m "feat: add new feature"

git add src/bug-fix.js
git commit -m "fix: resolve null pointer error"

git add README.md
git commit -m "docs: update installation instructions"
```

**Pull Request Description Template:**

```markdown
## Description
Brief description of changes

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Breaking change
- [ ] Documentation update

## Changes Made
- Added user authentication
- Updated API endpoints
- Fixed memory leak in worker threads

## Testing
- [ ] Unit tests pass
- [ ] Integration tests pass
- [ ] Manual testing completed

## Screenshots (if applicable)
[Add screenshots]

## Checklist
- [ ] Code follows style guidelines
- [ ] Self-review completed
- [ ] Comments added for complex logic
- [ ] Documentation updated
- [ ] No new warnings generated

## Related Issues
Closes #123
Relates to #456
```

---

### Q8: How do you handle code reviews effectively?

**Answer:**

**As Author:**

**1. Before Creating PR:**
```bash
# Self-review first
git diff main...your-branch

# Run tests
npm test

# Run linter
npm run lint

# Check for secrets
git diff main | grep -i "password\|secret\|api_key"
```

**2. Create Small PRs:**
```bash
# ❌ Bad: 50 files, 3000 lines changed
# ✅ Good: 5 files, 200 lines changed

# Break large features into small PRs:
# PR1: Add database models
# PR2: Add API endpoints
# PR3: Add frontend UI
# PR4: Add tests
```

**3. Provide Context:**
```markdown
## Context
User complained about slow page load times.
Profiling showed N+1 query issue.

## Solution
Added eager loading for related data.
Reduced queries from 100+ to 3.

## Performance Impact
Page load time: 2.5s → 0.3s
```

**As Reviewer:**

**1. Review Checklist:**
```markdown
## Code Quality
- [ ] Code is readable and maintainable
- [ ] No duplicated code
- [ ] Functions are small and focused
- [ ] Variable names are descriptive

## Functionality
- [ ] Code does what it's supposed to
- [ ] Edge cases handled
- [ ] Error handling present

## Performance
- [ ] No obvious performance issues
- [ ] Database queries optimized
- [ ] No memory leaks

## Security
- [ ] Input validation present
- [ ] No SQL injection vulnerabilities
- [ ] Secrets not hardcoded
- [ ] Authentication/authorization correct

## Testing
- [ ] Tests cover new code
- [ ] Tests cover edge cases
- [ ] All tests pass

## Documentation
- [ ] Code comments for complex logic
- [ ] README updated if needed
- [ ] API docs updated
```

**2. Constructive Feedback:**
```bash
# ❌ Bad comments:
"This is wrong"
"Why did you do this?"
"This is terrible"

# ✅ Good comments:
"Consider using Array.map() instead of forEach for this use case"
"Could we extract this into a helper function for reusability?"
"Great solution! One edge case: what happens if user is null?"
"Nitpick: Consider renaming 'data' to 'userData' for clarity"
```

**3. Approval Levels:**
```markdown
✅ LGTM (Looks Good To Me) - Approve
💬 Comment - Discussion needed but not blocking
🚫 Request Changes - Must be addressed before merge
🤔 Question - Need clarification
```

**GitHub Code Review Tools:**
```bash
# Suggest specific changes
```suggestion
const result = items.map(item => item.value);
```

# Request changes
Please add input validation before the database query.

# Ask questions
What happens if the API call fails here?
```

---

## Troubleshooting & Recovery

### Q9: How do you recover from common Git mistakes?

**Answer:**

**1. Undo Last Commit (Not Pushed):**
```bash
# Keep changes in working directory
git reset --soft HEAD~1

# Keep changes in staging
git reset HEAD~1

# Discard changes completely
git reset --hard HEAD~1
```

**2. Amend Last Commit:**
```bash
# Fix commit message
git commit --amend -m "New message"

# Add forgotten files
git add forgotten-file.js
git commit --amend --no-edit
```

**3. Recover Deleted Commit:**
```bash
# Show all actions
git reflog

# Output:
# abc123 HEAD@{0}: reset: moving to HEAD~1
# def456 HEAD@{1}: commit: Important work

# Restore deleted commit
git reset --hard def456
```

**4. Undo git reset --hard:**
```bash
# Thought you lost everything?
git reflog

# Find commit before reset
git reset --hard HEAD@{1}
```

**5. Revert Pushed Commit:**
```bash
# ❌ Don't do this on public branches:
git reset --hard HEAD~1
git push --force

# ✅ Do this instead:
git revert abc123
git push

# Creates new commit that undoes changes
```

**6. Recover Deleted Branch:**
```bash
# Deleted branch by mistake
git branch -D feature-branch

# Find it in reflog
git reflog

# Recreate branch
git checkout -b feature-branch abc123
```

**7. Undo git add (Unstage):**
```bash
# Unstage single file
git reset file.js

# Unstage all
git reset
```

**8. Discard Local Changes:**
```bash
# Single file
git checkout -- file.js

# Or with newer syntax
git restore file.js

# All files
git reset --hard HEAD
```

**9. Recover Stashed Changes:**
```bash
# Stash changes
git stash

# List stashes
git stash list

# Apply stash
git stash apply stash@{0}

# Apply and remove
git stash pop

# Recover deleted stash
git fsck --no-reflog | grep commit
```

**10. Fix Merge Conflicts:**
```bash
# During merge
git merge feature-branch
# CONFLICT in file.js

# Fix conflicts manually, then:
git add file.js
git commit

# Or abort merge
git merge --abort

# During rebase
git rebase main
# CONFLICT in file.js

# Fix, then:
git add file.js
git rebase --continue

# Or abort
git rebase --abort
```

---

### Q10: What is git reflog and how can it save you?

**Answer:**

Reflog is your safety net - it records every HEAD movement.

**Simple Analogy:**
Like a time machine that remembers everywhere your code has been.

**View Reflog:**
```bash
git reflog

# Output:
# abc123 HEAD@{0}: commit: Add feature
# def456 HEAD@{1}: checkout: moving from main to feature
# ghi789 HEAD@{2}: reset: moving to HEAD~1
# jkl012 HEAD@{3}: commit: Important work (deleted!)
```

**Use Cases:**

**1. Recover Accidentally Deleted Commit:**
```bash
git reset --hard HEAD~3  # Oops! Lost 3 commits

git reflog
# Shows: HEAD@{1} was before reset

git reset --hard HEAD@{1}  # Recovered!
```

**2. Recover Deleted Branch:**
```bash
git branch -D experimental

git reflog
# Find last commit on experimental branch

git checkout -b experimental abc123
```

**3. Undo Bad Rebase:**
```bash
git rebase main
# Conflicts everywhere, mess created

git reflog
# Find: HEAD@{1}: rebase started

git reset --hard HEAD@{1}  # Back to before rebase
```

**4. Find Lost Work:**
```bash
# "Where is my commit from yesterday?"

git reflog --date=relative
# Shows commits with timestamps
```

**Reflog Expiration:**
```bash
# Reflog entries expire after 90 days (default)
git config gc.reflogExpire  # Check current setting

# Keep reflog longer
git config gc.reflogExpire "1 year"

# Never expire
git config gc.reflogExpire "never"
```

**Note:** Reflog is local only, not pushed to remote!

---

## Best Practices

### Q11: What are Git hooks and how do you use them?

**Answer:**

Git hooks are scripts that run automatically on Git events.

**Location:** `.git/hooks/`

**Common Hooks:**

**1. pre-commit (Run tests before committing):**
```bash
#!/bin/sh
# .git/hooks/pre-commit

echo "Running tests..."
npm test

if [ $? -ne 0 ]; then
  echo "Tests failed! Commit aborted."
  exit 1
fi

echo "Running linter..."
npm run lint

if [ $? -ne 0 ]; then
  echo "Linting failed! Fix errors before committing."
  exit 1
fi
```

**2. commit-msg (Enforce commit message format):**
```bash
#!/bin/sh
# .git/hooks/commit-msg

commit_msg=$(cat "$1")

# Check format: type(scope): message
if ! echo "$commit_msg" | grep -qE "^(feat|fix|docs|style|refactor|test|chore)(\(.+\))?: .+"; then
  echo "Error: Commit message must follow format:"
  echo "  type(scope): message"
  echo "  Example: feat(auth): add login button"
  exit 1
fi
```

**3. pre-push (Prevent pushing to main):**
```bash
#!/bin/sh
# .git/hooks/pre-push

branch=$(git symbolic-ref HEAD | sed -e 's,.*/\(.*\),\1,')

if [ "$branch" = "main" ]; then
  echo "Direct push to main is not allowed!"
  echo "Please create a PR instead."
  exit 1
fi
```

**4. post-merge (Install dependencies after merge):**
```bash
#!/bin/sh
# .git/hooks/post-merge

# Check if package.json changed
if git diff-tree -r --name-only --no-commit-id ORIG_HEAD HEAD | grep --quiet "package.json"; then
  echo "package.json changed. Running npm install..."
  npm install
fi
```

**Sharing Hooks with Team:**
```bash
# Can't commit .git/hooks (not tracked)
# Solution: Create scripts folder

# project-root/scripts/hooks/pre-commit
#!/bin/sh
npm test && npm run lint

# Install for team:
# project-root/scripts/install-hooks.sh
#!/bin/bash
ln -sf ../../scripts/hooks/pre-commit .git/hooks/pre-commit
chmod +x .git/hooks/pre-commit

# Or use Husky (popular tool)
npm install husky --save-dev
npx husky install
npx husky add .husky/pre-commit "npm test"
```

**Using Husky:**
```json
// package.json
{
  "scripts": {
    "prepare": "husky install"
  },
  "devDependencies": {
    "husky": "^8.0.0",
    "lint-staged": "^13.0.0"
  },
  "lint-staged": {
    "*.{js,jsx,ts,tsx}": ["eslint --fix", "prettier --write"],
    "*.{json,md}": ["prettier --write"]
  }
}

// .husky/pre-commit
#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

npx lint-staged
npm test
```

---

## Summary

**Git Best Practices:**

1. **Commit Often**: Small, atomic commits
2. **Write Good Messages**: Descriptive, follow convention
3. **Branch Strategy**: Choose appropriate workflow (Git Flow, GitHub Flow, Trunk-Based)
4. **Code Review**: Thorough, constructive feedback
5. **Never Rewrite Public History**: No force push on shared branches
6. **Use Feature Flags**: Instead of long-running branches
7. **Automate**: Use hooks, CI/CD
8. **Learn Recovery**: reflog, bisect, reset, revert
9. **Keep Main Clean**: Always deployable
10. **Document**: Good README, PR descriptions

**Remember:** Git is a powerful tool. Master it, and you'll never fear losing code again!
