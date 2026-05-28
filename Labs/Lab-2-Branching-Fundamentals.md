# Lab 2 — Branching Fundamentals

## 🎯 Learning Objectives

Participants will learn:

- Why branches exist
- How branches isolate work
- How to create and switch branches
- How to merge feature work safely

---

## 🌳 Branching Overview

Branches allow developers to work independently without affecting the main branch.

```text
main
  \
   feature-yourname
```

---

## 🛠 Step-by-Step Instructions

### 1. Create a Feature Branch

```bash
git switch -c feature-yourname
```

### 2. Modify the README File

Add a section with your:
- Name
- Favorite programming language
- Favorite development tool

---

### 3. Stage Changes

```bash
git add .
```

### 4. Commit Changes

```bash
git commit -m "Added feature section"
```

### 5. Return to Main

```bash
git switch main
```

### 6. Merge the Feature Branch

```bash
git merge feature-yourname
```

### 7. Visualize Branch History

```bash
git log --graph --oneline --all
```

---

## 💡 Key Takeaways

- Branches let you work on features without affecting the main branch
- Use `git switch -c` to create and switch to a new branch
- Use `git switch` to move between branches
- `git merge` combines branches together
- Branches are safe, lightweight ways to experiment
