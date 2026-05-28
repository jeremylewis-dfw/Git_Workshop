# A2. Setup & Configuration

**← [Back to Index](00%20-%20Index.md)** | **[Previous: Git Terminology](A1%20-%20Git%20Terminology.md)** | **[Next: Workflow Diagrams →](A3%20-%20Workflow%20Diagrams.md)**

---

## Installation by Operating System

### macOS

**Using Homebrew (recommended):**
```bash
brew install git
git --version
# Verify installation
```

**Using Xcode Command Line Tools:**
```bash
xcode-select --install
# Follow prompts
git --version
```

**Using MacPorts:**
```bash
sudo port install git
```

### Windows

**Using Git for Windows (recommended):**
1. Download from https://git-scm.com/download/win
2. Run installer (accept defaults)
3. Open PowerShell/Command Prompt
4. Verify: `git --version`

**Using Chocolatey:**
```powershell
choco install git
```

**Using Windows Package Manager:**
```powershell
winget install Git.Git
```

**Using WSL (Windows Subsystem for Linux):**
```bash
wsl sudo apt install git
```

### Linux (Ubuntu/Debian)

```bash
sudo apt update
sudo apt install git
git --version  # Verify
```

### Linux (Fedora/Red Hat)

```bash
sudo dnf install git
git --version  # Verify
```

### Linux (Arch)

```bash
sudo pacman -S git
git --version  # Verify
```

---

## Initial Configuration

### Set Your Identity

```bash
# Global (affects all repositories on this machine)
git config --global user.name "Your Full Name"
git config --global user.email "your.email@example.com"

# Project-specific (overrides global for this project)
cd /path/to/project
git config --local user.name "Different Name"
git config --local user.email "different@example.com"

# Verify
git config user.name
git config user.email
git config --list  # See all settings
```

### Configure Your Editor

```bash
# GitHub's default editor
git config --global core.editor "code"        # VS Code
git config --global core.editor "vim"        # Vim
git config --global core.editor "nano"       # Nano
git config --global core.editor "subl"       # Sublime
git config --global core.editor "mate"       # TextMate (Mac)
```

### Line Endings (Important!)

```bash
# Windows (Git automatically converts on commit/checkout)
git config --global core.autocrlf true

# Mac/Linux (Don't convert, respect current format)
git config --global core.autocrlf input

# Disable conversion (use with caution)
git config --global core.autocrlf false
```

### Credentials Management

**macOS (Keychain):**
```bash
git config --global credential.helper osxkeychain
```

**Windows (Manager):**
```bash
git config --global credential.helper manager
```

**Windows (Older):**
```bash
git config --global credential.helper wincred
```

**Linux (If available):**
```bash
git config --global credential.helper cache
# Cache password for 15 minutes
git config --global credential.helper 'cache --timeout=900'
```

**Generic (Not recommended - stores in plain text):**
```bash
git config --global credential.helper store
# Stores in ~/.git-credentials (plaintext!)
```

### Aliases (Optional but Useful)

```bash
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.unstage 'reset HEAD --'
git config --global alias.last 'log -1 HEAD'
git config --global alias.visual 'log --graph --oneline --all --decorate'
```

---

## ~/.gitconfig File

Rather than running multiple config commands, edit your config file directly:

### Location

- **macOS/Linux:** `~/.gitconfig`
- **Windows:** `C:\Users\<username>\.gitconfig`

### Example Full Configuration

```ini
[user]
    name = Your Name
    email = your.email@example.com

[core]
    editor = code
    autocrlf = input
    pager = less -x4
    ignorecase = false
    
[color]
    ui = true
    branch = auto
    diff = auto
    status = auto

[alias]
    st = status
    co = checkout
    br = branch
    ci = commit
    unstage = reset HEAD --
    last = log -1 HEAD
    amend = commit --amend --no-edit
    recent = log --oneline -10
    visual = log --graph --oneline --all --decorate
    undo = reset --soft HEAD~1

[credential]
    helper = osxkeychain

[pull]
    rebase = true

[init]
    defaultBranch = main

[push]
    default = current
```

Edit with: `git config --global --edit`

---

## GitHub SSH Setup (Optional but Recommended)

Allows authentication without entering password every time.

### Generate SSH Key

```bash
ssh-keygen -t ed25519 -C "your.email@example.com"
# or if older system:
ssh-keygen -t rsa -b 4096 -C "your.email@example.com"

# Press Enter for prompts (default location, no passphrase unless desired)
```

### Add to SSH Agent

**macOS:**
```bash
ssh-add ~/.ssh/id_ed25519
```

**Linux/Windows (WSL):**
```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
```

### Add Public Key to GitHub

1. Copy your public key:
```bash
cat ~/.ssh/id_ed25519.pub
# or
pbcopy < ~/.ssh/id_ed25519.pub  # Mac - auto copies to clipboard
```

2. Go to GitHub → Settings → SSH and GPG keys
3. Click "New SSH key"
4. Paste your public key
5. Save

### Verify Setup

```bash
ssh -T git@github.com
# Should output: "Hi <username>! You've successfully authenticated..."
```

---

## .gitignore Setup

### Global .gitignore (Applies to All Repositories)

```bash
# Create global ignore file
touch ~/.gitignore_global

# Configure Git to use it
git config --global core.excludesfile ~/.gitignore_global
```

### Common Patterns to Ignore Globally

```
# IDE files
.vscode/
.idea/
*.swp
*.swo
*~
.project
.classpath

# OS files
.DS_Store
Thumbs.db
.directory

# System files
.env
.env.local
.env.*.local

# User-specific
.local/
secrets/
```

### Project-specific .gitignore

Create `.gitignore` in project root:

```
# Dependencies
node_modules/
venv/
vendor/
__pycache__/

# Build output
dist/
build/
.out/

# Environment
.env
.env.local

# IDE
.vscode/
.idea/

# OS
.DS_Store
Thumbs.db

# Logs
*.log
logs/

# Temporary
tmp/
*.tmp
```

### .gitignore best practices

```bash
# Check what's ignored
git check-ignore -v <path>

# Debug .gitignore issues
git clean -fdx --dry-run           # See what would be removed

# Don't track .gitignore itself (do track it!)
git add .gitignore                 # Always track this file
```

---

## Pre-commit Hooks (Optional)

Automatically run checks before committing:

### Location

`<project>/.git/hooks/pre-commit`

### Example: Prevent Committing Secrets

```bash
#!/bin/bash
# .git/hooks/pre-commit

# Check for common secret patterns
if grep -r "password" .  2>/dev/null | grep -v ".git" | grep -v "test"; then
    echo "Error: Possible password found in code!"
    exit 1
fi

if grep -r "API_KEY" . 2>/dev/null | grep -v ".git" | grep -v "test"; then
    echo "Error: Possible API key found in code!"
    exit 1
fi

exit 0
```

Make executable: `chmod +x .git/hooks/pre-commit`

---

## Verify Installation

```bash
# Check version
git --version

# Check configuration
git config --list

# Try basic operations
mkdir test-repo
cd test-repo
git init
echo "test" > test.txt
git add test.txt
git commit -m "Initial commit"
git log
```

---

## Environment Variables

### GIT_AUTHOR_NAME / GIT_AUTHOR_EMAIL

```bash
export GIT_AUTHOR_NAME="Your Name"
export GIT_AUTHOR_EMAIL="you@example.com"
```

### GIT_EDITOR

```bash
export GIT_EDITOR="code"
```

### GIT_PAGER

```bash
export GIT_PAGER="less -x4"
```

---

## Troubleshooting Installation

### Git Command Not Found (Mac)

```bash
# Check if installed via Xcode
xcode-select --install

# Or install via Homebrew
brew install git

# Verify
which git
```

### Git Command Not Found (Linux)

```bash
# Debian/Ubuntu
sudo apt install git

# Fedora
sudo dnf install git

# Verify
which git
```

### Git Command Not Found (Windows)

```powershell
# Reinstall from https://git-scm.com/download/win
# Make sure to add Git to PATH during installation

# Verify in new terminal
git --version
```

### Wrong Git Version

```bash
# Multiple installations? Check which is used
which git

# Remove old installation
# Reinstall from official source
```

---

## Next Steps

Understand common workflows visually:

→ **[Next: Workflow Diagrams](A3%20-%20Workflow%20Diagrams.md)**

---

**← [Back to Index](00%20-%20Index.md)**
