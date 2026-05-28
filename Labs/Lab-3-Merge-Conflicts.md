# Lab 3 — Merge Conflicts

## 🎯 Learning Objectives

Participants will:

- Understand how merge conflicts happen
- Read Git conflict markers
- Resolve conflicts manually
- Complete merges successfully

---

## ⚠️ Conflict Example

Git displays conflicts like this:

```text
<<<<<<< HEAD
Current branch version
=======
Incoming branch version
>>>>>>> feature-branch
```

---

## 🛠 Step-by-Step Instructions

### 1. Create a Conflict Scenario

Two users edit the same line in `README.md`.

---

### 2. User A Commits and Pushes

```bash
git add .
git commit -m "User A changes"
git push
```

---

### 3. User B Pulls or Merges

```bash
git pull
```

Observe the merge conflict.

---

### 4. Open the Conflicted File

Review the conflict markers carefully.

---

### 5. Resolve the Conflict

Edit the file manually and keep the desired final version.

---

### 6. Finalize the Merge

```bash
git add .
git commit
```

---

## 💡 Key Takeaways

- Merge conflicts happen when two branches edit the same lines
- Conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`) show the conflicting versions
- Resolution is manual — you choose what to keep
- After resolving, stage and commit to complete the merge
- Conflicts are normal and manageable in team development
