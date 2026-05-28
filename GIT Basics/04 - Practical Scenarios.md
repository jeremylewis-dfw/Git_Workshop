# 4. Practical Scenarios & Solutions

**← [Back to Index](00%20-%20Index.md)** | **[Previous: Common Workflows](03%20-%20Common%20Workflows.md)** | **[Next: Best Practices →](05%20-%20Best%20Practices.md)**

---

## Quick Links
- ["Oh No" Moments](#41-oh-no-moments) - Recovering from mistakes
- [Collaboration Problems](#42-collaboration-problems) - Resolving conflicts and issues
- [History Management](#43-history-management) - Cleaning up messy commits

---

## 4.1 "Oh No" Moments

### Scenario 1: I Committed on the Wrong Branch

**The Situation:**
```bash
# You're working on bug fixes
git checkout main

# You accidentally forgot you were on main
# You made 3 commits
git log --oneline | head -3
# abc123d Fix pagination bug
# def456g Update styles  
# ghi789j Add validation

# Oh no! These should be on bugfix/critical-issue, not main!
```

**Solution: Move Commits to Correct Branch**

```bash
# Option 1: Move commits using cherry-pick (safe, recommended)

# Create the correct branch (pointing to where main SHOULD be)
git checkout -b bugfix/critical-issue HEAD~3
# This creates a new branch 3 commits back

# Reset main to where it should be
git checkout main
git reset --hard HEAD~3
# Now main is back to before those commits

# Verify:
git log --oneline | head -1
# Should be the commit BEFORE your mistakes

# The commits are now safely on bugfix/critical-issue
# Push to remote
git push -u origin bugfix/critical-issue
```

**Option 2: Using git reset and git cherry-pick**

```bash
# Remember the commit hashes
git log --oneline
# abc123d Fix pagination bug
# def456g Update styles  
# ghi789j Add validation

# Create the feature branch
git checkout -b bugfix/critical-issue

# Go back to main
git checkout main

# Remove the 3 commits from main
git reset --hard HEAD~3

# Commits are now on bugfix branch, main is clean
```

### Scenario 2: I Lost My Work

**The Situation:**
```bash
# You made changes, didn't commit
# You did something stupid
git reset --hard HEAD

# All your uncommitted changes are gone!
# PANIC 😱
```

**Recovery: Using git reflog**

Git keeps a log of every place HEAD has pointed to:

```bash
# See the reflog (your history of operations)
git reflog
# ab12345 HEAD@{0}: reset: moving to HEAD
# cd34567 HEAD@{1}: commit: Add feature
# ef56789 HEAD@{2}: commit: Fix bug
# ...

# Recovery command
git reset --hard cd34567
# Now your uncommitted changes are back!
```

**Prevention: Commit More Often**

```bash
# Before doing risky operations, commit
git add .
git commit -m "WIP: Feature in progress"

# Now if something goes wrong, you have a checkpoint
```

### Scenario 3: I Need to Undo That Commit

**The Situation:**
```bash
# You made a commit that broke things
git log --oneline | head -3
# abc123d Broke authentication (OOPS!)
# def456g Add feature
# ghi789j Previous work

# Need to undo the bad commit but keep history visible
```

**Solution 1: Using git revert (Recommended for shared branches)**

```bash
# Revert creates a NEW commit that undoes the bad commit
git revert abc123d

# Git opens an editor for commit message
# (Default message: "Revert 'Broke authentication'")

# After saving:
git log --oneline | head -3
# new123z Revert "Broke authentication"
# abc123d Broke authentication
# def456g Add feature

# Result: Both commits exist. The revert undoes the bad one.
# History is preserved and visible.
# Safe to push to shared branches.
```

**Solution 2: Using git reset (Only for unpushed commits)**

```bash
# Reset as if that commit never happened
# WARNING: Only use if commit hasn't been pushed to shared branch!
git reset --hard HEAD~1

# The bad commit is gone
git log --oneline | head -2
# def456g Add feature
# ghi789j Previous work

# History shows it never happened
# Only use locally!
```

### Scenario 4: I Accidentally Deleted a File

**The Situation:**
```bash
# You deleted an important file
rm src/critical-component.js

# Or you committed a deletion
git add -A
git commit -m "Clean up old files"

# Now you realize: oh no, you need that file!
```

**Recovery:**

```bash
# Find when the file was last committed
git log --oneline -- src/critical-component.js
# abc123d Clean up old files (deletion)
# def456g Add component
# ghi789j Initial commit

# Go back to when it existed
git show def456g:src/critical-component.js > src/critical-component.js

# File is restored!
git add src/critical-component.js
git commit -m "Restore critical-component.js"
```

---

## 4.2 Collaboration Problems

### Scenario 1: Merge Conflicts

**The Situation:**
```bash
# You and teammate both edited app.js
# You pulled their changes
git pull origin main

# Git reports:
CONFLICT (content conflict): Merge conflict in app.js
```

**In app.js:**
```javascript
function authenticate() {
<<<<<<< HEAD
  // Your version
  const token = generateToken();
  return {token, expires: Date.now() + 3600000};
=======
  // Teammate's version
  const token = jwt.sign({user: userId});
  return token;
>>>>>>> origin/main
}
```

**Resolution Process:**

```bash
# Step 1: Understand the conflict
# Left side (<<<<<<): Your changes
# Right side (>>>>>>>): Their changes
# Middle (=======): Separator

# Step 2: Decide what to keep
function authenticate() {
  // Decision: Keep both approaches by combining them
  const token = jwt.sign({user: userId});
  return {
    token, 
    expires: Date.now() + 3600000
  };
}

# Step 3: Remove conflict markers

# Step 4: Tell Git the conflict is resolved
git add app.js

# Step 5: Complete the merge
git commit -m "Merge main: resolve authenticate function conflict"

# Step 6: Push your merged version
git push origin main
```

**Avoiding Big Conflicts:**

```bash
# Keep your branch in sync with main constantly
git fetch origin
git rebase origin/main

# This prevents massive divergence
# Conflicts are smaller and easier to resolve
```

### Scenario 2: Stale Branch (Teammate Merged Already)

**The Situation:**
```bash
# You were working on feature/users-table
# Meanwhile, teammate merged feature/user-profiles to main
# Now when you try to merge your branch:

git merge main
# Conflicts everywhere because user-profiles also modified database

# Your branch is "stale" - it's out of sync with main
```

**Solution: Rebase Instead of Merge**

```bash
# Update your feature branch with latest main
git fetch origin
git rebase origin/main

# Git replays your commits on top of latest main
# Result: Your commits are on top of current state
# Much cleaner history

# If conflicts arise during rebase, resolve them:
git add .
git rebase --continue

# Once done
git push -f origin feature/users-table
# Note: Force push because we changed history on feature branch
# This is OK for feature branches (not OK for main!)
```

### Scenario 3: Teammate Pushed to Wrong Branch

**The Situation:**
```bash
# Teammate merged feature branch directly to main without pull request
# Now main has untested code

# You see:
# "Oops. My branch got merged but it wasn't reviewed"
```

**Prevention: Branch Protection Rules**

Set up on GitHub/GitLab:
- Only allow merges through pull requests
- Require code review (minimum 2 approvals)
- Require tests to pass
- Require no conflicts with main
- Block force pushes to main

**If it Happens Anyway:**

```bash
# Revert the bad commits
git revert <commit-hash>

# Or reset main to before the merge (if nobody pulled yet)
git reset --hard <safe-commit>
git push -f origin main
# Force push to ignore the remote's history

# Then create a proper pull request for code review
git checkout -b feature/redo-with-review
git cherry-pick <commits-to-include>
# Submit as proper PR for review
```

---

## 4.3 History Management

### Scenario 1: I Have Too Many Messy Commits

**The Situation:**
```bash
git log --oneline
# abc123d WIP: trying something
# def456g Fix linting
# ghi789j WIP: still trying
# jkl012m Add feature (for real this time)
# mno345p Fix typo in feature
# qrs678u More fixes

# This is messy. Would be better as 1-2 clean commits.
```

**Solution: Interactive Rebase**

```bash
# Rebase the last 6 commits
git rebase -i HEAD~6

# Opens editor with:
pick abc123d WIP: trying something
pick def456g Fix linting
pick ghi789j WIP: still trying
pick jkl012m Add feature (for real this time)
pick mno345p Fix typo in feature
pick qrs678u More fixes

# Change to:
squash abc123d WIP: trying something
squash def456g Fix linting
squash ghi789j WIP: still trying
pick jkl012m Add feature (for real this time)
squash mno345p Fix typo in feature
squash qrs678u More fixes

# Save and close editor
# Git combines all squashed commits into one

# Now you have:
git log --oneline
# One clean commit with all the logical changes
```

**Commands in interactive rebase:**
- `pick` - Use commit as-is
- `squash` - Combine with previous commit
- `reword` - Keep commit but change message
- `drop` - Remove commit entirely
- `fixup` - Like squash but discard commit message

### Scenario 2: I Want to Split a Large Commit

**The Situation:**
```bash
# You made one big commit that should be 2 commits
git log --oneline | head -1
# abc123d Add user management system (too much in one commit)
```

**Solution:**

```bash
# Reset to before that commit, keep changes
git reset HEAD~1 --soft
# Commit is undone, but changes remain staged

# Now stage and commit separately
git reset HEAD
# Unstage everything

git add src/UserModel.js
git commit -m "Add User model and database schema"

git add src/UserController.js
git commit -m "Add user management endpoints"

# Now you have two focused commits instead of one large one
```

### Scenario 3: I Need to Clean Up Before Pushing

**The Situation:**
```bash
# Your branch has lots of work-in-progress commits
git log --oneline origin/main..
# abc123d WIP
# def456g WIP
# ghi789j Actual feature
# jkl012m More feature
# mno345p Fix
# qrs678u Ready to submit

# You want to clean this up before PR review
```

**Solution:**

```bash
# Interactive rebase to main
git rebase -i origin/main

# In the editor, squash the WIPs:
squash abc123d WIP
squash def456g WIP
pick ghi789j Actual feature
squash jkl012m More feature
squash mno345p Fix
squash qrs678u Ready to submit

# Result: 2 clean commits
# 1. All the WIP merged into feature development
# 2. Nice history for code review

git log --oneline origin/main..
# xyz789k Implement user authentication feature
# abc123d Add user model
```

---

## Summary: Recovery Toolkit

| Problem | Solution | Command |
|---------|----------|---------|
| Wrong branch | Move commits | `git reset/cherry-pick` |
| Lost work | Get from reflog | `git reflog` + `git reset --hard` |
| Bad commit | Undo it | `git revert` (safe) or `git reset` (local) |
| Deleted file | Restore from history | `git show <commit>:path/file` |
| Merge conflict | Resolve manually | Edit file, `git add`, `git commit` |
| Messy commits | Rebase and squash | `git rebase -i` |
| Large commit | Split it | `git reset --soft` |

---

## Next Steps

Now that you know how to handle problems, let's look at preventing them:

→ **[Next: Best Practices & DevOps](05%20-%20Best%20Practices.md)**

---

**← [Back to Index](00%20-%20Index.md)**
