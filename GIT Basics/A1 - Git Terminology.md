# A1. Git Terminology

**← [Back to Index](00%20-%20Index.md)** | **[Next: Setup & Configuration →](A2%20-%20Setup%20&%20Configuration.md)**

---

## Essential Git Terms Defined

### Commit
A permanent snapshot of your entire project at a specific point in time. Identified by a unique SHA-1 hash.

**Example:** `abc123def456...` represents a specific state of all code at that moment.

### Branch
A named pointer to a specific commit. A lightweight reference that tracks a line of development.

**Example:** `main` points to the latest commit on the main branch.

### Main
The default/primary branch in a repository. Typically represents the current production-ready code.

**Historically called `master`, now conventionally `main`.**

### Merge
The process of combining changes from one branch into another branch.

**Example:** `git merge feature/auth` combines feature/auth into current branch.

### Rebase
An advanced technique to reapply commits from one branch onto another, creating a linear history.

**Key difference from merge:** Rebase rewrites history; merge preserves both histories.

### HEAD
The current position reference. Normally points to your current branch, but can point directly to a commit (detached HEAD).

**Example:** `git reset --soft HEAD~1` means "undo the last commit."

### Staging Area (Index)
An intermediate area where you prepare changes before committing.

**Mental model:** Shopping cart before checkout.

### Remote
A version of the repository hosted on another machine (typically GitHub, GitLab, etc.).

**Example:** `origin` is the default remote (where you cloned from).

### Push
The operation of sending your commits to a remote repository.

**Example:** `git push origin main` uploads your commits to the remote's main branch.

### Pull
The operation of getting changes from a remote repository and merging them locally.

**Equivalent to:** `git fetch` + `git merge`

### Fetch
The operation of downloading changes from a remote without merging them.

**Key difference from pull:** Downloaded changes don't affect your working code yet.

### Tag
A permanent label for a specific commit, typically used for releases.

**Example:** `v1.0.0` marks the release version.

### PR/MR (Pull Request / Merge Request)
A request to merge one branch into another, with discussion and review capability.

**GitHub:** Pull Request (PR)  
**GitLab:** Merge Request (MR)  
**Bitbucket:** Pull Request (PR)

### Conflict
Occurs when Git can't automatically merge changes because the same lines were edited differently.

**Manual resolution required.**

### Cherry-pick
Selecting a specific commit from one branch and applying it to another branch.

**Example:** `git cherry-pick abc123d` copies that commit's changes to current branch.

### Stash
Temporarily saving changes without committing, allowing you to switch branches.

**Example:** You're mid-work, need to switch to fix a bug, so `git stash` saves your changes.

### Reflog
Reference log. Git's record of where HEAD has pointed over time.

**Useful for:** Finding lost commits.

### Fast-forward
A merge where the current branch simply advances to the target commit (no merge commit needed).

**Result:** Linear history.

### Detached HEAD
A state where HEAD points directly to a commit, not to a branch.

**Risky:** Commits made here could be lost if not saved to a branch.

### Orphaned commit
A commit that's not reachable from any branch. Typically forgotten or lost.

**Recovery:** View with `git reflog`, create branch to save.

### Upstream
The branch (typically on remote) that your local branch is tracking.

**Example:** Local `main` tracks `origin/main`.

### Origin
The default remote reference (where you cloned from).

**Convention:** Almost every repository has `origin` as primary remote.

### Blob
Git object representing file contents (binary data).

**Part of Git's object model.**

### Tree
Git object representing directory structure.

**Part of Git's object model.**

### SHA-1 Hash
A 40-character hexadecimal string that uniquely identifies a commit.

**Example:** `abc123def456789abcdef123456789abcdef1234`  
**Short form:** `abc123d` (first 7 characters typically sufficient)

### .gitignore
A file specifying which files/directories Git should ignore (not track).

**Example:** `node_modules/`, `.env`, `*.log`

### .git folder
Hidden directory containing Git's repository data (commits, branches, etc.).

**Important:** Don't modify manually!

### Worktree
Your local working directory—the files you see and edit.

**Not to be confused with:** Remote repository or git internal objects.

### Index
Alternative name for the staging area.

**Terms used interchangeably:** Index = Staging Area

### Porcelain
User-friendly Git commands.

**Examples:** `git add`, `git commit`, `git push`  
**Contrast with:** Plumbing

### Plumbing
Low-level, powerful Git commands.

**Examples:** `git cat-file`, `git hash-object`  
**For:** Advanced users, scripting

### Bisect
Binary search through commit history to find which commit introduced a bug.

**Process:** Mark commits as good/bad, Git narrows down the problematic commit.

### Revert
Creating a new commit that undoes changes from a previous commit.

**Safe operation:** Preserves history.

### Reset
Moving current branch to a previous commit.

**Dangerous operation:** Can lose history if used incorrectly.

### Fast-forward merge
A merge where no new merge commit is created (just updating branch pointer).

**Result:** Linear history, cleaner but loses merge context.

### Three-way merge
A merge that creates a merge commit, showing both branches in history.

**Result:** Non-linear history but preserves merge information.

---

## Related Concepts

### Continuous Integration (CI)
Automated testing and building on every commit/PR.

**Purpose:** Catch issues early.

### Continuous Deployment (CD)
Automated deployment to production after tests pass.

**Purpose:** Faster, more reliable releases.

### Version Control System (VCS)
Software that tracks changes to files over time.

**Git is a:** Distributed Version Control System (DVCS)

### Repository (Repo)
A directory containing all Git history and metadata.

Located in the `.git` folder.

### Clone
Creating a local copy of a remote repository (including all history).

**Example:** `git clone https://github.com/user/project.git`

### Fork
Creating your own copy of someone's repository (on a hosting platform like GitHub).

**Different from clone:** Fork is on the server, clone is local.

---

## Confusing Term Pairs

### Merge vs Rebase
- **Merge:** Combines two branches, creates merge commit, preserves history
- **Rebase:** Replays commits, rewrites history, creates linear timeline

### Push vs Pull
- **Push:** Send commits TO remote
- **Pull:** Get commits FROM remote

### Fetch vs Pull
- **Fetch:** Download from remote (no merge)
- **Pull:** Download + merge from remote

### Local vs Remote
- **Local:** Your machine
- **Remote:** Server (GitHub, GitLab, etc.)

### Branch vs Tag
- **Branch:** Moves as you commit (pointer that advances)
- **Tag:** Fixed to a commit (permanent label)

### Commit vs Push
- **Commit:** Permanent local snapshot
- **Push:** Upload commits to remote

### Reset vs Revert
- **Reset:** Move branch pointer (dangerous)
- **Revert:** Create new commit that undoes previous (safe)

### HEAD vs Origin
- **HEAD:** Where you are locally
- **Origin:** Remote location

---

**← [Back to Index](00%20-%20Index.md)**
