# Lab 6 — Undoing Mistakes / Git Recovery

## 🎯 Learning Objectives

Participants will:

- Recover from common Git mistakes
- Use restore, reset, revert, and reflog
- Understand safe vs destructive operations
- Build confidence troubleshooting Git

---

## 🧠 Key Recovery Concepts

| Command | Purpose |
|---|---|
| `git restore` | Restore modified files |
| `git reset` | Move branch history |
| `git revert` | Undo safely with a new commit |
| `git reflog` | View HEAD history |

---

## 🛠 Step-by-Step Instructions

### 1. Create and Commit a File

```bash
echo "test" > notes.txt
git add .
git commit -m "Added notes"
```

---

### 2. Accidentally Modify or Delete the File

Simulate a mistake.

---

### 3. Restore the File

```bash
git restore notes.txt
```

---

### 4. Create a Bad Commit

```bash
git commit -m "Oops"
```

---

### 5. Revert the Commit

```bash
git revert <commit>
```

---

### 6. Use Reflog

```bash
git reflog
```

---

### 7. Recover Previous State

Use reflog references to restore earlier repository states.

---

## 💡 Key Takeaways

- `git restore` recovers modified files from the staging area
- `git revert` safely undoes commits by creating a new commit
- `git reset` moves your branch history (use with caution)
- `git reflog` shows a history of HEAD changes — your safety net
- Most Git mistakes are recoverable with these tools
- Understanding recovery options gives you confidence to experiment
