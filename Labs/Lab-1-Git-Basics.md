# Lab 1 — Git Basics: My First Repo

## 🎯 Learning Objectives

By the end of this lab, participants will:

- Understand the basic Git workflow
- Learn how to initialize a repository
- Understand the staging area
- Create meaningful commits
- View repository history

---

## 📚 Core Concepts

### The Git Workflow

```text
Working Directory → Staging Area → Repository
```

### Important Commands

| Command | Purpose |
|---|---|
| `git init` | Initialize a new repository |
| `git status` | View repository status |
| `git add` | Stage files for commit |
| `git commit` | Save changes to history |
| `git log` | View commit history |

---

## 🛠 Step-by-Step Instructions

### 1. Create a Project Folder

```bash
mkdir git-workshop
cd git-workshop
```

### 2. Initialize Git

```bash
git init
```

### 3. Create a README File

```bash
echo "# Git Workshop" > README.md
```

### 4. Check Repository Status

```bash
git status
```

### 5. Stage the File

```bash
git add README.md
```

### 6. Commit the Changes

```bash
git commit -m "Initial commit"
```

### 7. View Commit History

```bash
git log --oneline
```

---

## 💡 Key Takeaways

- Every Git repository starts with `git init`
- The staging area (`git add`) lets you choose what to commit
- Commits create save points in your project history
- `git log` shows the complete history of your work
