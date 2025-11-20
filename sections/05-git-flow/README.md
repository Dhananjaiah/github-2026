# Section 5: Git Flow Branching Strategy

## 📖 Overview

Git Flow is a robust branching model designed for projects with scheduled releases. Created by Vincent Driessen in 2010, it's one of the most popular and well-defined workflows.

**Duration:** 35 minutes

## 🎯 Learning Objectives

By the end of this section, you will:
- Understand the Git Flow branching model
- Know when to use Git Flow
- Work with main, develop, feature, release, and hotfix branches
- Implement Git Flow in your projects
- Use git-flow tools (optional)

---

## 5.1 What is Git Flow?

### Core Concept

Git Flow uses two permanent branches and three types of temporary branches to manage development and releases in a structured way.

### Branch Types

**Permanent Branches:**
1. **main** (or master) - Production-ready code
2. **develop** - Integration branch for features

**Temporary Branches:**
3. **feature/** - New features (branch from develop)
4. **release/** - Release preparation (branch from develop)
5. **hotfix/** - Urgent production fixes (branch from main)

### Visual Diagram

```
main:      A --- B --- C ----------- F --- G
                |       \           /       \
develop:        +--- D --- E --- H --- I --- J
                |    |
feature-1:      |    +--- f1 --- f2 (merged to develop)
                |
release:        |                R1 --- R2
                                  |      |
                             (to main & develop)
                
hotfix:                                 HF (merged to main & develop)
```

---

## 5.2 Branch Details

### Main Branch

- **Purpose:** Production code only
- **Always deployable:** Yes
- **Protected:** Should be locked - no direct commits
- **Tagged:** Every commit is a production release
- **Merges from:** Release and hotfix branches only

### Develop Branch

- **Purpose:** Integration branch for features
- **Contains:** Latest development changes
- **Protected:** Usually locked - only merges allowed
- **Merges from:** Feature, release, and hotfix branches
- **Branches from:** Feature and release branches

### Feature Branches

- **Naming:** `feature/feature-name` or `feature/ticket-123`
- **Branch from:** develop
- **Merge back to:** develop
- **Lifetime:** Until feature is complete
- **Purpose:** Develop new features

**Example names:**
```
feature/user-authentication
feature/shopping-cart
feature/payment-integration
```

### Release Branches

- **Naming:** `release/version-number` or `release/v1.0.0`
- **Branch from:** develop
- **Merge back to:** main AND develop
- **Lifetime:** Short - just for release preparation
- **Purpose:** Prepare for production release

**Example names:**
```
release/v1.0.0
release/v2.1.0
release/2024-11-20
```

### Hotfix Branches

- **Naming:** `hotfix/issue-description` or `hotfix/v1.0.1`
- **Branch from:** main
- **Merge back to:** main AND develop
- **Lifetime:** Very short - urgent fixes only
- **Purpose:** Fix critical production bugs

**Example names:**
```
hotfix/security-vulnerability
hotfix/critical-crash
hotfix/v1.0.1
```

---

## 5.3 Complete Git Flow Workflow

### Phase 1: Initial Setup

```bash
# Create main branch
git checkout -b main
echo "# My App v1.0" > README.md
git add README.md
git commit -m "Initial commit"
git push origin main

# Create develop branch
git checkout -b develop
git push origin develop
```

### Phase 2: Developing a Feature

```bash
# 1. Start from develop
git checkout develop
git pull origin develop

# 2. Create feature branch
git checkout -b feature/user-profile

# 3. Develop the feature
echo "User profile code" > profile.js
git add profile.js
git commit -m "Add user profile component"

echo "Profile tests" > profile.test.js
git add profile.test.js
git commit -m "Add profile tests"

# 4. Keep updated with develop
git checkout develop
git pull origin develop
git checkout feature/user-profile
git merge develop

# 5. Push feature branch
git push origin feature/user-profile

# 6. Create pull request to develop (on GitHub)

# 7. After approval, merge to develop
git checkout develop
git merge feature/user-profile --no-ff
git push origin develop

# 8. Delete feature branch
git branch -d feature/user-profile
git push origin --delete feature/user-profile
```

**Note:** `--no-ff` creates a merge commit even for fast-forward merges, preserving feature history.

### Phase 3: Creating a Release

```bash
# 1. Start release from develop
git checkout develop
git pull origin develop
git checkout -b release/v1.0.0

# 2. Update version numbers
echo "v1.0.0" > VERSION
git add VERSION
git commit -m "Bump version to 1.0.0"

# 3. Fix any release bugs
echo "Fix release bug" >> bugfix.txt
git add bugfix.txt
git commit -m "Fix bug found during release testing"

# 4. Merge to main
git checkout main
git pull origin main
git merge release/v1.0.0 --no-ff
git tag -a v1.0.0 -m "Release version 1.0.0"
git push origin main --tags

# 5. Merge back to develop
git checkout develop
git merge release/v1.0.0 --no-ff
git push origin develop

# 6. Delete release branch
git branch -d release/v1.0.0
git push origin --delete release/v1.0.0
```

### Phase 4: Creating a Hotfix

```bash
# 1. Start hotfix from main
git checkout main
git pull origin main
git checkout -b hotfix/security-fix

# 2. Fix the issue
echo "Security patch" > security.patch
git add security.patch
git commit -m "Fix security vulnerability"

# 3. Update version
echo "v1.0.1" > VERSION
git add VERSION
git commit -m "Bump version to 1.0.1"

# 4. Merge to main
git checkout main
git merge hotfix/security-fix --no-ff
git tag -a v1.0.1 -m "Hotfix version 1.0.1"
git push origin main --tags

# 5. Merge to develop
git checkout develop
git merge hotfix/security-fix --no-ff
git push origin develop

# 6. Delete hotfix branch
git branch -d hotfix/security-fix
git push origin --delete hotfix/security-fix
```

---

## 5.4 Real-World Example: Building a Web App

### Scenario: E-commerce Application

**Sprint 1: Initial Features**

```bash
# Setup
git checkout -b main
git commit --allow-empty -m "Initial commit"
git checkout -b develop
git push origin main develop

# Developer A: Product Listing
git checkout develop
git checkout -b feature/product-listing
# ... develop product listing ...
git commit -m "Add product listing page"
git checkout develop
git merge feature/product-listing --no-ff

# Developer B: Shopping Cart (parallel)
git checkout develop
git checkout -b feature/shopping-cart
# ... develop shopping cart ...
git commit -m "Add shopping cart functionality"
git checkout develop
git merge feature/shopping-cart --no-ff

# Developer C: User Registration (parallel)
git checkout develop
git checkout -b feature/user-registration
# ... develop registration ...
git commit -m "Add user registration"
git checkout develop
git merge feature/user-registration --no-ff
```

**Sprint 1 Release:**

```bash
# Create release branch
git checkout develop
git checkout -b release/v1.0.0

# QA finds and Developer fixes bugs
echo "Fix cart calculation" > bugfix1.txt
git add bugfix1.txt
git commit -m "Fix cart total calculation"

echo "Fix registration validation" > bugfix2.txt
git add bugfix2.txt
git commit -m "Fix email validation"

# Update version
echo "1.0.0" > package.json
git add package.json
git commit -m "Bump version to 1.0.0"

# Deploy to production
git checkout main
git merge release/v1.0.0 --no-ff
git tag -a v1.0.0 -m "Version 1.0.0 - Initial release"

# Merge bug fixes back to develop
git checkout develop
git merge release/v1.0.0 --no-ff

git push origin main develop --tags
```

**Production Hotfix:**

```bash
# Critical bug found in production!
git checkout main
git checkout -b hotfix/cart-crash

# Fix the critical bug
echo "Fix cart crash" > critical-fix.txt
git add critical-fix.txt
git commit -m "Fix critical cart crash bug"

# Update version
echo "1.0.1" > package.json
git add package.json
git commit -m "Bump version to 1.0.1"

# Deploy hotfix
git checkout main
git merge hotfix/cart-crash --no-ff
git tag -a v1.0.1 -m "Hotfix 1.0.1 - Fix cart crash"

# Bring fix to develop
git checkout develop
git merge hotfix/cart-crash --no-ff

git push origin main develop --tags
```

---

## 5.5 Git Flow Tools

### Using git-flow Extension (Optional)

The `git-flow` tool provides commands to simplify Git Flow.

**Installation:**

```bash
# Mac
brew install git-flow

# Linux
apt-get install git-flow

# Windows (Git Bash)
# Download from: https://github.com/petervanderdoes/gitflow-avh
```

**Initialize Git Flow:**

```bash
git flow init

# You'll be prompted to set branch names (use defaults):
# Production branch: main
# Development branch: develop
# Feature prefix: feature/
# Release prefix: release/
# Hotfix prefix: hotfix/
```

**Feature Workflow with git-flow:**

```bash
# Start a feature
git flow feature start user-profile

# Work on feature
# ... make changes ...
git add .
git commit -m "Add user profile"

# Finish feature (merges to develop and deletes branch)
git flow feature finish user-profile
```

**Release Workflow with git-flow:**

```bash
# Start a release
git flow release start v1.0.0

# Prepare release
# ... bug fixes, version bumps ...
git add .
git commit -m "Prepare version 1.0.0"

# Finish release (merges to main and develop, creates tag)
git flow release finish v1.0.0
```

**Hotfix Workflow with git-flow:**

```bash
# Start a hotfix
git flow hotfix start critical-bug

# Fix the bug
# ... make changes ...
git add .
git commit -m "Fix critical bug"

# Finish hotfix (merges to main and develop, creates tag)
git flow hotfix finish critical-bug
```

---

## 5.6 Advantages and Disadvantages

### Advantages ✅

**Clear Structure**
- Well-defined branch types and purposes
- Everyone knows where to branch from and merge to

**Parallel Development**
- Multiple features developed simultaneously
- Release preparation doesn't block feature development

**Release Management**
- Structured release process
- Easy to track what goes into each release

**Production Stability**
- Main branch always production-ready
- Hotfixes don't interfere with development

**Quality Assurance**
- Release branch allows thorough testing
- Bug fixes don't block new features

### Disadvantages ⚠️

**Complexity**
- Steep learning curve for beginners
- More branches to manage

**Overhead**
- Can be too heavyweight for simple projects
- Requires discipline and coordination

**Merge Conflicts**
- Long-lived develop branch can accumulate conflicts
- Hotfixes must be merged to multiple branches

**Not for Continuous Deployment**
- Designed for scheduled releases
- Extra steps for continuous deployment

**Release Branch Overhead**
- Short release window means less value
- Can slow down deployment

---

## 5.7 When to Use Git Flow

### ✅ Use Git Flow When:

- **Scheduled releases** - Monthly, quarterly, or versioned releases
- **Multiple versions** - Supporting multiple production versions
- **Large teams** - Need clear structure and organization
- **Formal process** - QA, staging, and approval processes
- **Enterprise software** - Traditional software development
- **Mobile apps** - iOS/Android with app store releases
- **Desktop software** - Traditional versioned software

### ❌ Don't Use Git Flow When:

- **Continuous deployment** - Deploying multiple times per day
- **Single version** - Only one production version
- **Small team** - 1-3 developers who communicate easily
- **Simple project** - Personal projects or prototypes
- **Web apps** - With continuous deployment model

### Alternatives for These Cases:

- **GitHub Flow** - Simpler, for continuous deployment
- **GitLab Flow** - Environment-based deployments
- **Trunk-Based Development** - High-frequency integration

---

## 5.8 Best Practices

### Branch Protection

```bash
# Protect main and develop branches on GitHub:
# Settings → Branches → Add rule
# - Require pull request reviews
# - Require status checks to pass
# - Include administrators
# - Require linear history
```

### Naming Conventions

✅ **Good:**
```
feature/user-authentication
feature/JIRA-123-shopping-cart
release/v1.2.0
release/2024-12-01
hotfix/security-patch
hotfix/v1.0.1
```

❌ **Bad:**
```
new-feature
johns-branch
test
fix
```

### Commit Messages

```bash
# Feature
git commit -m "feat: add user profile page"
git commit -m "feat(auth): implement JWT authentication"

# Bug fix
git commit -m "fix: resolve cart calculation error"

# Release
git commit -m "chore: bump version to 1.0.0"

# Hotfix
git commit -m "fix: critical security vulnerability"
```

### Version Tagging

```bash
# Always tag releases
git tag -a v1.0.0 -m "Release 1.0.0"
git tag -a v1.0.1 -m "Hotfix 1.0.1"

# Use semantic versioning
# MAJOR.MINOR.PATCH
# 1.0.0 - Initial release
# 1.1.0 - Minor changes, new features
# 1.0.1 - Patch, bug fix
# 2.0.0 - Major changes, breaking changes

# Push tags
git push origin --tags
```

### Pull Request Templates

Create `.github/pull_request_template.md`:

```markdown
## Type of Change
- [ ] Feature
- [ ] Bug fix
- [ ] Hotfix
- [ ] Release

## Description
<!-- Describe your changes -->

## Testing
<!-- How was this tested? -->

## Checklist
- [ ] Code follows style guidelines
- [ ] Tests added/updated
- [ ] Documentation updated
- [ ] No merge conflicts
```

---

## 🎤 Complete Section Transcript

Welcome to Section 5! Now we're going to learn about Git Flow, one of the most popular and well-defined branching strategies in the software industry.

Git Flow was created by Vincent Driessen in 2010, and it quickly became the standard for projects with scheduled releases. It's a robust branching model that uses multiple types of branches to manage development, releases, and hotfixes in a very structured way.

Let's understand the core concept. Git Flow uses two permanent branches: main and develop. The main branch contains production-ready code that's actually deployed to users. The develop branch is the integration branch where features come together. Then there are three types of temporary branches: feature branches for developing new features, release branches for preparing releases, and hotfix branches for urgent production fixes.

Let me explain each branch type in detail. The main branch is sacred - it only contains production code. Every commit on main is a release and should be tagged with a version number. You never commit directly to main. The develop branch contains the latest development changes. Features are merged here, and when you're ready for a release, you create a release branch from develop.

Feature branches are where individual features are developed. They branch from develop and merge back to develop. Each feature gets its own branch. Release branches are for preparing a release. You branch from develop, fix any bugs found during testing, update version numbers, and then merge to both main and develop. Hotfix branches are for critical production bugs. They branch from main, get fixed quickly, and merge back to both main and develop.

Let me walk through the complete workflow. First, you set up your repository with main and develop branches. Then, for each feature, you create a feature branch from develop, work on it, and merge it back to develop when done. When you're ready for a release, you create a release branch from develop. On the release branch, you fix any bugs found during QA, update version numbers, and prepare everything. Then you merge the release to main, tag it with a version number, and also merge it back to develop so those bug fixes aren't lost. If a critical bug is found in production, you create a hotfix branch from main, fix it quickly, merge to main and tag it, and also merge to develop.

Let me show you a real-world example. Imagine we're building an e-commerce application. Three developers work in parallel - Developer A builds the product listing, Developer B builds the shopping cart, and Developer C builds user registration. Each works on their own feature branch, and all merge to develop when complete. Then we create a release branch for version 1.0.0. QA tests it and finds some bugs. We fix those bugs on the release branch, update the version number, then merge to main and tag it as v1.0.0. We also merge back to develop so those bug fixes are included in future development. A week later, a critical bug is found in production - the shopping cart crashes. We immediately create a hotfix branch from main, fix the bug, bump the version to 1.0.1, merge to main and tag it, and merge to develop. The fix is deployed quickly without waiting for the next release.

Now, there's an optional tool called git-flow that makes this workflow easier. It provides commands like "git flow feature start", "git flow feature finish", "git flow release start", and so on. These commands automate the branching and merging steps. It's not required, but many teams find it helpful.

Let's talk about when to use Git Flow. It's perfect for projects with scheduled releases - monthly or quarterly releases, for example. It's great for large teams that need clear structure. It works well for mobile apps with app store releases, desktop software with versioned releases, and enterprise software with formal QA processes. However, it's probably too complex for small teams doing continuous deployment, simple personal projects, or web apps that deploy many times per day. For those cases, simpler workflows like GitHub Flow or Trunk-Based Development work better.

Some best practices: protect your main and develop branches so nobody can push to them directly. Use clear naming conventions for your branches. Always use semantic versioning for tags - major.minor.patch like 1.0.0, 1.1.0, 1.0.1. And create pull request templates to ensure consistent code reviews.

Git Flow has advantages and disadvantages. The advantages are clear structure, good parallel development, excellent release management, and production stability. The disadvantages are complexity, overhead for simple projects, potential merge conflicts, and it's not ideal for continuous deployment.

Git Flow is powerful but complex. It requires discipline and coordination from the team. But for projects with scheduled releases and formal processes, it provides a proven structure that scales well.

Great work! You now understand Git Flow. In the next section, we'll learn GitHub Flow, which is simpler and designed for continuous deployment.

See you in Section 6!

---

## ✅ Section Summary

**Branch Structure:**
- **main** - Production code
- **develop** - Development integration
- **feature/** - New features
- **release/** - Release preparation
- **hotfix/** - Production fixes

**Key Rules:**
- Never commit directly to main or develop
- Features branch from and merge to develop
- Releases branch from develop, merge to main and develop
- Hotfixes branch from main, merge to main and develop
- Always tag releases
- Use `--no-ff` for merge commits

**Best For:**
- Scheduled releases
- Large teams
- Formal QA processes
- Mobile/desktop applications
- Enterprise software

---

## 📝 Practice Exercise

Practice Git Flow with a complete workflow:

```bash
# Setup
git init gitflow-practice
cd gitflow-practice

# Create main and develop
git checkout -b main
echo "# App v1.0" > README.md
git add README.md
git commit -m "Initial commit"

git checkout -b develop
git push origin main develop

# Create a feature
git checkout -b feature/homepage
echo "<h1>Homepage</h1>" > index.html
git add index.html
git commit -m "Add homepage"

# Merge to develop
git checkout develop
git merge feature/homepage --no-ff
git branch -d feature/homepage

# Create release
git checkout -b release/v1.0.0
echo "1.0.0" > VERSION
git add VERSION
git commit -m "Bump version to 1.0.0"

# Merge to main
git checkout main
git merge release/v1.0.0 --no-ff
git tag -a v1.0.0 -m "Version 1.0.0"

# Merge back to develop
git checkout develop
git merge release/v1.0.0 --no-ff
git branch -d release/v1.0.0

# Create hotfix
git checkout main
git checkout -b hotfix/critical-fix
echo "Fix" > fix.txt
git add fix.txt
git commit -m "Fix critical bug"

# Merge hotfix
git checkout main
git merge hotfix/critical-fix --no-ff
git tag -a v1.0.1 -m "Hotfix 1.0.1"

git checkout develop
git merge hotfix/critical-fix --no-ff
git branch -d hotfix/critical-fix

# View result
git log --oneline --graph --all
```

---

## 🔗 Next Section

Ready for a simpler workflow? Continue to [Section 6: GitHub Flow](../06-github-flow/README.md) →
