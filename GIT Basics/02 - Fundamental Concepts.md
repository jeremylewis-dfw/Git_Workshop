# 2. Fundamental Concepts

**← [Back to Index](00%20-%20Index.md)** | **[Previous: Overview of Git](01%20-%20Overview%20of%20Git.md)** | **[Next: Common Workflows →](03%20-%20Common%20Workflows.md)**

---

## Quick Links
- [The Three-Tree Architecture](#21-the-three-tree-architecture) - Understanding the mental model
- [Commits](#22-commits-the-foundation) - The fundamental building block
- [Branches](#23-branches-parallel-workstreams) - How parallel development works
- [Remotes](#24-remote-repositories) - Distributed collaboration

---

## 2.1 The Three-Tree Architecture

The most important concept to understand in Git is this: **Git doesn't directly track changes in individual files. Git tracks complete snapshots of your entire project at specific points in time.**

Git operates with three distinct storage areas, each with a different purpose:

### **1. Working Directory**

This is simply your project folder on disk—the files you see and edit every day.

```
my-project/
├── src/
│   ├── app.js (edited content)
│   └── utils.js (unchanged)
├── tests/
│   └── app.test.js
└── README.md
```

**Key point:** The working directory is YOUR PLAYGROUND. You can:
- Edit files freely
- Break things without fear
- Experiment
- Nothing is permanent here

**Current state:** Only you know what's here. Git isn't yet tracking these specific changes.

---

### **2. Staging Area (Index)**

The staging area is an intermediate holding zone between your working directory and your repository.

**Mental model:** Think of it like a shopping cart before checkout.

```
Working Directory → [Pick what to commit] → Staging Area → [Make it permanent] → Repository
```

When you run `git add`, you're saying: "I want to include these specific changes in my next commit."

**Example:**
```bash
# You edited 5 files
# But you only want to commit 3 of them right now
git add file1.js file2.js file3.js

# These 3 are now "staged" and ready to be committed
# The other 2 remain modified but not staged
```

**Key point:** The staging area lets you construct your commit intentionally. You can:
- Include only certain changes
- Leave work-in-progress uncommitted
- Build logical, focused commits

---

### **3. Repository (Commit History)**

The repository is the permanent storage of your project's history, stored in the `.git` folder.

Each commit is a **complete snapshot** of your entire project at that moment in time:

```
Repository (History):
│
├─ Commit A (Time: 10:00)
│  └─ Snapshot of entire project (all files as they were then)
│
├─ Commit B (Time: 10:15)  
│  └─ Snapshot of entire project (all files as they were then)
│
└─ Commit C (Time: 10:30)
   └─ Snapshot of entire project (all files as they were then)
```

**Key point:** The repository is PERMANENT and IMMUTABLE. Every commit has a permanent ID (SHA-1 hash). Once committed, you cannot secretly change history.

---

### The Data Flow: Putting It All Together

Here's how these three areas work together in a typical workflow:

```bash
# Step 1: You edit files (Working Directory changes)
# (You edit src/app.js, making it different from what's in the repository)

# Step 2: You review what's changed
git status
# Shows: src/app.js has been modified (but not staged)

# Step 3: You decide what to commit
git add src/app.js
# src/app.js is now in the Staging Area

# Step 4: You commit (permanent snapshot)
git commit -m "Add user authentication"
# Commit is now in the Repository

# Step 5: Repeat
# You edit more files
git add (select what to include)
git commit (permanent snapshot)
```

### Why This Matters

The three-tree architecture gives you **precision and control**:

- **Working directory**: Safe zone for experimentation
- **Staging area**: Intentional composition of commits
- **Repository**: Permanent, auditable history

This is why Git is so much more powerful than just "backup everything":
- You don't commit every tiny change immediately
- You don't commit unrelated changes together
- You can keep partial work uncommitted without losing it

---

## 2.2 Commits: The Foundation

### What is a Commit?

A commit is **a permanent snapshot of your entire project at a specific point in time**.

Not "the changes made" but "the complete state of everything."

### Anatomy of a Commit

Every commit contains five essential pieces of data:

```
Commit ID (SHA-1 hash): abc123def456...
│
├─ Author: "Alice Smith <alice@example.com>"
├─ Date: "April 13, 2026 at 2:30 PM"
├─ Message: "Add user authentication system"
└─ Content: 
   └─ Complete snapshot of all files
      ├─ src/app.js (contents)
      ├─ src/auth.js (contents)
      ├─ tests/auth.test.js (contents)
      └─ ... (every file in the project)
```

### The Commit Hash: Git's Fingerprint

Git uses SHA-1 hashing to create a unique identifier for each commit. This hash has several important properties:

**1. Identity**
- Every commit has a unique ID
- You reference commits by first 7 characters: `abc123d`
- Full hash: `abc123def456789abcdef123456789abcdef1234`

**2. Integrity**
- If anyone tries to change a commit, the hash changes
- If the hash changes, Git knows the commit was tampered with
- This makes Git tamper-proof

**3. Deterministic**
- Same content always produces same hash
- You can verify content wasn't corrupted by re-hashing
- Different systems can independently verify history

### The Commit Message

A good commit message explains **why** a change was made, not just **what** changed.

**Bad commit message:**
```
Fix bug in auth
```

**Good commit message:**
```
Add JWT token refresh mechanism to fix session timeout issue

Previously, auth tokens expired and users were logged out 
even on active sessions. Now tokens automatically refresh 
on each API call within the refresh window, maintaining 
user sessions across extended inactive periods.
```

The good message:
- Explains the problem
- Explains the solution
- Explains the reason

When debugging months later, you understand **why** this code exists.

### Atomic Commits: The Principle of One Change Per Commit

An **atomic commit** contains one logical change—one reason to exist.

**Anti-pattern (commit bundle):**
```
Commit: "Work done today"
├─ Fix user authentication
├─ Refactor database layer
├─ Add new admin dashboard
├─ Update documentation
└─ Update 10 other unrelated things
```

**Why this is bad:**
- If one change needs to be reverted, all are affected
- History is incomprehensible
- Code review is impossible
- Debugging takes 10x longer

**Pattern (atomic commits):**
```
Commit 1: "Add JWT token refresh mechanism"
│  └─ Changes only to auth system

Commit 2: "Refactor database connection pooling"
│  └─ Changes only to database layer

Commit 3: "Add admin dashboard UI"
│  └─ Changes only to dashboard components

Commit 4: "Add admin functionality to API"
│  └─ Changes only to API endpoints
```

**Why this is good:**
- Each commit represents one idea
- Any commit can be reverted independently if needed
- History is comprehensible
- Code review is focused and thorough
- Bisecting (finding which commit broke something) works perfectly

### Viewing Commit History

```bash
# View all commits
git log

# View with changes shown
git log -p

# View specific file's history
git log -p filename.js

# View one-line summary
git log --oneline

# View last 10 commits
git log -10

# View commits by specific author
git log --author="Alice"

# View commits since yesterday
git log --since="1 day ago"
```

---

## 2.3 Branches: Parallel Workstreams

### What is a Branch?

A branch is simply **a named pointer to a specific commit**.

That's it. That's the whole thing.

```
Commits (immutable history):

        A ← commit
        │
        B ← commit
        │
        C ← commit
        │
→ main ─ D ← commit (main points here)
        │
        E ← commit
        │
→ feature/auth ─ F ← commit (feature/auth points here)
```

When you "create a branch," you're not copying anything or creating a separate codeline. You're just creating a new pointer to a commit.

### Why This Is Powerful

Because branches are just pointers, they are:
- **Lightweight**: Nearly zero overhead
- **Fast**: Creating a branch takes microseconds
- **Cheap**: Disk space is negligible
- **Non-disruptive**: Creating a branch doesn't affect anyone else

Compare this to centralized version control like SVN where creating a branch means copying the entire codebase to a new directory (slow, expensive, disruptive).

### Main Branch (Master)

The `main` branch (historically called `master`) represents the current stable/production version of the code.

Important principle: **main branch should always be deployable**.

### Feature Branches

A feature branch is temporary branch created for a specific feature or fix:

```bash
# Create a feature branch (pointer to current commit)
git checkout -b feature/user-profiles

# Now you can work independently
# Make commits, edit files, break things
# Nothing affects the main branch

# When done and tested:
git checkout main
git merge feature/user-profiles
# Now main contains your changes

# Delete the feature branch (it was temporary)
git branch -d feature/user-profiles
```

### Branching Workflow

This is the power of Git's branch model:

```
Timeline:

main: ─────A────B────────F────G─────
           │         │    │
           │         │    └─ Merge feature/auth
           │         │
           └─C──D──E─┘ feature/auth branch
             │    │
           Create branch, make commits, merge back
```

**Key insight:** Multiple features can be developed in parallel because they're on separate branches. Each branch is independent.

---

## 2.4 Remote Repositories

### Local vs Remote

**Local Repository:**
- Stored on your machine in the `.git` folder
- You have complete history
- You can work offline
- Only you can access it (unless you copy files)

**Remote Repository:**
- Stored on a server (GitHub, GitLab, Bitbucket, your own server)
- Accessed over network
- Shared with team members
- Serves as central backup and collaboration point

### Typical Workflow with a Remote

```
Your Machine                    GitHub Server
─────────────────              ────────────────
 
Local Repo                      Remote Repo
(Your commits)        ──────>   (Same commits)
                  push
                  
Local Repo                      Remote Repo
(New commits)         <──────   (New commits)
                  pull
```

### Push: Sending Changes

```bash
# Send your commits to the remote
git push

# Git compares your local main with remote/main
# Sends only the new commits
# Remote repository now has your changes
```

**Analogy:** You're synchronizing your phone and cloud backup. Only the new data is transferred.

### Pull: Receiving Changes

```bash
# Get teammate's new commits from remote
git pull

# Git gets new commits from remote
# Git automatically merges them with your local commits
# Your local repository now matches remote
```

### Fetch: Seeing What's on Remote (Without Merging)

```bash
# Download what's on remote WITHOUT merging
git fetch

# Now you can see what teammates did
# But your local code isn't affected
# You can review before merging
```

### The Origin Reference

When you clone a repository, Git automatically creates a reference called `origin`:

```bash
git clone https://github.com/alice/project.git

# Git creates these references:
# - main (your local branch)
# - origin/main (tracking remote's main)
```

**Key distinction:**
- `main` = your local branch (where you work)
- `origin/main` = remote tracking branch (what you last synced from remote)

These can diverge if teammates push changes:

```
Timeline:

Local:      A──B──C──D (your work)
               │
Remote:     A──B──C──F──G (teammates' work)
               │
            Common ancestor
```

When you pull, Git merges (automatically if possible):

```
After git pull:

Local:      A──B──C──D─\
               │        └─ Merge commit
Remote:     A──B──C──F──G─/
```

---

## Summary: The Mental Model

Git's three core concepts work together:

| Concept | Purpose | Example |
|---------|---------|---------|
| **Commits** | Permanent snapshots | "At 2:30 PM, here's the entire project state" |
| **Branches** | Parallel workstreams | "I'm building feature/auth independently" |
| **Remotes** | Distributed collaboration | "Let's synchronize with GitHub" |

When you understand these three concepts deeply, Git's power becomes apparent.

---

## Next Steps

Ready to see how these concepts apply in real workflows?

→ **[Next: Common Workflows](03%20-%20Common%20Workflows.md)**

---

**← [Back to Index](00%20-%20Index.md)**
