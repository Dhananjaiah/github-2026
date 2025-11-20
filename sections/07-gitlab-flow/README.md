# Section 7: GitLab Flow

## 📖 Overview

GitLab Flow combines the best aspects of GitHub Flow and Git Flow, adding environment-specific branches for better deployment management. It's designed for teams that need both continuous delivery and environment control.

**Duration:** 30 minutes

## 🎯 Learning Objectives

By the end of this section, you will:
- Understand GitLab Flow and its variations
- Work with environment branches
- Implement release branches in GitLab Flow
- Choose between production branch and release branch strategies
- Deploy to multiple environments effectively

---

## 7.1 What is GitLab Flow?

### Core Concept

GitLab Flow extends GitHub Flow by adding environment branches (production, staging, etc.) or release branches for better deployment control. It gives you continuous delivery with more control than GitHub Flow, but less complexity than Git Flow.

### Three Variations

1. **Production Branch Strategy** - For continuous deployment with staging
2. **Environment Branches Strategy** - For multiple environments
3. **Release Branches Strategy** - For versioned releases

### Key Principles

- **Main is always green** - All tests pass on main
- **Upstream first** - Merge to main first, then to environment branches
- **Feature branches** - All development in branches
- **Merge requests** - Code review required
- **Environment branches** - Represent deployment environments

---

## 7.2 Production Branch Strategy

### Use Case
Perfect for web applications that need staging before production.

### Branch Structure

```
main:        A --- B --- C --- D --- E
                  |       |       |
production:       +-------+-------+
                 (deploy) (deploy)
```

**Branches:**
- **main** - Development work, always tested
- **production** - Production environment

### Workflow

**Step 1: Feature Development**
```bash
# Create feature branch from main
git checkout main
git pull origin main
git checkout -b feature/new-dashboard

# Develop feature
echo "Dashboard code" > dashboard.js
git add dashboard.js
git commit -m "Add new dashboard"
git push origin feature/new-dashboard

# Create merge request to main
```

**Step 2: Merge to Main (After Review)**
```bash
# Merges happen on GitLab/GitHub
# After merge, update local main
git checkout main
git pull origin main
git branch -d feature/new-dashboard
```

**Step 3: Deploy to Production**
```bash
# When ready to deploy, merge main to production
git checkout production
git pull origin production
git merge main
git push origin production

# This triggers deployment to production
```

### Real-World Example: Blog Platform

```bash
# Setup
git checkout -b main
git commit --allow-empty -m "Initial commit"
git checkout -b production
git push origin main production

# Week 1: Add comment feature
git checkout main
git checkout -b feature/comments
echo "Comment system" > comments.js
git add comments.js
git commit -m "Add comment system"
git push origin feature/comments
# Create MR, get reviewed, merge to main

# Week 1: Deploy to production
git checkout production
git merge main  # Deploys comments to production

# Week 2: Add search feature
git checkout main
git checkout -b feature/search
echo "Search functionality" > search.js
git add search.js
git commit -m "Add search feature"
git push origin feature/search
# Create MR, merge to main

# Week 2: Still testing search on staging (main)
# Production still has only comments

# Week 3: Search is ready
git checkout production
git merge main  # Deploys search to production
```

---

## 7.3 Environment Branches Strategy

### Use Case
For applications with multiple environments: development, staging, pre-production, production.

### Branch Structure

```
main:           A --- B --- C --- D --- E
                |     |     |     |     |
staging:        +-----+     |     |     |
                            |     |     |
pre-prod:                   +-----+     |
                                        |
production:                             +
```

**Branches:**
- **main** - Development integration
- **staging** - Staging environment
- **pre-production** - Pre-production testing
- **production** - Production environment

### Workflow

```bash
# Feature development (same as before)
git checkout main
git checkout -b feature/user-settings
# ... develop ...
git push origin feature/user-settings
# Merge to main after review

# Deploy to staging
git checkout staging
git merge main
git push origin staging
# Triggers deployment to staging environment

# After staging tests pass, deploy to pre-prod
git checkout pre-production
git merge staging
git push origin pre-production
# Triggers deployment to pre-prod environment

# After pre-prod validation, deploy to production
git checkout production
git merge pre-production
git push origin production
# Triggers deployment to production environment
```

### Real-World Example: E-commerce Platform

```bash
# Setup environments
git checkout -b main
git commit --allow-empty -m "Initial commit"
git checkout -b staging
git checkout -b pre-production
git checkout -b production
git push origin --all

# Monday: Add payment feature
git checkout main
git checkout -b feature/payment-gateway
echo "Payment integration" > payment.js
git add payment.js
git commit -m "Integrate payment gateway"
git push origin feature/payment-gateway
# MR to main, reviewed, merged

# Tuesday: Deploy to staging for QA
git checkout staging
git merge main
git push origin staging
# QA team tests in staging environment

# Wednesday: QA approves, deploy to pre-prod
git checkout pre-production
git merge staging
git push origin pre-production
# Business team validates in pre-prod

# Thursday: Final approval, deploy to production
git checkout production
git merge pre-production
git push origin production
# Payment feature now live!
```

---

## 7.4 Release Branches Strategy

### Use Case
For software with version numbers and multiple supported versions (like mobile apps, desktop software, or APIs with versioning).

### Branch Structure

```
main:      A --- B --- C --- D --- E --- F
                |           |
release-1.0:    +--- R1 --- R2
                            |
                         (hotfix)

release-2.0:                +--- R3 --- R4
```

**Branches:**
- **main** - Development
- **release/1.0** - Version 1.0 branch
- **release/2.0** - Version 2.0 branch

### Workflow

**Creating a Release:**
```bash
# When ready for new version
git checkout main
git checkout -b release/1.0

# Update version
echo "1.0.0" > VERSION
git add VERSION
git commit -m "Bump version to 1.0.0"
git tag v1.0.0
git push origin release/1.0 --tags

# Continue development on main
git checkout main
# Main now works on features for 2.0
```

**Maintaining Multiple Versions:**
```bash
# Bug fix for 1.0
git checkout release/1.0
git checkout -b bugfix/critical-issue
echo "Fix" > fix.txt
git add fix.txt
git commit -m "Fix critical issue"
git push origin bugfix/critical-issue
# MR to release/1.0

# After merge
git checkout release/1.0
git tag v1.0.1
git push origin release/1.0 --tags

# Also bring fix to main
git checkout main
git cherry-pick <commit-hash>
# Or merge the bugfix branch to main
```

### Real-World Example: Mobile App

```bash
# Develop version 1.0
git checkout -b main
# ... many feature branches merged to main ...

# Ready for 1.0 release
git checkout -b release/1.0
echo "1.0.0" > version.txt
git add version.txt
git commit -m "Release 1.0.0"
git tag v1.0.0
git push origin release/1.0 --tags
# App submitted to App Store

# Continue working on 2.0 features on main
git checkout main
git checkout -b feature/dark-mode
# ... develop dark mode ...

# Critical bug found in 1.0!
git checkout release/1.0
git checkout -b hotfix/app-crash
echo "Crash fix" > crashfix.txt
git add crashfix.txt
git commit -m "Fix app crash on startup"

# Merge to release/1.0
git checkout release/1.0
git merge hotfix/app-crash
git tag v1.0.1
git push origin release/1.0 --tags
# Emergency update submitted to App Store

# Bring fix to main so 2.0 doesn't have the bug
git checkout main
git merge hotfix/app-crash
# Or cherry-pick specific commits
```

---

## 7.5 Choosing the Right Strategy

### Production Branch Strategy

**✅ Use when:**
- Web application
- One production version
- Need staging before production
- Deploy frequently
- Simple deployment needs

**Example teams:**
- Small SaaS companies
- Internal web tools
- Content management systems

### Environment Branches Strategy

**✅ Use when:**
- Multiple environments required
- Complex approval processes
- Need to control promotion between environments
- Regulated industries
- Large enterprises

**Example teams:**
- Banking applications
- Healthcare systems
- Enterprise software
- Government systems

### Release Branches Strategy

**✅ Use when:**
- Multiple versions in production
- Desktop/mobile applications
- API versioning
- Support old versions
- Can't force updates

**Example teams:**
- Mobile app developers
- Desktop software companies
- API providers
- Libraries/frameworks

---

## 7.6 Comparison with Other Workflows

| Feature | GitHub Flow | GitLab Flow | Git Flow |
|---------|-------------|-------------|----------|
| **Simplicity** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐ |
| **Environment Control** | ⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ |
| **Multiple Versions** | ⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| **Deployment Speed** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐ |
| **Learning Curve** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐ |

---

## 7.7 Best Practices

### Upstream First Principle

Always merge to main before environment branches:

```bash
# ✅ Correct flow
git checkout main
git merge feature/new-feature
git push origin main

# Then promote to environments
git checkout staging
git merge main
git push origin staging

# ❌ Wrong - don't skip main
git checkout staging
git merge feature/new-feature  # DON'T DO THIS
```

**Why?** Ensures all code goes through main, making it the source of truth.

### Environment Branch Protection

```bash
# On GitLab/GitHub, protect environment branches:
# - main: Require MR, require tests
# - staging: Only merge from main
# - production: Only merge from staging/pre-prod
# - Require approvers for production
```

### Automated Deployments

```yaml
# .gitlab-ci.yml example
stages:
  - test
  - deploy-staging
  - deploy-production

test:
  stage: test
  script:
    - npm test
  only:
    - branches

deploy-staging:
  stage: deploy-staging
  script:
    - deploy-to-staging.sh
  only:
    - staging

deploy-production:
  stage: deploy-production
  script:
    - deploy-to-production.sh
  only:
    - production
  when: manual  # Require manual trigger
```

### Version Tagging

```bash
# Tag all production deployments
git checkout production
git merge pre-production
git tag -a v1.2.3 -m "Release 1.2.3: Add user dashboard"
git push origin production --tags

# Semantic versioning
# MAJOR.MINOR.PATCH
# 1.0.0 - Initial release
# 1.1.0 - New features
# 1.1.1 - Bug fixes
# 2.0.0 - Breaking changes
```

### Cherry-picking vs Merging

**Cherry-pick for specific commits:**
```bash
# Get specific commit from one branch to another
git checkout release/1.0
git cherry-pick abc123  # Commit from main
```

**Merge for all changes:**
```bash
# Get all changes
git checkout production
git merge staging
```

---

## 7.8 Handling Common Scenarios

### Scenario 1: Rolling Back Production

```bash
# Quick rollback by merging previous tag
git checkout production
git reset --hard v1.2.2  # Previous working version
git push origin production --force

# Or revert specific commits
git revert <bad-commit-hash>
git push origin production
```

### Scenario 2: Emergency Hotfix

```bash
# Create hotfix from production
git checkout production
git checkout -b hotfix/critical-security

# Fix the issue
echo "Security patch" > security.patch
git add security.patch
git commit -m "Fix security vulnerability"

# Merge to production
git checkout production
git merge hotfix/critical-security
git push origin production

# Also merge to all upstream branches
git checkout pre-production
git merge hotfix/critical-security

git checkout staging
git merge hotfix/critical-security

git checkout main
git merge hotfix/critical-security
```

### Scenario 3: Feature Toggle

```bash
# Deploy code with feature disabled
git checkout main
git checkout -b feature/new-ui
echo "if (featureFlags.newUI) { /* new UI */ }" > ui.js
git add ui.js
git commit -m "Add new UI behind feature flag"

# Merge to main and deploy to production
git checkout main
git merge feature/new-ui

git checkout production
git merge main
# Feature is in production but disabled

# Enable for testing in staging
# Update feature flag configuration

# Enable in production when ready
# Update production feature flag configuration
```

---

## 7.9 Advantages and Disadvantages

### Advantages ✅

**Flexibility**
- Choose strategy that fits your needs
- Easier than Git Flow
- More control than GitHub Flow

**Environment Control**
- Clear representation of each environment
- Easy to see what's deployed where
- Controlled promotion between environments

**Multiple Versions**
- Support old versions
- Independent bug fixes per version
- Clear version history

**Gradual Rollout**
- Test in staging
- Validate in pre-prod
- Deploy to production with confidence

### Disadvantages ⚠️

**More Branches**
- More complexity than GitHub Flow
- Need to maintain multiple branches
- Potential for drift between environments

**Discipline Required**
- Must follow upstream-first principle
- Must keep environment branches synced
- Must remember to merge to all necessary branches

**Deployment Overhead**
- Multiple deployment steps
- Slower than direct deployment
- More manual intervention

---

## 🎤 Complete Section Transcript

Welcome to Section 7! In this section, we'll learn about GitLab Flow, a branching strategy that combines the best aspects of both GitHub Flow and Git Flow.

GitLab Flow was created by GitLab to solve a common problem: GitHub Flow is too simple for teams that need environment control, but Git Flow is too complex for modern continuous delivery. GitLab Flow gives you the simplicity of GitHub Flow with better control over deployments.

The core principle of GitLab Flow is that main is always green, meaning all tests always pass. You use feature branches for development, just like GitHub Flow. But unlike GitHub Flow, which deploys directly from main, GitLab Flow adds environment branches or release branches for better control.

GitLab Flow has three variations, and you choose the one that fits your needs. The first is the production branch strategy, which is perfect for web applications that need a staging environment before production. The second is the environment branches strategy, for teams that need multiple environments like development, staging, pre-production, and production. The third is the release branches strategy, for software that has version numbers and needs to support multiple versions simultaneously.

Let's look at the production branch strategy first. You have two branches: main and production. Main is your development branch where features are integrated and tested. Production represents what's actually deployed to your users. The workflow is simple: develop features in feature branches, merge them to main after code review, test them on main which acts as your staging environment, and when ready, merge main to production to deploy.

For example, imagine you're building a blog platform. You develop a comment feature on a feature branch, merge it to main, test it on the staging environment that deploys from main, and when you're confident it works, you merge main to production to deploy it to your users. Meanwhile, you can continue developing a search feature on main without deploying it yet. When search is ready, you merge main to production again, and both features are now live.

The environment branches strategy extends this concept to multiple environments. You might have main for development integration, staging for QA testing, pre-production for business validation, and production for your users. Code flows downstream: main to staging to pre-production to production. This gives you multiple checkpoints before reaching users.

For an e-commerce platform, you might develop a payment feature, merge it to main, then promote it to staging for QA testing. After QA approves, you promote it to pre-production where the business team validates it. After final approval, you promote it to production. This ensures thorough testing at each stage.

The release branches strategy is different. It's for software with version numbers, like mobile apps or desktop software. Instead of environment branches, you create release branches for each version. Main is where you develop the next version. When ready for release, you create a release branch like "release/1.0". Future development on main works toward version 2.0, while bug fixes for 1.0 happen on the release/1.0 branch.

For a mobile app, you'd develop all the 1.0 features on main, create a release/1.0 branch when ready, and submit it to the app store. Then you start working on 2.0 features on main. If a critical bug is found in 1.0, you fix it on the release/1.0 branch, tag it as 1.0.1, and submit the update. You also bring that fix to main so 2.0 doesn't have the bug.

An important principle in GitLab Flow is "upstream first." Always merge to main before environment branches. This ensures main is the source of truth and contains all changes. Don't merge features directly to staging or production - go through main first.

GitLab Flow gives you flexibility to choose the strategy that fits your needs. Use production branch strategy for simple web apps. Use environment branches strategy for complex approval processes and multiple environments. Use release branches strategy for versioned software that needs to support multiple versions.

Compared to other workflows, GitLab Flow is simpler than Git Flow but gives more control than GitHub Flow. It's especially good for teams that need environment control or version management but want to avoid the complexity of Git Flow.

Best practices include protecting your environment branches so only specific people can deploy to production, automating deployments with CI/CD, always following the upstream-first principle, and tagging all production deployments with version numbers.

GitLab Flow offers a middle ground between simplicity and control. It adapts to your needs while keeping complexity manageable. Whether you need staging, multiple environments, or version management, GitLab Flow has a strategy for you.

Excellent work! You now understand GitLab Flow and its three variations. In the next section, we'll learn about Trunk-Based Development, a strategy focused on very short-lived branches and continuous integration.

See you in Section 8!

---

## ✅ Section Summary

**Three Strategies:**
1. **Production Branch** - Main + production
2. **Environment Branches** - Main + staging + pre-prod + production
3. **Release Branches** - Main + release/version branches

**Key Principles:**
- Main is always green
- Upstream first (merge to main first)
- Feature branches for development
- Environment/release branches for deployment

**Choose Based On:**
- Production branch: Simple web apps
- Environment branches: Multiple environments/complex approval
- Release branches: Version management/mobile apps

---

## 📝 Practice Exercise

Practice GitLab Flow with production branch strategy:

```bash
# Setup
mkdir gitlab-flow-practice
cd gitlab-flow-practice
git init

# Create main and production branches
git checkout -b main
echo "# App" > README.md
git add README.md
git commit -m "Initial commit"

git checkout -b production
git checkout main

# Feature 1
git checkout -b feature/dashboard
echo "Dashboard" > dashboard.html
git add dashboard.html
git commit -m "Add dashboard"

git checkout main
git merge feature/dashboard
git branch -d feature/dashboard

# Deploy to production
git checkout production
git merge main
git tag v1.0.0
git checkout main

# Feature 2
git checkout -b feature/reports
echo "Reports" > reports.html
git add reports.html
git commit -m "Add reports"

git checkout main
git merge feature/reports

# Deploy to production
git checkout production
git merge main
git tag v1.1.0

# View history
git log --oneline --graph --all
```

---

## 🔗 Next Section

Continue to [Section 8: Trunk-Based Development](../08-trunk-based-development/README.md) →
