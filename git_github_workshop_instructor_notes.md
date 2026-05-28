# Git + GitHub Workshop — Instructor Notes

## Workshop Goals

By the end of this workshop, participants should:
- Understand core Git concepts and terminology
- Use Git locally with confidence
- Collaborate using branches and GitHub
- Resolve merge conflicts
- Recover from common mistakes
- Understand modern team workflows


# Why Git Is Better Than Simple Storage

A great way to explain Git to beginners is:

> “Git is not just storage — it’s a time machine + collaboration system for files.”

Most people already understand:
- folders,
- Dropbox,
- OneDrive,
- Google Drive.

So compare Git directly against those.

---

# Simple Analogy

## Simple Storage
A normal folder is like:
> Keeping only the latest version of a document.

Problems:
- You overwrite work
- Hard to undo changes
- No history
- No idea WHO changed WHAT
- Collaboration becomes messy

Example:
```text
Resume.docx
Resume_v2.docx
Resume_FINAL.docx
Resume_FINAL_REAL.docx
```

Everyone laughs because they’ve lived this.

---

# Git Analogy

Git is like:
> Google Docs revision history + save points + multiplayer collaboration for code.

Git remembers:
- every change,
- who made it,
- when they made it,
- WHY they made it.

And you can:
- go backwards,
- compare versions,
- safely experiment,
- collaborate without overwriting others.

---

# Best Real-World Explanation

## Without Git
Imagine 5 people editing the same Word document by emailing copies around.

You get:
```text
Report_FINAL.docx
Report_FINAL_v2.docx
Report_FINAL_USE_THIS_ONE.docx
```

Chaos.

---

## With Git
Git acts like:
- a traffic controller,
- history tracker,
- backup system,
- collaboration manager.

Everyone works safely at the same time.

---

# Core Concept to Emphasize

## Storage Only Answers:
> “What files exist right now?”

## Git Answers:
- What changed?
- Who changed it?
- Why was it changed?
- When was it changed?
- Can we undo it?
- Can we compare versions?
- Can multiple people work safely?

That’s the HUGE difference.

---

# Beginner-Friendly Comparison Table

| Simple Storage | Git |
|---|---|
| Stores files | Stores file history |
| Overwrites changes | Tracks every change |
| Manual backups | Automatic history |
| Hard to collaborate | Built for collaboration |
| No rollback | Easy rollback |
| No branching | Safe experimentation |
| Limited visibility | Full audit trail |

---

# Best Demonstration

The BEST way to prove Git’s value live:

## Demo 1 — Simple Folder
1. Create a text file
2. Make changes
3. Overwrite content
4. Ask:
> “How do we get version 2 back?”

Most people realize:
> “We can’t.”

---

## Demo 2 — Git
1. Commit several versions
2. Show:
```bash
git log
```

Then:
```bash
git checkout <old_commit>
```

Watch their reaction when history instantly reappears.

That moment usually “clicks.”

---

# Another Strong Analogy

## Git Is Like Video Game Save Files

Without Git:
- one save slot,
- mistakes permanent.

With Git:
- unlimited save points,
- can go back anytime,
- can try risky things safely.

Developers immediately understand this analogy.

---

# Explain Branches This Way

Branches are:
> “Alternate timelines.”

You can:
- experiment,
- test ideas,
- build features,

without damaging the main version.

Then merge successful work back in.

That analogy works extremely well.

---

# One-Sentence Executive Summary

If you need a concise explanation:

> “Cloud storage stores files. Git stores the entire history and evolution of a project.”


---

# Lab 1 — Git Basics: “My First Repo”

## Primary Learning Objectives

### Understand What Git Actually Is
Participants should understand:
- Git is a distributed version control system
- Git tracks changes over time
- Git allows rollback and collaboration
- Git stores snapshots, not just diffs

### Understand the Git Workflow
Students should learn the lifecycle:

Working Directory → Staging Area → Repository

This is the MOST important conceptual model in Git.

### Understand Common Commands
Participants should know:
- `git init`
- `git status`
- `git add`
- `git commit`
- `git log`

---

## Important Teaching Points

### Explain the Three States
Use diagrams if possible:
- Modified
- Staged
- Committed

Most beginner confusion comes from not understanding staging.

### Emphasize Commit Quality
Teach:
- Small commits
- Meaningful commit messages
- One logical change per commit

Good example:
```bash
git commit -m "Add workshop introduction section"
```

Bad example:
```bash
git commit -m "stuff"
```

### Explain Why `git status` Matters
Teach students:
> “When confused, run git status.”

---

# Lab 2 — Branching Fundamentals

## Primary Learning Objectives

### Understand Why Branches Exist
Students should understand:
- Branches isolate work
- Multiple developers can work simultaneously
- Main branch remains stable

### Understand Branch Workflow
Students should learn:
- Create branch
- Switch branch
- Commit work
- Merge back to main

---

## Important Teaching Points

### Branches Are Lightweight
Many beginners think branches are expensive or dangerous.

Explain:
- Branches are cheap
- Teams create branches constantly

### Explain HEAD
Simple explanation:
> “HEAD points to your current branch.”

### Visualize Branches
Draw:
```text
main
  \
   feature-login
```

Visualization dramatically improves understanding.

---

# Lab 3 — Merge Conflicts

## Primary Learning Objectives

### Remove Fear Around Conflicts
Critical takeaway:
> Merge conflicts are NORMAL.

### Understand Conflict Markers
Students should recognize:
```text
<<<<<<< HEAD
Current branch code
=======
Incoming code
>>>>>>> feature-branch
```

### Learn Manual Resolution
Students should:
- Edit files manually
- Decide final content
- Complete merge

---

## Important Teaching Points

### Conflicts Are NOT Failures
Explain:
- Git cannot decide intent
- Humans resolve semantic meaning

### Slow Down Here
This lab is usually emotionally stressful for beginners.

Walk through carefully.

---

# Lab 4 — GitHub Collaboration Workflow

## Primary Learning Objectives

### Understand Git vs GitHub
Students should know:
- Git = version control system
- GitHub = hosting/collaboration platform

### Learn Remote Workflow
Participants should understand:
- push
- pull
- clone
- origin

### Understand Pull Requests
Students should learn:
- PRs enable code review
- PRs support discussion
- PRs are central to team workflows

---

# Lab 5 — Rebasing and Clean History

## Primary Learning Objectives

### Understand Rebase vs Merge
Students should understand:
- Merge preserves branch history
- Rebase creates linear history

### Learn Commit Cleanup
Participants should:
- squash commits
- reorder commits
- edit messages

---

# Lab 6 — Undoing Mistakes / Git Recovery

## Primary Learning Objectives

### Build Confidence
Main objective:
> Git mistakes are recoverable.

### Learn Recovery Commands
Students should understand:
- `git restore`
- `git reset`
- `git revert`
- `git reflog`

---

# Suggested Instructor Timing

| Lab | Suggested Time |
|---|---|
| Git Basics | 25 min |
| Branching | 25 min |
| Merge Conflicts | 35 min |
| GitHub Workflow | 35 min |
| Rebasing | 25 min |
| Recovery | 30 min |

---

# Final Takeaways

Students should leave understanding:
- Git is a safety net, not a danger
- Collaboration workflows are manageable
- Mistakes are recoverable
- Branches and PRs are core modern development tools
