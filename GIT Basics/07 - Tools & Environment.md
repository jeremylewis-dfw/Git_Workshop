# 7. Tools & Environment

**← [Back to Index](00%20-%20Index.md)** | **[Previous: Git Internals](06%20-%20Git%20Internals.md)** | **[Next: Common Pitfalls →](08%20-%20Common%20Pitfalls.md)**

---

## Quick Links
- [Command Line Mastery](#71-command-line-mastery) - Becoming efficient with Git commands
- [GUI Tools & IDEs](#72-gui-tools--ides) - When and how to use visual tools
- [Hosting Platforms](#73-hosting-platforms) - GitHub, GitLab, Bitbucket

---

## 7.1 Command Line Mastery

### Essential Git Commands

**Viewing Status and History**
```bash
git status                          # What's changed?
git log                             # See all commits
git log --oneline                   # Compact view
git log -p                          # Show actual changes
git log --graph --all               # Visual branch diagram
git log --author="Alice"            # Commits by person
git log -- file.js                  # History of specific file
git diff                            # Changes not staged
git diff --staged                   # Changes staged for commit
git show <commit>                   # View specific commit
git blame file.js                   # Who wrote each line?
```

**Making Changes and Committing**
```bash
git add file.js                     # Stage specific file
git add .                           # Stage all changes
git add -p                          # Interactive staging (choose hunks)
git commit -m "message"             # Commit staged changes
git commit -am "message"            # Add and commit in one (tracked files only)
git commit --amend                  # Modify last commit
git reset file.js                   # Unstage file
git restore file.js                 # Discard changes in file
git restore --staged file.js        # Unstage and keep changes
```

**Branching and Merging**
```bash
git branch                          # List local branches
git branch -a                       # List all branches
git branch feature/new              # Create new branch
git checkout branch-name            # Switch to branch
git checkout -b branch-name         # Create and switch to branch
git merge feature/auth              # Merge branch into current branch
git branch -d branch-name           # Delete branch (safe)
git branch -D branch-name           # Force delete branch
git rebase feature/base             # Rebase current branch on another
```

**Undoing and Recovery**
```bash
git revert <commit>                 # Create new commit that undoes it
git reset --hard HEAD~1             # Undo last commit (dangerous!)
git reset --soft HEAD~1             # Undo commit but keep changes staged
git reset HEAD user.js              # Unstage file
git reflog                          # Find lost commits
git cherry-pick <commit>            # Apply specific commit to current branch
git stash                           # Temporarily save changes
git stash pop                       # Restore stashed changes
```

**Collaboration**
```bash
git push origin main                # Send commits to remote
git pull origin main                # Fetch and merge from remote
git fetch origin                    # Check remote without merging
git clone https://...               # Get existing repository
git remote -v                       # View remotes
git remote add upstream https://... # Add another remote
```

### Aliases for Power Users

Create shortcuts for commonly used commands:

```bash
# Add to ~/.gitconfig

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
  fixup = commit --fixup
  squash = commit --squash
```

**Usage:**
```bash
git st              # Instead of git status
git br feature/new  # Instead of git branch feature/new
git visual          # Instead of git log --graph --oneline --all --decorate
```

### Shell Integration

**Display Git status in your prompt:**

```bash
# Add to ~/.bashrc or ~/.zshrc

export PS1='\u@\h \W$(__git_ps1 " (%s)") \$ '
# Now your shell shows current branch:
# user@computer ~/project (main) $
# user@computer ~/project (feature/auth) $
```

---

## 7.2 GUI Tools & IDEs

### VS Code Integration

**Built-in Git Features:**
- Source Control panel (Ctrl+Shift+G)
- View changes, stage files
- Write commit messages
- See branch indicators

**Recommended Extensions:**
1. **GitLens** - See blame info, commit history inline
2. **Git Graph** - Visualize commit history
3. **GitKraken** - Full Git client in VS Code

### GitKraken: Visual Git Client

**When to use GUI over CLI:**
- Viewing complex merge scenarios
- Interactive rebasing
- Comparing multiple commits side-by-side
- Visual branch management
- Learning how Git works

**When to use CLI:**
- Faster for common tasks
- Better for automation/scripting
- More reliable for complex operations
- Available everywhere

**Other GUI Tools:**
- **SourceTree** (free, feature-rich)
- **GitHub Desktop** (simple, GitHub-focused)
- **TortoiseGit** (Windows Explorer integration)
- **Sublime Merge** (lightweight, fast)

### IDE Built-in Support

**VS Code**
```
Left sidebar → Source Control icon
Shows:
- Changed files
- Staged files
- Commit history
- Branch management
```

**JetBrains (IntelliJ, PyCharm, etc.)**
```
VCS menu → Git options
Integrated diff viewer
Built-in merge conflict resolution
```

**Visual Studio**
```
Team Explorer → Git management
Full feature-rich Git integration
```

---

## 7.3 Hosting Platforms

### GitHub

**Pull Request Workflow:**
1. Fork repository (make your copy)
2. Clone to local machine
3. Create feature branch
4. Make commits and push
5. Create Pull Request on GitHub
6. Discuss changes with maintainers
7. Get approved → merge to main

**Features:**
- Issue tracking
- GitHub Actions (CI/CD)
- GitHub Pages (static sites)
- Discussions and wiki
- Security alerts

**For Open Source:**
```bash
# Fork the project on GitHub (creates your copy)

git clone https://github.com/YOUR-USERNAME/project.git
cd project
git remote add upstream https://github.com/ORIGINAL-OWNER/project.git

# Create feature branch
git checkout -b fix/my-fix

# Make commits...

git push origin fix/my-fix

# Create Pull Request via GitHub interface
# Discuss with maintainers
# Once merged, clean up:
git checkout main
git branch -d fix/my-fix
```

### GitLab

Similar to GitHub with some differences:
- **Merge Requests** instead of Pull Requests
- GitLab CI/CD built-in
- More enterprise features
- Self-hosting options available

### Bitbucket

Atlassian's Git solution:
- **Pull Requests** (similar to GitHub)
- Jira integration (great for issue tracking)
- Good for teams already using Atlassian tools
- Both cloud and self-hosted

### Choosing a Platform

| Aspect | GitHub | GitLab | Bitbucket |
|--------|--------|--------|-----------|
| **Open Source** | Dominant | Growing | Limited |
| **Ease of Use** | Very easy | Easy | Easy |
| **CI/CD** | GitHub Actions | Built-in | Built-in |
| **Enterprise** | Enterprise plan | Strong option | Strong option |
| **Cost** | Free/paid tiers | Free/paid tiers | Free/paid tiers |
| **Community** | Largest | Growing | Smaller |

**Recommendation:** GitHub for open source, any for teams.

---

## 7.4 Git Configuration

### User Configuration

```bash
# Set your identity (required for commits)
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# Project-specific (overrides global):
git config --local user.name "Different Name"
git config --local user.email "different@example.com"

# View your config
git config --list
```

### Helpful Configurations

```bash
# Set default editor
git config --global core.editor "code"  # VS Code
git config --global core.editor "vim"   # Vim

# Colorize output
git config --global color.ui true

# Prevent autocrlf issues (line endings)
git config --global core.autocrlf true  # Windows
git config --global core.autocrlf input # Mac/Linux

# Set up aliases (see section 7.1)
git config --global alias.st status
git config --global alias.co checkout

# Store credentials (don't re-enter password)
git config --global credential.helper osxkeychain  # Mac
git config --global credential.helper manager      # Windows
git config --global credential.helper store        # Linux
```

### .gitconfig File

```
# ~/.gitconfig (on your machine)
[user]
    name = Your Name
    email = your.email@example.com

[core]
    editor = code
    autocrlf = true

[alias]
    st = status
    co = checkout
    br = branch

[color]
    ui = true

[pull]
    rebase = true
```

---

## Summary: Tool Selection Guide

**For Learning Git:**
Start with command line. Understanding the fundamentals is more important than tool.

**For Daily Work:**
Mix of CLI (fast for common tasks) + GUI (when needed).

**For Visual Review:**
GUI tools are excellent for:
- Reviewing complex merges
- Understanding history
- Resolving conflicts

**For Automation:**
CLI + scripts are your friend.

---

## Next Steps

Let's look at common pitfalls and how to avoid them:

→ **[Next: Common Pitfalls](08%20-%20Common%20Pitfalls.md)**

---

**← [Back to Index](00%20-%20Index.md)**
