# Lab 5 — Rebasing and Clean History

## 🎯 Learning Objectives

Participants will:

- Understand merge vs rebase
- Clean up commit history
- Squash unnecessary commits
- Use interactive rebase

---

## 🔄 Merge vs Rebase

| Merge | Rebase |
|---|---|
| Preserves branch history | Creates linear history |
| Easier for beginners | Cleaner project history |
| Adds merge commits | Rewrites commit history |

---

## 🛠 Step-by-Step Instructions

### 1. Create Multiple Small Commits

Make several small edits and commits.

---

### 2. Start Interactive Rebase

```bash
git rebase -i HEAD~3
```

---

### 3. Squash Commits

Change:

```text
pick
```

to:

```text
squash
```

for commits you want combined.

---

### 4. Save and Close the Editor

Git will combine the commits.

---

### 5. Review the Updated History

```bash
git log --oneline
```

---

## ⚠️ Important Warning

Avoid rebasing shared public branches unless your team agrees on the workflow.

---

## 💡 Key Takeaways

- Rebasing creates a clean, linear history
- Interactive rebase (`git rebase -i`) lets you squash and reorganize commits
- Squashing combines multiple commits into one
- Rebasing is powerful but changes history — use with caution on shared branches
- A clean history makes project maintenance easier
