# Git and GitHub Glossary

A comprehensive glossary of Git and GitHub terms used throughout the course.

---

## A

**Add**  
Stage files for commit using `git add`.

**Amend**  
Modify the last commit using `git commit --amend`.

---

## B

**Branch**  
A parallel version of the repository. Allows isolated development.

**Branch Protection**  
Rules that enforce quality standards before merging to protected branches.

---

## C

**Cherry-pick**  
Apply a specific commit from one branch to another using `git cherry-pick`.

**Checkout**  
Switch between branches or restore files using `git checkout`.

**Clone**  
Create a local copy of a remote repository using `git clone`.

**Commit**  
A snapshot of changes in the repository. Created with `git commit`.

**Commit Hash**  
Unique identifier (SHA) for each commit (e.g., `abc123def456`).

**Commit Message**  
Description of changes in a commit.

**Conflict**  
Occurs when Git can't automatically merge changes. Requires manual resolution.

**Conventional Commits**  
Standardized commit message format: `type(scope): description`.

**Continuous Integration (CI)**  
Automatically building and testing code on every commit.

**Continuous Deployment (CD)**  
Automatically deploying code to production after passing tests.

---

## D

**Develop Branch**  
Integration branch in Git Flow where features are merged.

**Diff**  
Shows differences between versions using `git diff`.

**Distributed Version Control**  
System where every developer has complete repository history (like Git).

---

## E

**End of Life (EOL)**  
Version no longer receiving updates or support.

**Environment Branch**  
Branch representing a deployment environment (staging, production, etc.).

---

## F

**Fast-Forward Merge**  
Merge that simply moves the branch pointer forward (no merge commit).

**Fetch**  
Download changes from remote without merging using `git fetch`.

**Feature Branch**  
Branch for developing a new feature.

**Feature Flag**  
Code that enables/disables features without deployment.

**Fork**  
Personal copy of someone else's repository on GitHub.

---

## G

**Git**  
Distributed version control system.

**GitHub**  
Cloud-based platform for hosting Git repositories.

**GitLab**  
Alternative to GitHub with built-in CI/CD.

**Git Flow**  
Branching strategy with main, develop, feature, release, and hotfix branches.

**GitHub Flow**  
Simple branching strategy: feature branches + main.

**GitLab Flow**  
Extension of GitHub Flow with environment or release branches.

**.gitignore**  
File specifying which files Git should ignore.

---

## H

**HEAD**  
Pointer to the current commit/branch.

**Hotfix**  
Urgent bug fix for production, often on a hotfix branch.

---

## I

**Index**  
Another name for the staging area.

**Interactive Rebase**  
Rewriting history with options to squash, reword, edit commits.

**Issue**  
GitHub's way to track bugs, features, and tasks.

---

## L

**Local Repository**  
Repository on your computer.

**Log**  
History of commits, viewed with `git log`.

**Long-lived Branch**  
Branch that exists for extended period (weeks/months).

---

## M

**Main Branch**  
Primary branch (formerly called master). Contains production code.

**Merge**  
Combining changes from one branch into another.

**Merge Commit**  
Commit created when merging branches (has two parents).

**Merge Conflict**  
When Git can't automatically combine changes.

**Merge Request (MR)**  
GitLab's name for pull requests.

**Monorepo**  
Single repository containing multiple projects/services.

---

## O

**Origin**  
Default name for the remote repository you cloned from.

---

## P

**Pull**  
Fetch and merge changes from remote using `git pull`.

**Pull Request (PR)**  
Proposal to merge changes, with code review on GitHub.

**Push**  
Upload local commits to remote using `git push`.

**Production Branch**  
Branch representing production environment code.

---

## R

**Rebase**  
Reapply commits on top of another base, rewriting history.

**Reflog**  
Log of all Git operations, useful for recovery.

**Release Branch**  
Branch for preparing a release or maintaining a version.

**Remote**  
Version of repository hosted elsewhere (like GitHub).

**Repository (Repo)**  
Project tracked by Git, contains all files and history.

**Reset**  
Undo commits using `git reset`.

**Revert**  
Create new commit that undoes previous commit(s).

---

## S

**SHA**  
Secure Hash Algorithm - unique identifier for commits.

**Short-lived Branch**  
Branch existing only briefly (hours to days).

**Squash**  
Combine multiple commits into one.

**Stash**  
Temporarily save uncommitted changes using `git stash`.

**Staging Area**  
Place where changes are prepared before committing.

**Semantic Versioning**  
Version numbering: MAJOR.MINOR.PATCH (e.g., 1.2.3).

---

## T

**Tag**  
Named reference to specific commit, often used for releases.

**Three-way Merge**  
Merge creating a merge commit (not fast-forward).

**Tracking Branch**  
Local branch connected to remote branch.

**Trunk**  
Main development branch in Trunk-Based Development.

**Trunk-Based Development**  
Strategy where all developers work on single branch with short-lived feature branches.

---

## U

**Unstage**  
Remove files from staging area using `git reset HEAD <file>`.

**Upstream**  
Original repository when working with forks.

---

## V

**Version Control**  
System for tracking changes to files over time.

---

## W

**Working Directory**  
Your local files, before staging or committing.

**WIP (Work In Progress)**  
Indicates incomplete work, often in commit messages or PR titles.

---

## Common Acronyms

- **CI** - Continuous Integration
- **CD** - Continuous Deployment/Delivery
- **PR** - Pull Request
- **MR** - Merge Request
- **VCS** - Version Control System
- **SCM** - Source Code Management
- **EOL** - End of Life
- **LTS** - Long Term Support
- **WIP** - Work In Progress
- **TBD** - Trunk-Based Development

---

## Git Commands Quick Reference

```bash
# Basic commands
git init          # Initialize repository
git clone         # Clone repository
git add           # Stage changes
git commit        # Save snapshot
git push          # Upload to remote
git pull          # Download from remote
git status        # Check status
git log           # View history

# Branching
git branch        # List/create branches
git checkout      # Switch branches
git merge         # Merge branches
git rebase        # Rebase branches

# Remote
git remote        # Manage remotes
git fetch         # Download changes
git push origin   # Push to remote

# Undo
git reset         # Undo commits
git revert        # Create undo commit
git checkout --   # Discard changes
git stash         # Save work temporarily
```

---

## Related Terms

**DevOps**  
Practices combining development and operations for faster delivery.

**CI/CD Pipeline**  
Automated workflow for building, testing, and deploying code.

**Code Review**  
Process of examining code before merging.

**Technical Debt**  
Cost of rework caused by choosing quick solutions over better approaches.

**Atomic Commit**  
Commit containing one logical change.

---

*This glossary covers terms from all sections of the course. Refer back to specific sections for detailed explanations and examples.*
