# Git Quick Reference Guide

A quick reference for the most commonly used Git commands and workflows.

---

## Setup and Configuration

```bash
# Set your identity
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# Set default branch name
git config --global init.defaultBranch main

# View configuration
git config --list

# Set default editor
git config --global core.editor "code --wait"  # VS Code
git config --global core.editor "vim"          # Vim
```

---

## Creating Repositories

```bash
# Create new repository
git init

# Clone existing repository
git clone https://github.com/username/repo.git

# Clone to specific directory
git clone https://github.com/username/repo.git my-folder
```

---

## Basic Workflow

```bash
# Check status
git status

# Add files to staging
git add filename.txt           # Specific file
git add .                      # All files
git add *.js                   # All JavaScript files
git add directory/             # All files in directory

# Commit changes
git commit -m "Commit message"
git commit -am "Message"       # Add and commit tracked files

# View history
git log                        # Full history
git log --oneline              # Condensed
git log --graph --all          # Visual graph
git log -5                     # Last 5 commits

# View changes
git diff                       # Unstaged changes
git diff --staged              # Staged changes
git diff branch1 branch2       # Between branches
```

---

## Branching

```bash
# List branches
git branch                     # Local branches
git branch -a                  # All branches
git branch -r                  # Remote branches

# Create branch
git branch branch-name         # Create only
git checkout -b branch-name    # Create and switch
git switch -c branch-name      # Create and switch (newer)

# Switch branches
git checkout branch-name
git switch branch-name         # Newer syntax

# Delete branch
git branch -d branch-name      # Delete if merged
git branch -D branch-name      # Force delete

# Rename branch
git branch -m old-name new-name
```

---

## Merging

```bash
# Merge branch into current branch
git merge branch-name

# Merge with no fast-forward (create merge commit)
git merge --no-ff branch-name

# Squash merge (combine all commits)
git merge --squash branch-name

# Abort merge
git merge --abort
```

---

## Rebasing

```bash
# Rebase current branch onto another
git rebase branch-name

# Interactive rebase (last 3 commits)
git rebase -i HEAD~3

# Continue after resolving conflicts
git rebase --continue

# Skip commit
git rebase --skip

# Abort rebase
git rebase --abort
```

---

## Remote Repositories

```bash
# View remotes
git remote -v

# Add remote
git remote add origin https://github.com/user/repo.git
git remote add upstream https://github.com/original/repo.git

# Remove remote
git remote remove origin

# Rename remote
git remote rename origin new-name

# Fetch from remote
git fetch origin
git fetch --all                # All remotes

# Pull from remote
git pull origin main
git pull --rebase origin main  # Pull with rebase

# Push to remote
git push origin main
git push -u origin branch-name # Set upstream and push
git push --all                 # Push all branches
git push --tags                # Push all tags
```

---

## Undoing Changes

```bash
# Discard changes in working directory
git checkout -- filename.txt
git restore filename.txt       # Newer syntax

# Unstage files
git reset HEAD filename.txt
git restore --staged filename.txt  # Newer syntax

# Undo last commit (keep changes)
git reset HEAD~1
git reset --soft HEAD~1        # Keep in staging

# Undo last commit (discard changes)
git reset --hard HEAD~1

# Revert commit (create new commit)
git revert commit-hash

# Amend last commit
git commit --amend
git commit --amend --no-edit   # Keep same message
```

---

## Stashing

```bash
# Save work temporarily
git stash
git stash save "Description"

# List stashes
git stash list

# Apply most recent stash
git stash apply
git stash pop                  # Apply and remove

# Apply specific stash
git stash apply stash@{2}

# Delete stash
git stash drop stash@{0}
git stash clear                # Delete all stashes
```

---

## Tags

```bash
# List tags
git tag
git tag -l "v1.*"              # Pattern match

# Create tag
git tag v1.0.0
git tag -a v1.0.0 -m "Version 1.0.0"  # Annotated tag

# Push tags
git push origin v1.0.0
git push origin --tags         # Push all tags

# Delete tag
git tag -d v1.0.0              # Local
git push origin --delete v1.0.0  # Remote
```

---

## Inspection

```bash
# Show commit details
git show commit-hash
git show HEAD                  # Latest commit

# Show file at specific commit
git show commit-hash:filename.txt

# Blame (who changed what)
git blame filename.txt

# Search commits
git log --grep="bug fix"
git log --author="John"
git log --since="2 weeks ago"
git log -- filename.txt        # File history
```

---

## Cleaning

```bash
# Remove untracked files (dry run)
git clean -n

# Remove untracked files
git clean -f

# Remove untracked files and directories
git clean -fd

# Remove ignored files too
git clean -fdx
```

---

## Common Workflows

### Feature Branch Workflow

```bash
git checkout main
git pull origin main
git checkout -b feature/new-feature
# ... make changes ...
git add .
git commit -m "Add new feature"
git push origin feature/new-feature
# Create PR on GitHub
# After merge:
git checkout main
git pull origin main
git branch -d feature/new-feature
```

### Updating Feature Branch

```bash
git checkout feature-branch
git fetch origin
git merge origin/main
# Or with rebase:
git rebase origin/main
```

### Fixing Merge Conflicts

```bash
# After git merge or git pull
# Edit conflicted files
# Look for <<<<<<< ======= >>>>>>>
# Remove markers and choose what to keep
git add filename.txt
git commit -m "Resolve merge conflict"
```

### Forking Workflow

```bash
# Fork on GitHub, then:
git clone https://github.com/you/repo.git
cd repo
git remote add upstream https://github.com/original/repo.git
git checkout -b feature-branch
# ... make changes ...
git push origin feature-branch
# Create PR on GitHub

# Sync fork:
git checkout main
git pull upstream main
git push origin main
```

---

## Branch Protection Rules (GitHub)

```
Settings → Branches → Add rule

✓ Require pull request reviews before merging
✓ Require status checks to pass before merging
✓ Require conversation resolution before merging
✓ Require linear history
✓ Include administrators
```

---

## Useful Aliases

Add to `~/.gitconfig`:

```ini
[alias]
    st = status
    co = checkout
    br = branch
    ci = commit
    lg = log --oneline --graph --all
    last = log -1 HEAD
    unstage = reset HEAD --
    aliases = config --get-regexp alias
```

Usage:
```bash
git st        # Instead of git status
git co main   # Instead of git checkout main
git lg        # Pretty log
```

---

## Commit Message Conventions

```bash
# Format
<type>(<scope>): <subject>

<body>

<footer>

# Types
feat:     New feature
fix:      Bug fix
docs:     Documentation
style:    Formatting
refactor: Code restructuring
test:     Adding tests
chore:    Maintenance

# Examples
feat(auth): add OAuth2 login support
fix(api): handle null response in user endpoint
docs: update installation instructions
test: add unit tests for login flow
```

---

## Emergency Commands

```bash
# I committed to wrong branch!
git branch feature-branch      # Create branch with current commit
git reset --hard HEAD~1        # Remove commit from current branch
git checkout feature-branch    # Switch to correct branch

# I need to undo everything!
git reset --hard HEAD          # Discard all changes

# I deleted something important!
git reflog                     # Find lost commits
git checkout commit-hash       # Recover

# I pushed something bad!
git revert commit-hash         # Safe: creates new commit
git push origin main
# Or (dangerous):
git reset --hard HEAD~1
git push origin main --force
```

---

## Getting Help

```bash
# General help
git help
git help -a                    # All commands

# Command-specific help
git help commit
git commit --help
```

---

## Common Patterns

```bash
# See what would be pushed
git diff origin/main main

# See what would be pulled
git fetch
git diff main origin/main

# Delete all merged branches
git branch --merged | grep -v "\*" | xargs -n 1 git branch -d

# Find commit that introduced bug
git bisect start
git bisect bad                 # Current is bad
git bisect good v1.0          # v1.0 was good
# Git checks out middle commit
# Test and mark:
git bisect good                # or bad
# Repeat until found
git bisect reset               # Done
```

---

**Pro Tips:**
- Commit often, perfect later
- Write clear commit messages
- Pull before push
- Never rebase public branches
- Use branches for all work
- Delete merged branches
- Back up your work (push regularly)

---

*For detailed explanations, refer to the full course sections.*
