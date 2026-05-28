# 6. Understanding Git Internals

**← [Back to Index](00%20-%20Index.md)** | **[Previous: Best Practices](05%20-%20Best%20Practices.md)** | **[Next: Tools & Environment →](07%20-%20Tools%20&%20Environment.md)**

---

## Quick Links
- [The Object Model](#61-the-object-model) - How Git stores data
- [References and Branches](#62-references-and-branches) - How Git tracks branches
- [Git Philosophy](#63-git-philosophy) - Why Git is designed this way

---

## 6.1 The Object Model

### The Four Object Types

Git stores everything as one of four object types. Understanding these explains how Git works.

#### **1. Blob Object**

A blob is a version of a file (the file's content).

```
File: src/app.js
Content: 
  function main() {
    console.log("hello");
  }

Git stores this as:
┌─────────────────────────────┐
│ Type: blob                  │
│ Size: 45 bytes              │
│ Content: (file contents)    │
│ SHA-1: 5e9d9... (unique ID) │
└─────────────────────────────┘
```

**Key point:** Git doesn't store filenames at the blob level. A blob is pure content.

#### **2. Tree Object**

A tree represents a directory structure (like a folder).

```
Tree for src/:
┌──────────────────────────────┐
│ Type: tree                   │
│ Contents:                    │
│  ├─ app.js → blob 5e9d9...  │
│  ├─ utils.js → blob 3f2a1..│
│  └─ helpers/ → tree 7c4b8.. │
│ SHA-1: 8a2f0...             │
└──────────────────────────────┘
```

Trees can contain:
- Blobs (files)
- Other trees (subdirectories)

#### **3. Commit Object**

A commit object represents a snapshot of the entire project at one point in time.

```
Commit Object:
┌──────────────────────────────────────┐
│ Type: commit                         │
│ Tree: 8a2f0... (root tree)          │
│ Parent: 3f1b2... (previous commit)  │
│ Author: Alice <alice@example.com>   │
│ Date: April 13, 2026 2:30 PM        │
│ Message: "Add user authentication"  │
│ SHA-1: abc123d...                   │
└──────────────────────────────────────┘
```

**The chain of commits:**
```
Commit 1 (abc123d)
├─ Tree (entire project snapshot)
└─ Parent: (none - this is first commit)

Commit 2 (def456g)
├─ Tree (entire project snapshot)
└─ Parent: abc123d (points to commit 1)

Commit 3 (ghi789j)
├─ Tree (entire project snapshot)
└─ Parent: def456g (points to commit 2)
```

This linked list is Git's commit history. Each commit points to the previous one.

#### **4. Tag Object**

A tag object marks a specific commit (used for releases).

```
Tag Object:
┌────────────────────────────────┐
│ Type: tag                      │
│ Name: v1.0.0                   │
│ Commit: abc123d...             │
│ Tagger: Alice <alice@example.com>
│ Message: "Release version 1.0" │
│ SHA-1: xyz789t...              │
└────────────────────────────────┘
```

### Content Addressability

The crucial insight: **Git uses SHA-1 hash of content as the object's ID.**

```
Content: "hello world"
SHA-1: 2aae6c35c94fcfb415dbe95f408b9ce91ee846ed

No matter who calculates it, this content always produces this hash.
This is called "content addressability."
```

**Why it matters:**
- **Integrity:** If content changes, hash changes. Tampering is obvious.
- **Deduplication:** Same content produces same hash. No redundant storage.
- **Verification:** Anyone can verify that content hasn't been corrupted.

### The Git Object Database

All Git objects are stored in `.git/objects/`:

```
.git/objects/
├─ 2a/
│  └─ ae6c35c94fcfb415dbe95f408b9ce91ee846ed (blob)
├─ 5e/
│  └─ 9d91234... (tree)
├─ ab/
│  └─ c123def... (commit)
└─ xy/
   └─ z789... (tag)
```

The hash is used as the path:
- First 2 chars: directory name
- Remaining chars: filename

---

## 6.2 References and Branches

### References (Refs)

A reference is a pointer to a commit (stored as a simple file containing a SHA-1 hash).

**Types of refs:**
- **Branch:** `refs/heads/main` points to latest commit on main
- **Remote tracking:** `refs/remotes/origin/main` points to what's on remote
- **Tag:** `refs/tags/v1.0.0` points to a specific commit

### How Branches Work Internally

When you create a branch:

```bash
git checkout -b feature/auth
# Git creates a file: .git/refs/heads/feature/auth
# Content: sha1 of current commit
# Example: abc123def456...

# When you commit, Git updates this file
git commit -m "Add authentication"
# .git/refs/heads/feature/auth now points to NEW commit
```

**Viewing refs:**
```bash
# See all refs
cat .git/refs/heads/main
# Output: abc123def456789...

# Switch branch
# Git updates HEAD to point to different ref
cat .git/HEAD
# Output: ref: refs/heads/main

# Switch to feature branch
git checkout feature/auth
cat .git/HEAD
# Output: ref: refs/heads/feature/auth
```

### HEAD: The Direct or Indirect Reference

**Normal case (attached HEAD):**
```
HEAD → refs/heads/main → Commit ABC
```
You're on the main branch, HEAD points to the branch ref.

**Detached HEAD state:**
```
HEAD → Commit ABC (directly)
```
HEAD isn't pointing to a branch. It's pointing directly to a commit.

**When this happens:**
```bash
git checkout abc123d
# You're now in detached HEAD state
# Not on any branch

git log
# Shows history correctly

git commit -m "New commit"
# Creates a commit but no branch points to it
# It could be lost!

# Recovery: Create a branch
git checkout -b my-recovered-work
# Now the commit is saved by a branch
```

### Remote Tracking Branches

`origin/main` is a remote tracking branch (not a branch you work on).

```bash
# Clone a repo
git clone https://github.com/alice/project.git

# Git creates:
├─ main (local branch - you work here)
└─ origin/main (remote tracking - read-only reference)

# When you fetch:
git fetch origin
# origin/main is updated to match remote
# Your local main is unchanged

# When you pull:
git pull origin main
# Equivalent to: git fetch + git merge
# Fetches updates to origin/main
# Merges origin/main into your local main
```

---

## 6.3 Git Philosophy

### Immutability: The Core Principle

Once you commit something, **it cannot be secretly changed.**

Why? Because all commits are identified by SHA-1 hash of their content.

```
Original Commit:
Content: "Initial setup"
SHA-1: abc123d

If someone tries to change the commit message:
Content: "Initial setup" (changed to "Changed message")
SHA-1: different hash!

Git detects: "The commit ID doesn't match the content!"
Result: Tampering is obvious
```

**This is why Git is trustworthy:**
- You can trust history hasn't been altered
- You can verify commits are authentic
- You can run `git fsck` to check repository integrity

### Distribution as Strength (Not Weakness)

Traditional systems: One central server = single point of failure

```
Centralized (SVN):
Server goes down → Nobody can work
Server has only copy → Data loss catastrophe
One person controls access → Bottleneck
```

Git distribution:

```
Each developer has complete history:
├─ Developer 1: Full history backup
├─ Developer 2: Full history backup
├─ Developer 3: Full history backup
└─ GitHub: Another full backup

Server goes down → Everyone keeps working
Server has problem → History is safely distributed
Multiple copies → Impossible to lose
```

### Offline-First Capabilities

Most Git operations work without network:

```bash
git log              # ✅ Works offline (history is local)
git commit           # ✅ Works offline (committing locally)
git branch -a        # ✅ Works offline (refs are local)
git blame app.js     # ✅ Works offline (history is local)
git diff HEAD~5      # ✅ Works offline (comparing local commits)

git push origin main # ❌ Needs network (uploading to server)
git pull origin main # ❌ Needs network (downloading from server)
git fetch            # ❌ Needs network (checking remote)
```

**Why this matters:**
- Work on airplanes/offline
- Work on slow connections (sync large histories once)
- Reduced server load (less network traffic)

### Flexibility Through Plumbing and Porcelain

Git has two levels of commands:

**Porcelain** (user-friendly):
```bash
git add, git commit, git push, git pull
git branch, git merge, git rebase
```

**Plumbing** (low-level, powerful):
```bash
git cat-file -p <object>    # View object contents
git hash-object             # Calculate object hash
git update-ref              # Manually update refs
git rev-parse               # Parse revision references
```

This flexibility allows advanced users to:
- Recover seemingly lost commits
- Rebuild history
- Work with Git in creative ways

---

## Summary: How Git Works

| Layer | What | How |
|-------|------|-----|
| **Objects** | Blobs, Trees, Commits, Tags | Content-addressed by SHA-1 hash |
| **References** | Branches, remote tracking branches | Pointers to commits (files containing SHA-1) |
| **Commits** | History | Linked list of commits (each points to parent) |
| **Integrity** | Tamper detection | SHA-1 hashes verify content hasn't changed |
| **Distribution** | Backup and collaboration | Every developer has complete history |

This design makes Git:
- **Fast** (objects are local, hashing is cheap)
- **Safe** (immutability and hashing ensure no secrets changes)
- **Distributed** (everyone has complete history)
- **Flexible** (plumbing commands allow advanced usage)

---

## Next Steps

Now let's look at the practical tools for using Git:

→ **[Next: Tools & Environment](07%20-%20Tools%20&%20Environment.md)**

---

**← [Back to Index](00%20-%20Index.md)**
