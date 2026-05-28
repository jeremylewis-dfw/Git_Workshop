# 3. Common Workflows

**← [Back to Index](00%20-%20Index.md)** | **[Previous: Fundamental Concepts](02%20-%20Fundamental%20Concepts.md)** | **[Next: Practical Scenarios →](04%20-%20Practical%20Scenarios.md)**

---

## Quick Links
- [Solo Developer Workflow](#31-solo-developer-workflow) - Working alone with branches
- [Team Collaboration Patterns](#32-team-collaboration-patterns) - Working with pull requests
- [Feature vs Hotfix](#33-feature-vs-hotfix) - Different branch strategies

---

## 3.1 Solo Developer Workflow

### The Simple Case: Local Branching

When you're building a personal project, you still get enormous benefits from Git, even without remote collaboration.

### Setup: Your First Repository

```bash
# Initialize a new project as a Git repository
cd my-project
git init

# Check status (nothing committed yet)
git status

# Stage your initial files
git add .

# Make your first commit
git commit -m "Initial project setup"

# Create a local branch to work on
git checkout -b main
```

Now you have:
- A `main` branch (your stable code)
- Local Git history
- Complete ability to undo any mistake

### Daily Workflow: Branching for Experimentation

Let's say you're building a todo app and want to try implementing a new feature (offline support):

```bash
# Start on main (your stable version)
git checkout main

# Create a feature branch
git checkout -b feature/offline-support

# Make commits as you work
echo "// Offline sync logic" >> app.js
git add app.js
git commit -m "Add offline sync mechanism"

# Continue working on the branch
# Make more edits, more commits
git add .
git commit -m "Add offline data persistence"

# Test thoroughly, make sure it works
npm test

# Feature is complete and tested
# Merge it back to main
git checkout main
git merge feature/offline-support

# Delete the feature branch (it was temporary)
git branch -d feature/offline-support

# Now both features coexist on main
```

### Recovery: Undo Without Losing Everything

**Scenario 1: You made a breaking change but haven't committed it yet**

```bash
# You edited a file and broke everything
# But the file isn't committed yet

# See what's changed
git status
# Shows file.js is modified

# Restore it to the last committed version
git restore file.js
# file.js is back to working state

# Work lost (last 10 minutes): 0 (you had committed regularly)
```

**Scenario 2: You committed something that broke things**

```bash
# You committed a bad change
git log --oneline
# abc123d Add feature
# def456g Previous feature
# etc...

# Undo the bad commit
git revert abc123d

# Git creates a new commit that undoes abc123d
# You're back to working state
# History is preserved (both commits visible)
```

**Scenario 3: You want to go back in time and continue from an old version**

```bash
# Switch to an old commit
git checkout def456g

# You're now at that point in history
# You can see old code, test old versions

# If you want to start a new branch from here
git checkout -b bugfix/old-issue
# Make commits, fix the bug

# Later merge it in or continue independently
```

### Best Practices: Solo Developer

1. **Commit frequently** (every 15-30 minutes)
   - Granular recovery options
   - Natural checkpoint system

2. **Use branches for features** (even alone)
   - `feature/X`, `bugfix/Y`, `experiment/Z`
   - Organize your work mentally
   - Easy to see what you're working on

3. **Keep main stable**
   - Only merge tested, working code to main
   - main is your "last known good" version
   - Gives you safety net

4. **Write meaningful commit messages**
   - Future you will thank present you
   - Understanding evolution is valuable
   - Good practice before joining a team

---

## 3.2 Team Collaboration Patterns

### The Pull Request Workflow

This is how professional teams use Git. The key concept: **code review before anything merges to main**.

### Step-by-Step: Adding a Feature as Team Member

**Developer: Alice wants to implement user profiles**

```bash
# Step 1: Create a feature branch
git checkout -b feature/user-profiles

# Step 2: Make commits
git add src/ProfileComponent.jsx
git commit -m "Create user profile component"

git add src/api/users.js
git commit -m "Add API endpoint for user profiles"

git add tests/ProfileComponent.test.jsx
git commit -m "Add profile component tests"

# Step 3: Push to remote
git push -u origin feature/user-profiles
# Remote now has your branch
```

**On GitHub/GitLab/Bitbucket:**

Alice creates a "Pull Request" (or "Merge Request"). This is:
- A request to merge her feature branch into main
- A code review interface
- A discussion forum for feedback
- An automated test verification point

**Reviewers (Bob and Carol): Review the code**

Bob looks at the PR and sees:
- All changes in one visual interface
- Diff showing exactly what changed
- Can comment on specific lines

Bob's review:
```
Line 15 (in ProfileComponent.jsx):
"You're fetching on every render. 
This will cause performance issues. 
Use useEffect with dependency array instead."

Carol:
"Good point. Also, what about error handling 
if the API call fails?"
```

**Alice: Address feedback**

```bash
# Alice makes the changes locally
git add src/ProfileComponent.jsx
git commit -m "Fix: use useEffect for profile fetch, handle errors"

# Push the new commit
git push origin feature/user-profiles
# PR automatically updates (new commit appears)
```

**Automated Checks**

When Alice pushed, the remote automatically:
- Runs linter on the code
- Runs unit tests
- Runs integration tests
- Checks code coverage
- All must pass before merge is allowed

```
✅ ESLint: Passed
✅ Unit Tests: 15/15 passed
✅ Integration Tests: 8/8 passed
✅ Code Coverage: 89% (above minimum 80%)
✅ Security Scan: No vulnerabilities detected

Status: Ready to Merge ✓
```

**Approval and Merge**

Once two people approve (Alice excluded), the PR is merged:

```bash
# Automatic merge performed by GitHub
# feature/user-profiles → main

# Alice's commits are now part of main
# feature/user-profiles branch is deleted
```

**Timeline in the repository:**

```
main:     A────B─────C─────D──
          │         │     │
old:      A────B     │     │ (where main was)
                 └──E─F─G─┘  (feature/user-profiles)
                  (review)
                      
After merge:
main:     A────B─────C─────D──E─F─G
```

### Resolving Merge Conflicts in a Team

**Scenario:** Alice and Bob both edit the same file

```bash
# Bob finishes first, merges to main
main:     A────B────C (Bob's changes)

# Alice is still on her branch
# When she tries to merge her changes (which overlap with Bob's),
# Git detects a conflict

# Alice pulls latest main to sync up
git fetch origin
git merge origin/main

# Git reports a conflict
CONFLICT: Merge conflict in app.js
```

**In app.js, Git shows both versions:**

```javascript
// Original code
function getUser() {
<<<<<<< HEAD
  // Alice's changes
  const user = await api.getUser(userId);
  return user.profile;
=======
  // Bob's changes
  const user = await db.query('users', userId);
  return user;
>>>>>>> origin/main
}
```

**Alice resolves it:**

```javascript
// Alice decides: combine both approaches
function getUser() {
  const user = await api.getUser(userId);
  if (!user) {
    throw new Error('User not found');
  }
  return user.profile;
}
```

She removes the conflict markers, keeps the right code:

```bash
# Tell Git the conflict is resolved
git add app.js

# Complete the merge
git commit -m "Merge main: resolve getUserUser API/DB implementation"

# Push the resolved merge
git push
```

### Best Practices: Team Collaboration

1. **Keep branches short-lived** (2-5 days max)
   - Reduces chance of conflicts
   - Makes code review manageable
   - Faster feature integration

2. **Pull frequently from main**
   - `git pull origin main` daily
   - Catch conflicts early
   - Your branch doesn't diverge too far

3. **Require code review**
   - Never merge own code to main
   - Catches bugs before production
   - Shares knowledge across team

4. **Automate checks**
   - Tests run on every PR
   - Linting enforced
   - Coverage requirements met
   - Bad code can't merge

5. **Meaningful branch names**
   - `feature/` for new features
   - `bugfix/` for bug fixes
   - `hotfix/` for production emergencies
   - `chore/` for maintenance

6. **One change per pull request**
   - PR has one clear purpose
   - Reviewers understand context
   - Easier to revert if needed

---

## 3.3 Feature vs Hotfix: Different Branch Strategies

### Feature Branches: Planned Development

Used for new features that you work on for several days:

```bash
git checkout -b feature/dashboard-redesign

# Work on it for 2-3 days
# Make many commits
# Get feedback from designers

# Once merged, it goes to staging for a week
# Then deployed in next weekly release

# Timeline:
Monday start → Friday review → Sunday staging → Monday production
```

**Characteristics:**
- Longer-lived (typically 2-5 days)
- Comprehensive code review
- Coordinated testing
- Planned integration

### Bugfix Branches: Planned Fixes

Used for bugs found in development:

```bash
git checkout -b bugfix/auth-token-expiration

# Fix takes a day or less
# Merged to main
# Goes to staging

# Timeline: 
Found Tuesday morning → Review Tuesday afternoon → Staging and testing → Deployment
```

**Characteristics:**
- Short-lived (typically 1 day)
- Focused scope (one bug)
- Faster review process
- Integrated with next release

### Hotfix Branches: Emergency Response

Used for critical bugs **already in production**:

```bash
# Production is broken. We need to fix NOW.
git checkout -b hotfix/payment-processing-down

# Create off main (current production version)
# Fix the critical bug (usually 30 minutes to 2 hours)
# Code review is expedited (still reviewed, but urgent)
# Deploy to production immediately

# Also merge back to main so development has the fix

# Timeline:
Issue detected → Branch created → Fixed → Reviewed → Deployed (total: 1-2 hours)
```

**Characteristics:**
- Created from main branch
- Fixed as fast as possible
- Deployed immediately to production
- Also merged to development branches
- Urgent code review

### Branch Model Visualization

```
Timeline:                    Deployment to Production
                            ↓
main:     ──A──B──────E──────J─────
          ├─────C─D──┘ (feature/dashboard)
          └─F─────G─┴─ (bugfix/auth - merged)
                   ↑ 
                   H 
                   │
                   hotfix/payment (emergency!)
                   Deployed immediately
```

---

## Summary: Workflows in Different Scenarios

| Scenario | Branch Type | Duration | Review | Deployment |
|----------|-------------|----------|--------|------------|
| Solo developer | experimental | Days | Self | When ready |
| New feature | feature/ | 2-5 days | Formal PR | Planned |
| Bug fix | bugfix/ | 1 day | PR review | Planned |
| Production issue | hotfix/ | 1-2 hours | Expedited | Immediate |

---

## Next Steps

Now that you understand workflows, let's look at real problems that come up:

→ **[Next: Practical Scenarios & Solutions](04%20-%20Practical%20Scenarios.md)**

---

**← [Back to Index](00%20-%20Index.md)**
