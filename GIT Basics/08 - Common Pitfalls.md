# 8. Common Pitfalls & How to Avoid Them

**← [Back to Index](00%20-%20Index.md)** | **[Previous: Tools & Environment](07%20-%20Tools%20&%20Environment.md)** | **[Next: Quick Reference →](09%20-%20Quick%20Reference.md)**

---

## Quick Links
- [Beginner Mistakes](#81-beginner-mistakes) - Common errors when starting
- [Team Collaboration Pitfalls](#82-team-collaboration-pitfalls) - Issues working with others
- [Repository Hygiene](#83-repository-hygiene) - Keeping repos clean and healthy

---

## 8.1 Beginner Mistakes

### Mistake 1: Committing Large Binary Files

**The Problem:**
```bash
git add video.mp4          # 500MB video file
git add node_modules/      # 200MB dependencies
git add .env               # Secret API keys
git commit -m "Add stuff"
git push
# Repository is now 700MB
# Everyone cloning gets 700MB
# History is bloated forever
```

**Why it's bad:**
- Clone times become glacially slow
- Pushing/pulling is painful
- Binary files can't be merged (corrupted on conflict)
- Once in history, hard to remove permanently
- Secrets are now in public history

**Prevention:**
```bash
# Create .gitignore (committed to repo):
node_modules/
dist/
build/
*.mp4
*.avi
.env
.env.local
*.log

# One-time cleanup (if you already committed):
git rm --cached node_modules/    # Remove from Git, keep locally
git commit -m "Remove node_modules from tracking"

# Use Git LFS for large files:
git lfs install
git lfs track "*.mp4"
git add .gitattributes
git add video.mp4                # Now stores pointer, not actual file
```

### Mistake 2: Hardcoding Secrets

**The Problem:**
```javascript
// config.js
API_KEY = "sk_live_abc123def456";  // Commit this by accident
DATABASE_PASSWORD = "super-secret-password";
```

```bash
git add config.js
git commit -m "Add configuration"
git push
# Now your secrets are in public GitHub history
# Hackers see them: bad day
```

**Why it's bad:**
- Anyone with repo access has your secrets
- Secrets are forever in history (hard to remove)
- Security breach waiting to happen

**Prevention:**
```bash
# Store secrets in environment variables... Add to .gitignore:
.env
.env.local
.env.*.local

# In your code:
const API_KEY = process.env.API_KEY;
const PASSWORD = process.env.DATABASE_PASSWORD;

# On your machine (.env file):
API_KEY=sk_live_abc123def456
DATABASE_PASSWORD=super-secret-password

# If you accidentally committed:
# Treat as active security incident
# Rotate all affected secrets immediately
# Use tools to remove from history (git-filter-branch)
```

### Mistake 3: Unclear Commit Messages

**The Problem:**
```bash
git log --oneline
# abc123d Fix stuff
# def456g Update things
# ghi789j Changes
# jkl012m WIP
```

Six months later, you need to understand why a line of code exists:
```bash
git blame confusing-file.js
# Shows: def456g "Update things" by Alice

# You have NO IDEA why this was changed
# Alice has moved on to another company
# Dead end
```

**Prevention:**
```bash
# Good commit message includes:
# - What changed (be specific)
# - Why it changed (the reason)
# - How it changed (if non-obvious)

git commit -m "Fix payment processing timeout issue

Previously, payment API calls failed if response took >5 seconds.
Now we've increased timeout to 10 seconds and added retry logic.
Fixes #1234 (customer complaints about failed transactions).
"
```

**Future you will be grateful.**

### Mistake 4: Committing Without Understanding

**The Problem:**
```bash
# Someone told you:
git add .
git commit -m "Here's my fix"
git push

# You have no idea what's in those commits
# What if sensitive data leaked?
# What if you broke something?
# You can't explain your own work
```

**Prevention:**
```bash
# Before staging and committing:
git status              # What files changed?
git diff               # What exactly changed?
git diff --cached      # What's staged?

# Review your own work:
git log --oneline -5   # Did those commits happen?
git show <commit>      # What's in that commit?

# Only commit what you understand
```

---

## 8.2 Team Collaboration Pitfalls

### Pitfall 1: Force Pushing to Shared Branches

**The Problem:**
```bash
# You rebase and want to update main
git push -f origin main
# Force push to "fix history"

# Meanwhile, teammates have pulled main
# They have old main in their machines
# Their next push conflicts/corrupts history
# Chaos ensues
```

**Why it's bad:**
- Rewrites history others depends on
- Causes confusion for the team
- Data loss can occur
- Breaks merge bases

**Prevention:**
```bash
# NEVER force push to main or shared branches

# Force push is OK for:
# - Feature branches (only you working on it)
# - Under specific circumstances (and tell your team!)

# If you need to rebase a shared branch:
git rebase origin/main
git push    # Regular push, not -f
# If that fails, it means someone pushed too
# Resolve it: git pull --rebase then git push
```

### Pitfall 2: Stale Branches Piling Up

**The Problem:**
```bash
git branch -a
# feature/auth (merged 3 months ago)
# feature/dashboard (merged 2 months ago)
# bugfix/old-issue (merged 4 months ago)
# feature/abandoned-work (never finished)
# experiment/weird-idea (forgotten)
# ... (30+ branches)

# Nobody knows which branches are active
# Repository becomes cluttered
```

**Prevention:**
```bash
# Delete merged branches
git branch -d feature/auth           # Safe delete (already merged)
git branch -D feature/abandoned      # Force delete

# Set up cleanup automation
# Delete branches automatically after merge (GitHub setting)

# Regular cleanup
git branch -a
# Delete local branches no longer needed
# Keep main and active feature branches
```

### Pitfall 3: Not Keeping Branches in Sync

**The Problem:**
```bash
# Day 1: You create feature/my-feature from main
# Day 2: Teammates merge other features to main
# Day 3: You finish your work
# Day 4: You merge your branch to main

# Massive conflicts because main evolved and you didn't update
```

**Prevention:**
```bash
# Keep branch in sync with main
git fetch origin
git rebase origin/main

# Or pull and merge:
git fetch origin
git merge origin/main

# Do this daily (or more often on active projects)
```

### Pitfall 4: Ignoring Code Review

**The Problem:**
```bash
# You skip code review to move faster
# Raw code goes to production
# Bugs that reviewer would have caught make it live
# Customer-facing issue
```

**Prevention:**
```bash
# Enforce code review workflows
# GitHub: Branch protection rules
#   - Require pull request reviews
#   - Require status checks pass
#   - Block direct pushes to main

# Minimum 2 approvals before merge
# Everyone participates (even experienced devs)
# Reviews are for learning and quality, not blame
```

---

## 8.3 Repository Hygiene

### Cleanliness Issue 1: History Pollution

**The Problem:**
```bash
git log --oneline | head -20
# abc123d WIP
# def456g Small fix
# ghi789j WIP: still trying
# jkl012m WIP: almost there
# mno345p Actually works now
# pqr678u Fix typo
# stu901v Minor cleanup
# ... (incomprehensible mass)
```

When you try to find when a bug was introduced:
- `git bisect` takes forever
- History is unreadable

**Prevention:**
```bash
# Before pushing, clean up
git rebase -i origin/main

# Squash related commits:
pick abc123d WIP
squash def456g Small fix
squash ghi789j WIP: still trying
squash jkl012m WIP: almost there
pick mno345p Actually works now

# Result: 1 clean commit instead of 5 messy ones
```

### Cleanliness Issue 2: Orphaned Branches

**The Problem:**
```bash
git branch -a | wc -l
# 150 branches!

# Most are dead:
feature/old-feature (from 2 years ago)
experiment/failed-idea
temp/random-testing
backup/old-code
...
```

**Prevention:**
```bash
# Delete merged branches regularly
git branch --merged | grep -v main
# Lists all branches merged to current (main)

# Delete them
git branch --merged | grep -v main | xargs git branch -d

# Set GitHub to auto-delete on merge
# (GitHub setting: Delete head branches on merge)
```

### Cleanliness Issue 3: Improper Release Tags

**The Problem:**
```bash
git tag -l
# v1.0
# v1.0.1
# version-1-point-0
# final
# final2
# final_for_real
# FINAL
# official-release

# Nobody knows which is production version
```

**Prevention:**
```bash
# Follow a versioning scheme (Semantic Versioning recommended)
# Major.Minor.Patch
# v1.2.3

# Create annotated tags (not lightweight):
git tag -a v1.0.0 -m "Release version 1.0.0"

# Tag from main only:
git checkout main
git pull
git tag -a v1.2.3 -m "Release 1.2.3"
git push origin v1.2.3
```

### Cleanliness Issue 4: Broken Repository

**Prevention:**
```bash
# Regularly verify repository integrity
git fsck
# Output should be: "Checking object database for abrupt termination"
# If errors appear, repository may be corrupted

# Backup important repos
# Consider:
# - Local backup (external drive)
# - Second remote (GitHub backup)
# - Regular clones to archive
```

---

## Summary: Pitfalls and Prevention

| Pitfall | Problem | Prevention |
|---------|---------|-----------|
| Large binary files | Slow clones, bloated repos | Use .gitignore, Git LFS |
| Hardcoded secrets | Security breach | Use .env files, add to .gitignore |
| Unclear messages | Can't understand history | Write meaningful commit messages |
| Force push to main | History corruption | Only force push feature branches |
| Stale branches | Repository clutter | Delete merged branches regularly |
| Out-of-sync branches | Mega conflicts | Rebase frequently on main |
| No code review | Bugs in production | Enforce PR/MR requirements |
| History pollution | Hard to debug | Clean up commits before pushing |
| Orphaned branches | Confusion and clutter | Regular cleanup |
| Unversioned releases | Can't track versions | Use semantic versioning |

---

## Next Steps

Ready for the quick reference guide?

→ **[Next: Quick Reference & Cheatsheet](09%20-%20Quick%20Reference.md)**

---

**← [Back to Index](00%20-%20Index.md)**
