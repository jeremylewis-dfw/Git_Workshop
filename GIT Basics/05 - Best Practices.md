# 5. Best Practices & DevOps

**← [Back to Index](00%20-%20Index.md)** | **[Previous: Practical Scenarios](04%20-%20Practical%20Scenarios.md)** | **[Next: Git Internals →](06%20-%20Git%20Internals.md)**

---

## Quick Links
- [Commit Discipline](#51-commit-discipline) - Writing atomic, meaningful commits
- [Branch Strategy](#52-branch-strategy) - Naming and organizing branches
- [Team Agreements](#53-team-agreements) - .gitignore and code review standards
- [CI/CD Integration](#54-integration-with-cicd) - Automated testing and deployment

---

## 5.1 Commit Discipline

### The Rule of One Logical Change

Every commit should represent exactly one logical change.

**Anti-pattern (Bundled commit):**
```bash
git commit -m "Work from today"
# Contains: 
#  - Bug fix in authentication
#  - Refactor database layer
#  - Update styling
#  - Add documentation
#  - Fix typo in README
```

**Problem with this approach:**
- Can't easily revert just one change
- Code review is overwhelming
- History is incomprehensible
- Debugging takes forever

**Pattern (Atomic commits):**
```bash
# Commit 1: One bug fix
git commit -m "Fix: JWT token expiration handling

Previously tokens expired even on active sessions.
Now tokens refresh automatically within the refresh window."

# Commit 2: One refactor
git commit -m "Refactor: Extract database connection pooling

Move connection management to separate module.
Simplifies main application code and improves testability."

# Commit 3: One feature
git commit -m "Add: User preferences UI component

New component allows users to customize dashboard layout.
Includes form validation and error handling."
```

**Benefits:**
- Each commit has clear purpose
- Any commit can be reverted independently
- Code review is focused
- Debugging and bisecting work perfectly

### Atomic Commits in Practice

```bash
# Developer workflow:
# 1. Make changes to multiple files
# 2. Stage only related changes
# 3. Commit staged changes with focused message
# 4. Repeat for next logical change

# Example: Adding user authentication

# Change 1: Database schema
git add src/database/migrations/001_add_users.sql
git commit -m "Add users table to database schema"

# Change 2: API endpoints
git add src/api/auth.js
git commit -m "Add authentication endpoints (register/login/logout)"

# Change 3: Frontend component
git add src/components/LoginForm.jsx
git commit -m "Add login form component with validation"

# Change 4: Tests
git add tests/auth.test.js
git commit -m "Add authentication tests"

# Result: 4 focused commits, each can stand alone
```

### Meaningful Commit Messages

**Structure: Title + Body**

```
Brief one-line summary (50 chars max)

Detailed explanation of why this change was made.
Explain the problem being solved, not just the code changes.
Reference issues/tickets if applicable.

Fixes #123
Closes #456
```

**Good Examples:**

```
Add JWT refresh token mechanism

Previously, JWT tokens expired without refresh capability,
forcing users to re-login frequently. This implementation
adds automatic token refresh with a separate refresh token,
improving user experience for long sessions.

Fixes #2341
```

```
Refactor: Move database queries to repository layer

Extract database logic from service layer into dedicated
repository classes. Improves testability and reduces coupling.
Services now call repositories instead of executing queries
directly.
```

```
Fix: Handle null values in user profile serialization

Request failed when user.profile was null. Added null checks
before serializing profile fields. Also added defensive
programming in related fields.

Fixes #2156
```

**Bad Examples (avoid):**

```
Fix stuff
Update things
Random changes
WIP
Blah
```

### Commit Frequency

**Too infrequent (One commit per day):**
- Large commits hard to review
- Harder to recover from mistakes
- History is hard to understand

**Too frequent (Every line change):**
- Noise in history
- Hard to understand overall changes
- Testing each individual commit is tedious

**Sweet spot: Every 15-30 minutes**
- Natural checkpoints
- Focused changes
- Easy recovery
- Clean history

---

## 5.2 Branch Strategy

### Naming Conventions

**Format: `<type>/<description>`**

Types:
- `feature/` - New feature
- `bugfix/` - Bug fix
- `hotfix/` - Emergency fix
- `chore/` - Maintenance, dependencies
- `refactor/` - Code restructuring
- `docs/` - Documentation only
- `test/` - Test additions

**Examples:**
```
✅ Good:
feature/user-profiles
bugfix/auth-token-expiration
hotfix/payment-processing-crash
chore/update-dependencies
refactor/database-connection-pooling
docs/api-endpoint-documentation

❌ Bad:
fix
feature1
branch_from_alice
tmp
update
```

### Branch Lifetime

**Feature branches should be short-lived:**

```
Goals:
├─ Keep main stable
├─ Minimize merge conflicts
├─ Keep review scope manageable
├─ Enable rapid integration

Timeline per branch type:
├─ Feature: 2-5 days
├─ Bugfix: 1 day
├─ Hotfix: 1-2 hours
└─ Chore: A few hours
```

**Stale branch problem:**
```
Day 1:  feature/dashboard created from main
Day 2:  Other features merged to main (main changed)
Day 3:  feature/dashboard is now "stale" (out of sync)
Day 4:  Merge conflicts when trying to merge

Prevention: Keep branch in sync with main
git fetch origin
git rebase origin/main (daily or more)
```

### Main Branch Protection

**Golden Rule: main should always be deployable**

Setup on GitHub/GitLab:
- ✅ Require pull requests
- ✅ Require code review (2+ approvals)
- ✅ Require status checks to pass (tests, lint, security)
- ✅ Dismiss stale reviews when code changes
- ✅ Block force pushes
- ✅ Require branches to be up-to-date before merge

**Never:**
- Commit directly to main (always use PR)
- Force push to main
- Merge without review
- Merge failing tests

---

## 5.3 Team Agreements

### .gitignore Strategy

**Purpose:** Prevent accidental commits of files that shouldn't be tracked

**Common entries:**
```
# Dependencies
node_modules/
packages/
vendor/

# Environment variables (NEVER commit secrets!)
.env
.env.local
.env.*.local

# Build artifacts
dist/
build/
*.o
*.class

# IDE files
.vscode/
.idea/
*.swp
*.swo

# OS files
.DS_Store
Thumbs.db

# Logs
*.log
logs/

# Temporary files
tmp/
temp/
```

**Template approach:**
```bash
# Use pre-built templates for your stackTemplate
# Create custom rules for project-specific files
# Commit .gitignore to the repository
# Everyone uses same ignore rules
```

### Code Review Standards

**Pull Request expectations:**
- Clear title that explains change
- Description of what changed and why
- Links to related issues
- Before/after screenshots if UI changed
- Test coverage for new code

**Reviewer expectations:**
- Review within 24 hours
- Check logic, not just syntax
- Ask questions about unclear code
- Run tests locally if possible
- Approve or request changes

**Conflict resolution:**
- Disagreements don't block indefinitely
- Discuss in PR comments
- Escalate to team lead if needed
- Document decision for future team members

### Deployment Workflow

```
Timeline:
┌────────────────────────────────────────────┐
│ Developer creates feature branch           │
│         ↓                                   │
│ Makes commits (atomic, focused)            │
│         ↓                                   │
│ Creates pull request                       │
│         ↓                                   │
│ Automated tests run (must pass)            │
│         ↓                                   │
│ Code review (2+ approvals)                 │
│         ↓                                   │
│ Merge to main (automatic or manual)        │
│         ↓                                   │
│ CI/CD runs full test suite                 │
│         ↓                                   │
│ Deploy to staging for final verification   │
│         ↓                                   │
│ Deploy to production (automated or manual) │
│         ↓                                   │
│ Feature is live!                           │
└────────────────────────────────────────────┘
```

---

## 5.4 Integration with CI/CD

### Automated Testing on Commits

```yaml
# Example GitHub Actions workflow (.github/workflows/test.yml)
name: Tests

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Install dependencies
        run: npm install
      - name: Run linter
        run: npm run lint
      - name: Run tests
        run: npm test
      - name: Check coverage
        run: npm run coverage
```

**Benefits:**
- Tests run automatically on every push
- Broken code can't merge to main
- Everyone runs same tests
- Catches issues early

### Gating Merges with Checks

Setup branch protection to require:
- ✅ All tests pass
- ✅ Linting succeeds
- ✅ Code coverage above 80%
- ✅ Security scan passes
- ✅ No conflicts with main

**Result:** Code quality is enforced by automation, not willpower.

### Deployment Pipelines

```yaml
# Deploy to different environments based on branch

Merge to main
    ↓
All tests pass?
    ├─ No → Block merge
    └─ Yes → Send to staging
             ↓
             Manual test? 
             ├─ No → Back to dev
             └─ Yes → Tag release
                      ↓
                      Deploy to production
```

### Feedback Loops

**Direct feedback to developer:**
```
Developer pushes code
    ↓
CI/CD runs tests
    ↓
Test fails?
    ├─ Yes → Email/Slack: "Build failed: auth.test.js line 42"
    │        Developer gets immediate feedback
    │        Fixes are same day
    └─ No → Continue to code review
```

**Quality metrics:**
- Measure build times
- Track test coverage trends
- Monitor production errors
- Report metrics to team

---

## Summary: Best Practices Checklist

**Commit Discipline:**
- [ ] One logical change per commit
- [ ] Meaningful commit messages (title + body)
- [ ] Regular commits (15-30 min frequency)
- [ ] Atomic, reviewable units

**Branch Strategy:**
- [ ] Consistent naming (type/description)
- [ ] Short-lived branches (2-5 days max)
- [ ] Keep branches in sync with main
- [ ] Delete after merge

**Collaboration:**
- [ ] Code review before merge
- [ ] Require tests to pass
- [ ] Document agreements
- [ ] Run linting/formatting

**DevOps:**
- [ ] Automate testing
- [ ] Gate merges with checks
- [ ] Automated deployment
- [ ] Continuous feedback

---

## Next Steps

Understanding the internals of Git helps you use it more effectively:

→ **[Next: Understanding Git Internals](06%20-%20Git%20Internals.md)**

---

**← [Back to Index](00%20-%20Index.md)**
