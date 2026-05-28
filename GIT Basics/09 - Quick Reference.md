# 9. Quick Reference & Cheatsheet

**← [Back to Index](00%20-%20Index.md)** | **[Previous: Common Pitfalls](08%20-%20Common%20Pitfalls.md)** | **[Next: Git Terminology →](A1%20-%20Git%20Terminology.md)**

---

## Quick Command Lookup by Task

### Getting Started

```bash
git init                            # Create new repository
git clone https://...               # Copy existing repository
git config --global user.name "..."  # Set your name
git config --global user.email "..." # Set your email
```

---

### Viewing Status and History

```bash
git status                          # What changed?
git status -s                       # Short status
git log                             # Full commit log
git log --oneline                   # One line per commit
git log --oneline -10               # Last 10 commits
git log --graph --all               # Visual branch diagram
git log --author="name"             # Commits by author
git log -- filename.js              # History of file
git log -p                          # Show changes too
git log --since="2 days ago"        # Recent commits
git log --grep="auth"               # Search commit messages
git diff                            # Unstaged changes
git diff --staged                   # Staged changes
git diff HEAD~3                     # Changes last 3 commits
git show <commit>                   # View specific commit
git blame filename.js               # Who wrote each line?
git tag -l                          # List all tags
git remote -v                       # List remotes with URLs
```

---

### Making Changes

```bash
git add filename.js                 # Stage file
git add -A                          # Stage all changes
git add .                           # Stage all in current dir
git add -p                          # Interactive staging
git commit -m "message"             # Commit staged with message
git commit -am "message"            # Add tracked + commit
git commit --amend                  # Change last commit
git commit --amend --no-edit        # Amend without changing message
git reset filename.js               # Unstage file
git restore filename.js             # Discard changes in file
git restore --staged filename.js    # Unstage, keep changes
git clean -fd                       # Remove untracked files/dirs (DANGEROUS!)
```

---

### Branching

```bash
git branch                          # List local branches
git branch -a                       # List all branches
git branch feature-name             # Create branch
git checkout feature-name           # Switch to branch
git checkout -b feature-name        # Create and switch
git switch feature-name             # Switch (newer syntax)
git switch -c feature-name          # Create and switch (newer)
git branch -d feature-name          # Delete branch
git branch -D feature-name          # Force delete
git branch -v                       # Branches with last commit
git branch -m old-name new-name     # Rename branch
git branch --merged                 # Branches already merged
git branch --no-merged              # Branches not yet merged
```

---

### Merging

```bash
git merge feature-name              # Merge branch into current
git merge --no-ff feature-name      # Merge with merge commit
git merge --squash feature-name     # Squash commits before merge
git merge --abort                   # Cancel merge (if conflicts)
```

---

### Rebasing

```bash
git rebase main                     # Rebase current onto main
git rebase -i HEAD~5                # Interactive rebase last 5
git rebase --continue               # Resume after conflict
git rebase --abort                  # Cancel rebase
git pull --rebase                   # Fetch + rebase instead of merge
```

---

### Undoing and Recovery

```bash
git restore filename.js             # Restore file to HEAD
git restore --staged filename.js    # Unstage file
git revert <commit>                 # Create undoing commit
git reset --soft HEAD~1             # Undo commit, keep changes
git reset --hard HEAD~1             # Undo commit, lose changes
git reset --hard <commit>           # Go back to specific commit
git reset HEAD~3                    # Go back 3 commits
git reflog                          # Find lost commits
git cherry-pick <commit>            # Apply commit to current branch
git stash                           # Temporarily save changes
git stash list                      # View all stashes
git stash pop                       # Restore latest stash
git stash show                      # View what's in stash
git stash drop                      # Delete stash
git fsck --lost-found               # Find orphaned commits
```

---

### Remote Operations

```bash
git push origin main                # Push branch to remote
git push -u origin main             # Push and set upstream
git push origin feature-name         # Push feature branch
git push --all                      # Push all branches
git push origin --delete feature    # Delete remote branch
git push origin --tags              # Push all tags
git push origin <tag>               # Push specific tag
git pull origin main                # Fetch + merge
git fetch origin                    # Get remote changes (no merge)
git fetch --all                     # Fetch from all remotes
git pull --rebase                   # Pull with rebase instead merge
git remote add upstream <url>       # Add another remote
git remote remove origin             # Remove remote
git remote rename old-name new-name # Rename remote
git remote set-url origin <url>     # Change remote URL
git remote show origin              # View remote details
```

---

### Collaboration

```bash
git fetch origin                    # Check what's new on remote
git pull origin main                # Get latest from remote
git log origin/main..main           # Commits not yet pushed
git log main..origin/main           # Commits not yet pulled
```

---

### Tagging

```bash
git tag v1.0.0                      # Create lightweight tag
git tag -a v1.0.0 -m "Release"     # Create annotated tag
git tag -l                          # List tags
git show v1.0.0                     # View tag details
git push origin v1.0.0              # Push specific tag
git push origin --tags              # Push all tags
git tag -d v1.0.0                   # Delete local tag
git push origin --delete v1.0.0     # Delete remote tag
```

---

### Advanced

```bash
git bisect start                    # Find bug via binary search
git bisect bad <commit>             # Mark commit as bad
git bisect good <commit>            # Mark commit as good
git bisect reset                    # End bisect session
git filter-branch                   # Rewrite history (dangerous!)
git gc                              # Garbage collection (cleanup)
git fsck                            # Check repository integrity
git rev-parse HEAD                  # Get current commit SHA
git describe --tags                 # Human-readable version
git whatchanged file.js             # Alternative to git log
```

---

## Useful Aliases

Add these to your `~/.gitconfig`:

```ini
[alias]
  st = status
  co = checkout
  br = branch
  ci = commit
  unstage = reset HEAD --
  last = log -1 HEAD
  recent = log --oneline -10
  visual = log --graph --oneline --all --decorate
  amend = commit --amend --no-edit
  undo = reset --soft HEAD~1
  squash = commit --squash
  fixup = commit --fixup
  rebase-i = rebase -i
  find-file = log -p --follow -S
  contrib = shortlog --summary --numbered
```

**Usage:**
```bash
git st                              # git status
git recent                          # git log --oneline -10
git visual                          # git log --graph --oneline --all --decorate
```

---

## Common Workflows

### Create and Push a Feature

```bash
git checkout -b feature/login-page
git add .
git commit -m "Add login page component"
git push -u origin feature/login-page
# Create PR on GitHub
```

### Update Work Branch with Latest Main

```bash
git fetch origin
git rebase origin/main
# or: git merge origin/main
```

### Squash Multiple Commits

```bash
git rebase -i HEAD~5
# In editor: mark commits as 'squash'
# Save and close
```

### Undo Last Commit (Keep Changes)

```bash
git reset --soft HEAD~1
git status                          # Changes still staged
```

### Recover Deleted File

```bash
git log -- deleted-file.js
git show <commit>:path/deleted-file.js > deleted-file.js
```

### Merge Conflict Resolution

```bash
git merge feature/branch
# Conflicts appear in files
# Edit files to resolve
git add resolved-files.js
git commit -m "Resolve merge conflicts"
```

---

## Emergency Commands

**"I committed on wrong branch":**
```bash
git reflog | head -5              # Find your commit
git reset --hard <old-commit>     # Reset branch
git checkout -b correct-branch
git reset --hard <your-commit>
```

**"I lost my commits":**
```bash
git reflog                        # Find them
git reset --hard <lost-commit>
```

**"I need to undo a push":**
```bash
git revert <commit>              # Safe: creates new commit
git push                         # Push the revert
# or for local only:
git reset --hard HEAD~1
# Don't push after force-reset on shared branches!
```

---

## Performance Tips

```bash
git gc                    # Clean up repository
git prune                 # Remove unreachable objects
git count-objects         # Check object count
du -sh .git               # Check repository size
git fsck --lost-found     # Find unreachable commits
```

---

## Debugging

```bash
git status                # Always start here
git log --oneline -20     # See recent history
git show HEAD             # See last commit details
git diff                  # See uncommitted changes
git reflog                # See your command history
git fsck                  # Check repository integrity
```

---

## Configuration Essentials

```bash
# View all config
git config --list

# Set essentials
git config --global user.name "Your Name"
git config --global user.email "you@example.com"

# Helpful settings
git config --global color.ui true
git config --global core.editor "code"
git config --global pull.rebase true
git config --global credential.helper osxkeychain  # macOS
```

---

## Quick Comparison: Similar Operations

| What You Want | Command |
|---------------|---------|
| Undo commit (safe) | `git revert <commit>` |
| Undo commit (dangerous) | `git reset --hard HEAD~1` |
| Update with remote (merge) | `git pull origin main` |
| Update with remote (rebase) | `git pull --rebase` |
| See changes | `git diff` |
| See changes staged | `git diff --staged` |
| Update branch from main | `git rebase origin/main` |
| Update branch from main | `git merge origin/main` |
| Switch branch | `git checkout branch` |
| Switch branch | `git switch branch` |

---

## Next Steps

Need more details on specific terms?

→ **[Next: Git Terminology](A1%20-%20Git%20Terminology.md)**

---

**← [Back to Index](00%20-%20Index.md)**
