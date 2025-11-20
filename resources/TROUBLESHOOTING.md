# Git and GitHub Troubleshooting Guide

Common problems and their solutions.

---

## Table of Contents

1. [Authentication Issues](#authentication-issues)
2. [Merge Conflicts](#merge-conflicts)
3. [Commit Problems](#commit-problems)
4. [Branch Issues](#branch-issues)
5. [Remote Repository Problems](#remote-repository-problems)
6. [Undoing Mistakes](#undoing-mistakes)
7. [Performance Issues](#performance-issues)
8. [Configuration Problems](#configuration-problems)

---

## Authentication Issues

### Problem: Permission Denied (publickey)

```bash
# Error
Permission denied (publickey).
fatal: Could not read from remote repository.
```

**Solution 1: Use HTTPS instead of SSH**
```bash
# Change remote URL
git remote set-url origin https://github.com/username/repo.git
```

**Solution 2: Setup SSH Key**
```bash
# Generate SSH key
ssh-keygen -t ed25519 -C "your_email@example.com"

# Start ssh-agent
eval "$(ssh-agent -s)"

# Add key
ssh-add ~/.ssh/id_ed25519

# Copy public key
cat ~/.ssh/id_ed25519.pub

# Add to GitHub:
# Settings → SSH and GPG keys → New SSH key
# Paste the public key
```

### Problem: Username/Password Not Working

**Solution: Use Personal Access Token**
```bash
# GitHub no longer accepts passwords
# Generate token at: Settings → Developer settings → Personal access tokens

# Use token as password when prompted
# Or configure credential helper:
git config --global credential.helper cache

# For Mac:
git config --global credential.helper osxkeychain

# For Windows:
git config --global credential.helper wincred
```

---

## Merge Conflicts

### Problem: Merge Conflict

```bash
# Error
CONFLICT (content): Merge conflict in file.txt
Automatic merge failed; fix conflicts and then commit the result.
```

**Solution:**
```bash
# 1. Open conflicted file
# Look for conflict markers:
<<<<<<< HEAD
Your changes
=======
Their changes
>>>>>>> branch-name

# 2. Edit file - choose what to keep
# Remove conflict markers

# 3. Stage resolved file
git add file.txt

# 4. Complete merge
git commit -m "Resolve merge conflict in file.txt"
```

### Problem: Too Many Conflicts

**Solution: Abort and Try Different Approach**
```bash
# Abort merge
git merge --abort

# Try rebasing instead (if safe)
git rebase branch-name

# Or break into smaller merges
git merge --no-commit branch-name
# Fix conflicts gradually
git commit
```

---

## Commit Problems

### Problem: Wrong Commit Message

**Solution:**
```bash
# Change last commit message
git commit --amend -m "Correct message"

# If already pushed:
git push origin branch-name --force-with-lease
```

### Problem: Forgot to Add Files to Commit

**Solution:**
```bash
# Add forgotten files
git add forgotten-file.txt

# Amend last commit
git commit --amend --no-edit

# If already pushed:
git push origin branch-name --force-with-lease
```

### Problem: Committed to Wrong Branch

**Solution:**
```bash
# Create new branch with commit
git branch correct-branch

# Remove commit from current branch
git reset --hard HEAD~1

# Switch to correct branch
git checkout correct-branch
```

### Problem: Committed Sensitive Data

**Solution:**
```bash
# Remove file from last commit
git rm --cached sensitive-file.txt
git commit --amend -m "Remove sensitive file"

# If already pushed:
# Use BFG Repo-Cleaner or git filter-branch
# https://github.com/rtyley/bfg-repo-cleaner

# Add to .gitignore
echo "sensitive-file.txt" >> .gitignore
git add .gitignore
git commit -m "Update .gitignore"

# Rotate any exposed secrets!
```

---

## Branch Issues

### Problem: Can't Delete Branch

```bash
# Error
error: The branch 'branch-name' is not fully merged.
```

**Solution:**
```bash
# Force delete
git branch -D branch-name

# Or merge it first
git checkout main
git merge branch-name
git branch -d branch-name
```

### Problem: Lost Branch After Delete

**Solution:**
```bash
# Find deleted branch in reflog
git reflog

# Recreate branch at that commit
git branch branch-name commit-hash
```

### Problem: Accidentally Deleted Branch with Unpushed Work

**Solution:**
```bash
# Find commits in reflog
git reflog

# Look for branch commits
# Find the commit hash

# Create new branch at that commit
git checkout -b recovered-branch commit-hash
```

---

## Remote Repository Problems

### Problem: Can't Push - Non-Fast-Forward

```bash
# Error
! [rejected] main -> main (non-fast-forward)
error: failed to push some refs to 'origin'
```

**Solution:**
```bash
# Pull first
git pull origin main

# Resolve any conflicts, then:
git push origin main

# If you're sure (dangerous):
git push origin main --force
# Better:
git push origin main --force-with-lease
```

### Problem: Push Rejected - Branch Protected

```bash
# Error
refusing to allow an OAuth App to create or update workflow
```

**Solution:**
- Create pull request instead
- Don't push directly to protected branches
- Contact repository admin if needed

### Problem: Large File Error

```bash
# Error
remote: error: File large-file.zip is 123 MB; exceeds GitHub's file size limit of 100 MB
```

**Solution:**
```bash
# Remove from history
git rm --cached large-file.zip
git commit --amend

# If already committed:
# Use BFG Repo-Cleaner or Git LFS

# Git LFS for large files:
git lfs install
git lfs track "*.zip"
git add .gitattributes
git commit -m "Add Git LFS"
```

---

## Undoing Mistakes

### Problem: Need to Undo Last Commit (Keep Changes)

**Solution:**
```bash
# Undo commit, keep changes in working directory
git reset HEAD~1

# Undo commit, keep changes staged
git reset --soft HEAD~1
```

### Problem: Need to Undo Last Commit (Discard Changes)

**Solution:**
```bash
# Undo commit and discard changes
git reset --hard HEAD~1

# If already pushed:
git revert HEAD
git push origin branch-name
```

### Problem: Need to Undo Multiple Commits

**Solution:**
```bash
# Undo last 3 commits, keep changes
git reset HEAD~3

# Undo last 3 commits, discard changes
git reset --hard HEAD~3

# If already pushed, revert each:
git revert HEAD~2..HEAD
```

### Problem: Messed Up Rebase

**Solution:**
```bash
# Abort rebase
git rebase --abort

# Or if completed but wrong:
git reflog
# Find commit before rebase (HEAD@{n})
git reset --hard HEAD@{n}
```

### Problem: Deleted File by Mistake

**Solution:**
```bash
# Restore from last commit
git checkout HEAD filename.txt

# Restore from specific commit
git checkout commit-hash filename.txt

# If already committed:
git revert commit-hash
```

---

## Performance Issues

### Problem: Git is Slow

**Solution:**
```bash
# Cleanup and optimize
git gc
git prune

# Reduce repository size
git gc --aggressive --prune=now

# Check repository size
du -sh .git
```

### Problem: Clone is Too Slow

**Solution:**
```bash
# Shallow clone (recent history only)
git clone --depth 1 https://github.com/user/repo.git

# Later, fetch full history if needed:
git fetch --unshallow
```

### Problem: Large Repository

**Solution:**
```bash
# Use sparse checkout (clone specific folders)
git clone --filter=blob:none --sparse https://github.com/user/repo.git
cd repo
git sparse-checkout set path/to/folder
```

---

## Configuration Problems

### Problem: Wrong User Name/Email in Commits

**Solution:**
```bash
# Set correct credentials
git config --global user.name "Correct Name"
git config --global user.email "correct@email.com"

# Amend last commit:
git commit --amend --reset-author --no-edit

# Change author of multiple commits:
git rebase -i HEAD~5
# Mark commits as 'edit'
# For each commit:
git commit --amend --reset-author --no-edit
git rebase --continue
```

### Problem: Line Ending Issues (Windows/Mac/Linux)

**Solution:**
```bash
# Configure line endings
# Windows:
git config --global core.autocrlf true

# Mac/Linux:
git config --global core.autocrlf input

# Fix existing repository:
git add --renormalize .
git commit -m "Normalize line endings"
```

### Problem: Git Ignoring .gitignore

**Solution:**
```bash
# Remove cached files
git rm -r --cached .
git add .
git commit -m "Fix .gitignore"
```

---

## Common Error Messages

### "detached HEAD state"

```bash
# You checked out a commit directly
# Solution: Create branch or return to a branch
git checkout main
# Or create branch here:
git checkout -b new-branch
```

### "Your branch and 'origin/main' have diverged"

```bash
# Solution 1: Merge
git pull origin main

# Solution 2: Rebase (if safe)
git pull --rebase origin main

# Solution 3: Reset (if you don't want local changes)
git reset --hard origin/main
```

### "fatal: refusing to merge unrelated histories"

```bash
# When merging independent repositories
git pull origin main --allow-unrelated-histories
```

### "Please commit your changes or stash them before you merge"

```bash
# Solution 1: Commit
git add .
git commit -m "Work in progress"

# Solution 2: Stash
git stash
# Do merge
git stash pop
```

---

## Recovery Commands

### Lost Commits

```bash
# View all operations
git reflog

# Find lost commit
# Recreate branch or cherry-pick
git checkout -b recovered commit-hash
# Or
git cherry-pick commit-hash
```

### Corrupted Repository

```bash
# Check for corruption
git fsck

# Try to recover
git fsck --full

# Clone fresh copy if possible
```

### Reset Gone Wrong

```bash
# Find state before reset
git reflog

# Go back
git reset --hard HEAD@{n}
```

---

## Prevention Tips

**Before Making Risky Changes:**
```bash
# Create backup branch
git branch backup-branch

# Or create backup of entire repository
cp -r my-repo my-repo-backup
```

**Regular Maintenance:**
```bash
# Keep branches synced
git fetch --all --prune

# Clean up regularly
git gc

# Check for issues
git fsck
```

**Safe Practices:**
```bash
# Never force push to shared branches
# Use --force-with-lease instead of --force
git push --force-with-lease

# Always pull before push
git pull origin main
git push origin main

# Test before pushing
# Run tests, check builds
```

---

## Getting Help

**In Terminal:**
```bash
# Git help
git help
git help <command>

# Explain error
git help <error-keyword>
```

**Online Resources:**
- [Stack Overflow - Git Questions](https://stackoverflow.com/questions/tagged/git)
- [Git Official Documentation](https://git-scm.com/doc)
- [GitHub Community Forum](https://github.community/)
- [Oh Shit, Git!?!](https://ohshitgit.com/) - Plain language Git recovery

**Ask for Help:**
```bash
# When asking for help, provide:
git --version
git status
git log --oneline -10
git remote -v

# Include error messages
```

---

## Emergency Toolkit

```bash
# Show everything
git reflog
git log --all --oneline --graph

# Undo everything to last working state
git reset --hard origin/main

# Save work before experimenting
git stash
git branch backup-$(date +%Y%m%d)

# If totally stuck, reclone
cd ..
mv repo repo-backup
git clone https://github.com/user/repo.git
# Copy over uncommitted work from backup
```

---

**Remember:**
- Don't panic!
- Git rarely loses data
- Most mistakes are recoverable
- When in doubt, create a backup
- Ask for help if needed

---

*For more detailed explanations, refer to the main course sections.*
