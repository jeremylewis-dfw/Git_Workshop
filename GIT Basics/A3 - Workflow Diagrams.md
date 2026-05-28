# A3. Workflow Diagrams

**← [Back to Index](00%20-%20Index.md)** | **[Previous: Setup & Configuration](A2%20-%20Setup%20&%20Configuration.md)**

---

## Visual Git Workflows

### The Three-Tree Architecture

```
┌─────────────────────────────────────────────────────┐
│             Your Working Directory                  │
│         (Files you edit in your editor)             │
│                                                     │
│  ├─ app.js (modified)                              │
│  ├─ config.js (modified)                           │
│  └─ README.md (unchanged)                          │
│                                                     │
│  "git add" selectively stages changes              │
│                 ↓                                   │
├─────────────────────────────────────────────────────┤
│            Staging Area (Index)                     │
│    (Changes ready for next commit)                 │
│                                                     │
│  ├─ app.js (staged)                                │
│  └─ config.js (staged)                             │
│  (README.md not staged)                            │
│                                                     │
│  "git commit" makes these permanent                │
│                 ↓                                   │
├─────────────────────────────────────────────────────┤
│           Git Repository (.git)                     │
│    (Permanent history, immutable)                  │
│                                                     │
│  Commit A: entire project snapshot                 │
│  Commit B: entire project snapshot                 │
│  Commit C: entire project snapshot (HEAD points)   │
│                                                     │
└─────────────────────────────────────────────────────┘
```

### Commit Chain (Linear History)

```
Development Timeline:

Working directory
      ↓
  git add .
      ↓
  git commit -m "msg1"
      ↓
┌───────────────────────────────────────┐
│        Repository History             │
├───────────────────────────────────────┤
│                                       │
│   Commit A ← Commit B ← Commit C      │
│   (past)     (recent)   (HEAD here)   │
│                                       │
│  Each commit points to parent         │
│  Forms an unbreakable chain           │
│                                       │
└───────────────────────────────────────┘
```

### Branching Model

```
                     ← main branch pointer
                     ↓
timeline:  ──A──B──C  (main)
              │   │
              └─D─E─F (feature branch)
                   ↑
                   └─ feature branch pointer

When you work on a feature:
1. Create new branch: points to current commit
2. Make commits: branch pointer advances
3. Meanwhile: main branch unchanged
4. When done: merge feature back to main

Result after merge:
──A──B──C────G
     └─D─E─F┘

Merge commit G combines both branches
```

### Feature Development Workflow

```
Day 1: Create Feature Branch
────────────────────────────────────
main:    ───A──B──C
              ↓
         git checkout -b feature/auth
              ↓
feature: ───A──B──C   (same as main initially)


Day 1-3: Development
────────────────────────────────────
main:    ───A──B──C          (unchanged)
              │
feature: ───A──B──C──D──E──F (your work)


Day 4: Merge Back
────────────────────────────────────
After merge:
main:    ───A──B──C─────G
              └─D──E──F┘
                       ↑
                   merge commit
                   
Or after rebase:
main:    ───A──B──C──D──E──F
```

### Push and Pull Workflow

```
Developer A             GitHub Server          Developer B
┌─────────┐            ┌───────────┐            ┌─────────┐
│ Local   │            │  Remote   │            │ Local   │
│  Repo   │            │   Repo    │            │  Repo   │
└─────────┘            └───────────┘            └─────────┘
    │                        ↑                       │
    │      git push          │                       │
    ├───────────────────────→│                       │
    │                        │                       │
    │                        │      git pull         │
    │                        │←──────────────────────┤
    │                        │                       │
    │                  (Sync complete)              │
    │                        │                       │
```

### Merge Conflict Scenario

```
Developer A Changes      Conflict!        Developer B Changes
┌──────────────────────────────────────────────────────────────┐
│ auth.js line 15:       CONFLICT        auth.js line 15:      │
│ ✗ const token = ...      (same line)    ✗ const token = ...  │
│  generateToken()       edited by both   jwt.sign()           │
│  ↓                     developers       ↓                     │
│  Merge fails, manual resolution needed                        │
└──────────────────────────────────────────────────────────────┘

Resolution:
┌──────────────────────────────────────────────────────────────┐
│ Choose one version, or combine both intelligently:          │
│                                                              │
│ ✓ const token = jwt.sign({user: id});                       │
│   return {token, expires: Date.now() + 3600000};            │
│                                                              │
│ Commit merge → Continue                                      │
└──────────────────────────────────────────────────────────────┘
```

### Rebase vs Merge

```
Starting state:
─ A ─ B ─ C ─ main
  └─ D ─ E ─ feature

MERGE APPROACH:
─ A ─ B ─ C ─ G ─ main (G is merge commit)
  └─ D ─ E ─ ┘
  
Result: Non-linear, but preserves both histories
Command: git merge feature

REBASE APPROACH:
─ A ─ B ─ C ─ D' ─ E' ─ feature, main
  (D and E replayed on top of C)

Result: Linear history, easier to follow
Command: git rebase main (from feature branch)

Trade-off: Rebase = cleaner history but rewrites commits
         Merge = preserves original history but non-linear
```

### Pull Request Workflow (GitHub)

```
                          Developer
                              ↓
                    ┌─────────────────────┐
                    │   Feature Branch    │
                    │   (local work)      │
                    │                     │
                    │ Commit A            │
                    │ Commit B            │
                    │ Commit C            │
                    └──────────┬──────────┘
                               ↓
                        git push origin
                               ↓
                    ┌─────────────────────┐
                    │   Remote Repository │
                    │   (GitHub)          │
                    │                     │
                    │ feature branch      │
                    │ ├─ Commit A         │
                    │ ├─ Commit B         │
                    │ └─ Commit C         │
                    └──────────┬──────────┘
                               ↓
                     Create Pull Request
                               ↓
                    ┌─────────────────────┐
                    │  Code Review        │
                    │                     │
                    │ - Reviewer 1: ✓     │
                    │ - Reviewer 2: ✗     │
                    │   "needs changes"   │
                    └──────────┬──────────┘
                               ↓
                     Make requested changes
                               ↓
                        git push origin
                               ↓
                    ┌─────────────────────┐
                    │  Re-Review          │
                    │                     │
                    │ - Reviewer 2: ✓     │
                    │ - All approved!     │
                    └──────────┬──────────┘
                               ↓
                    Merge to main branch
                               ↓
                    ┌─────────────────────┐
                    │   Main Production   │
                    │   Features go live! │
                    └─────────────────────┘
```

### Emergency Hotfix Workflow

```
PRODUCTION ISSUE DETECTED
        ↓
   ┌────────────────────────────┐
   │ Create hotfix branch from  │
   │ main (current production)  │
   └────┬───────────────────────┘
        │
        ↓ (Fast turnaround)
   ┌─────────────────────┐
   │ Fix the bug (30min) │
   │ Test it works       │
   │ Commit             │
   └────┬────────────────┘
        │
        ↓
   Urgent code review
   (expedited approval)
        │
        ↓
   ┌──────────────────────────────┐
   │ Merge to main (production)   │
   │ Deploy immediately!          │
   └────┬───────────────────────┬─┘
        │                       │
        ↓                       ↓
   ┌──────────┐          ┌──────────────────┐
   │  Live!   │          │ Also merge to    │
   │  Issue   │          │ develop branch   │
   │ resolved │          │ (bug fix ready)  │
   └──────────┘          └──────────────────┘
```

### Revert vs Reset

```
REVERT (Safe - Creates new commit undoing the bad commit)
──────────────────────────────────────────────────────>>>>
Commit A ← Commit B (BAD!) ← Commit C ← Commit D (Revert B)
                                              ↑
                                     New commit undoes B
Result: Both commits visible in history
Status: Safe for shared branches

RESET (Dangerous - Rewrites history)
───────────────────────────────────<<<<
Commit A ← Commit B (BAD!) ← Commit C ← (deleted - gone)
              ↑
          HEAD now here
Result: Commit B and C erased from history
Status: Lose work, only use locally!
```

### Git Reflog (Finding Lost Commits)

```
Operations you performed:

git checkout -b feature1
    ↓: HEAD points to feature1
    └─ Logged in reflog

git commit -m "new work"
    ↓: HEAD advances
    └─ Logged in reflog

git checkout main
    ↓: HEAD moves to main
    └─ Logged in reflog

git branch -D feature1
    ↓: Deleted branch, but commit still exists
    └─ Logged in reflog (just orphaned)

View with: git reflog
────────────────────────
abc123d HEAD@{0}: checkout: moving to main
def456g HEAD@{1}: commit: new work
ghi789j HEAD@{2}: checkout: -b feature1

Recovery: git reset --hard def456g
         (commits are back!)
```

### Clone and Contribution Workflow

```
Original Project (upstream)
    ↓
Fork to Your Account (origin)
    ↓
┌──────────────────────────────┐
│ git clone <your-fork>        │
│ Creates local copy           │
│ Automatically sets:          │
│   origin = your-fork         │
│   (can add upstream ref)     │
└──────────────────┬───────────┘
                   ↓
         ┌─ Local Development
         │
    ├─ Create feature branch
    │
    ├─ Make commits
    │
    ├─ Push to origin (your fork)
    │
    ├─ Create Pull Request
    │  (your fork → original project)
    │
    ├─ Maintainers review
    │
    └─ Merged to original upstream!
```

### Typical Day: Developer Workflow

```
Morning:
┌──────────────────┐
│ git pull origin  │ ← Get latest from team
│ main             │
└────────┬─────────┘
         ↓
┌───────────────────────────┐
│ git checkout -b          │ ← Start feature
│ feature/new-dashboard    │
└────────┬─────────────────┘
         ↓

During the day:
         ├─ Edit files
         ├─ git add (stage changes)
         ├─ git commit (save locally)
         └─ (repeat multiple times)
         
Afternoon:
         ├─ git push -u origin feature/new-dashboard
         ├─ Create PR on GitHub
         ├─ Request code review
         └─ Make suggested changes
         
         ├─ git add (additional changes)
         ├─ git commit
         ├─ git push origin feature/new-dashboard
         └─ (auto-updates PR)

End of day:
         ├─ Get PR approved
         ├─ PR merged to main
         ├─ Delete feature branch
         └─ Tomorrow: pull latest main
```

---

## Summary: Visual Git Model

```
Files       Staging     Repository    Remote
────────────────────────────────────────────

Edited  ──→ add file ──→ Commit ──→ Push ──→ GitHub
            (staging)   (local)    (upload)
```

---

**← [Back to Index](00%20-%20Index.md)**
