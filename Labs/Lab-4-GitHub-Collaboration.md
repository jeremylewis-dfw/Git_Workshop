# Lab 4 — GitHub Collaboration Workflow

## 🎯 Learning Objectives

Participants will:

- Push code to GitHub
- Work with remote repositories
- Create Pull Requests
- Review teammate changes
- Merge approved work

---

## 🌐 Git vs GitHub

| Git | GitHub |
|---|---|
| Version control system | Collaboration platform |
| Runs locally | Cloud-hosted |
| Tracks history | Enables team workflows |

---

## 🛠 Step-by-Step Instructions

### 1. Create a GitHub Repository

Create a new repository on GitHub.

---

### 2. Connect Local Repository

```bash
git remote add origin <repository-url>
```

---

### 3. Push the Repository

```bash
git push -u origin main
```

---

### 4. Create a Feature Branch

```bash
git switch -c feature-update
```

---

### 5. Push the Branch

```bash
git push -u origin feature-update
```

---

### 6. Open a Pull Request

- Compare your branch against `main`
- Add a description
- Request reviews

---

### 7. Merge the Pull Request

Merge approved changes into the main branch.

---

## 💡 Key Takeaways

- GitHub is where teams collaborate on Git repositories
- `git remote add origin` connects your local repo to GitHub
- `git push` sends your commits to GitHub
- Pull Requests enable code review and discussion before merging
- GitHub workflows keep teams organized and code quality high
