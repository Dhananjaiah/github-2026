# Section 9: Release Branching Strategy

## 📖 Overview

Release Branching is a strategy for managing multiple versions of software in production. It's essential for software that requires version support, long release cycles, or the ability to patch old versions.

**Duration:** 25 minutes

## 🎯 Learning Objectives

By the end of this section, you will:
- Understand release branching concepts
- Manage multiple production versions
- Create and maintain release branches
- Handle hotfixes for specific versions
- Know when to use release branching

---

## 9.1 What is Release Branching?

### Core Concept

Create a separate branch for each released version that needs ongoing support. New development happens on main while release branches receive only bug fixes and critical updates.

### Key Characteristics

1. **Version branches** - One branch per major/minor version
2. **Long-lived** - Branches exist as long as version is supported
3. **Bug fixes only** - No new features on release branches
4. **Forward porting** - Fixes applied to release branches also go to main
5. **Multiple versions** - Support several versions simultaneously

### Visual Diagram

```
main:          A - B - C - D - E - F - G - H - I
                   |           |           |
release/1.0:       +--- b1 --- b2 (v1.0.1, v1.0.2)
                       |
release/2.0:           +--- d1 --- d2 (v2.0.1)
                               |
release/3.0:                   +--- f1 (v3.0.1)
```

Each release branch maintains its own version while main continues forward.

---

## 9.2 Complete Workflow

### Phase 1: Initial Development

```bash
# Develop on main
git checkout main

# Add features for version 1.0
git checkout -b feature/user-management
# ... develop feature ...
git commit -m "Add user management"
git checkout main
git merge feature/user-management

# More features
git checkout -b feature/reporting
# ... develop feature ...
git commit -m "Add reporting"
git checkout main
git merge feature/reporting
```

### Phase 2: Creating a Release

```bash
# When ready for version 1.0 release
git checkout main
git checkout -b release/1.0

# Update version numbers
echo "1.0.0" > VERSION
git add VERSION
git commit -m "Release 1.0.0"

# Tag the release
git tag -a v1.0.0 -m "Release version 1.0.0"
git push origin release/1.0 --tags

# Continue development on main for 2.0
git checkout main
echo "2.0.0-dev" > VERSION
git commit -am "Start development for 2.0"
```

### Phase 3: Bug Fixes on Release Branch

```bash
# Bug found in version 1.0
git checkout release/1.0

# Create fix branch
git checkout -b fix/critical-bug-1.0

# Fix the bug
echo "Bug fix" > fix.txt
git add fix.txt
git commit -m "Fix critical bug in 1.0"

# Merge to release branch
git checkout release/1.0
git merge fix/critical-bug-1.0

# Update version (patch number)
echo "1.0.1" > VERSION
git add VERSION
git commit -m "Bump version to 1.0.1"

# Tag the patch release
git tag -a v1.0.1 -m "Patch release 1.0.1"
git push origin release/1.0 --tags

# Forward port fix to main
git checkout main
git cherry-pick <commit-hash>
# Or merge the fix branch
git merge fix/critical-bug-1.0

git push origin main
```

### Phase 4: Supporting Multiple Versions

```bash
# Version 2.0 released
git checkout main
git checkout -b release/2.0
echo "2.0.0" > VERSION
git add VERSION
git commit -m "Release 2.0.0"
git tag -a v2.0.0 -m "Release version 2.0.0"
git push origin release/2.0 --tags

# Now supporting three versions:
# - release/1.0 (maintenance only)
# - release/2.0 (active support)
# - main (developing 3.0)

# Security fix needed in all versions
git checkout release/1.0
git checkout -b security-fix-all-versions
echo "Security patch" > security.patch
git add security.patch
git commit -m "Fix security vulnerability"

# Apply to 1.0
git checkout release/1.0
git merge security-fix-all-versions
git tag -a v1.0.2 -m "Security fix 1.0.2"
git push origin release/1.0 --tags

# Apply to 2.0
git checkout release/2.0
git cherry-pick <commit-hash>
git tag -a v2.0.1 -m "Security fix 2.0.1"
git push origin release/2.0 --tags

# Apply to main
git checkout main
git cherry-pick <commit-hash>
git push origin main
```

---

## 9.3 Real-World Example: Desktop Application

### Scenario: Video Editing Software

**January 2024: Version 1.0 Development**

```bash
# Develop features on main
git checkout main

# Feature: Basic editing
git checkout -b feature/basic-editing
# ... 2 months of development ...
git checkout main
git merge feature/basic-editing

# Feature: Effects
git checkout -b feature/effects
# ... 1 month of development ...
git checkout main
git merge feature/effects

# Ready to release 1.0
git checkout -b release/1.0
echo "1.0.0" > version.txt
git add version.txt
git commit -m "Release 1.0.0"
git tag -a v1.0.0 -m "Version 1.0.0"
git push origin release/1.0 --tags
# Ship to customers!
```

**April 2024: Bug Fix in 1.0**

```bash
# Customer reports crash bug
git checkout release/1.0
git checkout -b fix/crash-on-export

echo "Fix export crash" > fix-crash.txt
git add fix-crash.txt
git commit -m "Fix crash during video export"

git checkout release/1.0
git merge fix/crash-on-export
echo "1.0.1" > version.txt
git add version.txt
git commit -m "Version 1.0.1 - Fix export crash"
git tag -a v1.0.1 -m "Patch 1.0.1"
git push origin release/1.0 --tags
# Emergency update to customers

# Port fix to main (for 2.0)
git checkout main
git cherry-pick <commit-hash>
```

**June 2024: Version 2.0 Release**

```bash
# New features developed on main
git checkout main

# Feature: 4K support
git checkout -b feature/4k-support
# ... develop ...
git checkout main
git merge feature/4k-support

# Feature: Cloud sync
git checkout -b feature/cloud-sync
# ... develop ...
git checkout main
git merge feature/cloud-sync

# Release 2.0
git checkout -b release/2.0
echo "2.0.0" > version.txt
git add version.txt
git commit -m "Release 2.0.0"
git tag -a v2.0.0 -m "Version 2.0.0"
git push origin release/2.0 --tags
# Ship version 2.0!
```

**August 2024: Supporting Both Versions**

```bash
# Bug in 1.0 only (doesn't exist in 2.0)
git checkout release/1.0
git checkout -b fix/ui-glitch-1.0
echo "Fix UI" > ui-fix.txt
git add ui-fix.txt
git commit -m "Fix UI glitch in effects panel"
git checkout release/1.0
git merge fix/ui-glitch-1.0
echo "1.0.2" > version.txt
git add version.txt
git commit -m "Version 1.0.2"
git tag -a v1.0.2 -m "Patch 1.0.2"
git push origin release/1.0 --tags

# Bug in 2.0 that also exists in 1.0
git checkout release/2.0
git checkout -b fix/memory-leak-all
echo "Fix memory leak" > mem-fix.txt
git add mem-fix.txt
git commit -m "Fix memory leak"

# Apply to 2.0
git checkout release/2.0
git merge fix/memory-leak-all
echo "2.0.1" > version.txt
git add version.txt
git commit -m "Version 2.0.1"
git tag -a v2.0.1 -m "Patch 2.0.1"

# Apply to 1.0
git checkout release/1.0
git cherry-pick <commit-hash>
echo "1.0.3" > version.txt
git add version.txt
git commit -m "Version 1.0.3"
git tag -a v1.0.3 -m "Patch 1.0.3"

# Apply to main (for 3.0)
git checkout main
git cherry-pick <commit-hash>

git push origin --all --tags
```

**December 2024: End of Life for 1.0**

```bash
# Announce 1.0 EOL (end of life)
# No more updates for release/1.0
# Focus support on 2.0 and development of 3.0
```

---

## 9.4 Semantic Versioning

### Version Format: MAJOR.MINOR.PATCH

```
1.0.0
│ │ │
│ │ └── PATCH: Bug fixes, no new features
│ └──── MINOR: New features, backward compatible
└────── MAJOR: Breaking changes, not backward compatible
```

### Examples

```bash
1.0.0 → 1.0.1  # Bug fix (patch)
1.0.1 → 1.1.0  # New feature (minor)
1.1.0 → 2.0.0  # Breaking change (major)
```

### Applying Semantic Versioning

```bash
# Patch release (bug fix only)
echo "1.0.0" → "1.0.1"
git tag -a v1.0.1 -m "Fix critical bug"

# Minor release (new features, no breaking changes)
echo "1.0.1" → "1.1.0"
git tag -a v1.1.0 -m "Add new export formats"

# Major release (breaking changes)
echo "1.1.0" → "2.0.0"
git tag -a v2.0.0 -m "New plugin API (breaking changes)"
```

---

## 9.5 Best Practices

### Branch Naming Conventions

```bash
# Good naming
release/1.0
release/2.0
release/3.0.x  # For patch-level branches

# With dates (for projects without version numbers)
release/2024-Q1
release/2024-Q2

# For mobile apps
release/ios-1.0
release/android-1.0
```

### Support Lifecycle

```
Version    Status        Support Type
-------    ------        ------------
3.0        Current       Full support (features + bugs)
2.0        Previous      Bug fixes only
1.0        Legacy        Critical security fixes only
0.9        End of Life   No support
```

### Documentation

Create a `RELEASES.md` file:

```markdown
# Release History

## Version 2.0.0 (2024-06-01)
- Added 4K video support
- Added cloud synchronization
- Improved rendering performance
- Breaking: Changed plugin API

## Version 1.0.3 (2024-08-15)
- Fixed memory leak in effects processing
- Fixed UI glitch in effects panel

## Version 1.0.2 (2024-08-01)
- Fixed UI glitch in effects panel

## Version 1.0.1 (2024-04-15)
- Fixed crash during video export

## Version 1.0.0 (2024-01-15)
- Initial release
```

### Changelog

Use conventional commits for automatic changelog generation:

```bash
# Commit format
git commit -m "fix: resolve memory leak in video processing"
git commit -m "feat: add 4K video export support"
git commit -m "feat!: redesign plugin API (BREAKING CHANGE)"

# Generate changelog
npx standard-version
# Creates CHANGELOG.md automatically
```

---

## 9.6 Tools and Automation

### Automated Version Bumping

```javascript
// package.json scripts
{
  "scripts": {
    "release:patch": "npm version patch && git push --follow-tags",
    "release:minor": "npm version minor && git push --follow-tags",
    "release:major": "npm version major && git push --follow-tags"
  }
}

// Usage
npm run release:patch  # 1.0.0 → 1.0.1
npm run release:minor  # 1.0.1 → 1.1.0
npm run release:major  # 1.1.0 → 2.0.0
```

### Release Management Tools

```bash
# semantic-release (automated versioning)
npm install --save-dev semantic-release

# standard-version (changelog generation)
npm install --save-dev standard-version

# git-flow (if using Git Flow with release branches)
brew install git-flow-avh
```

---

## 9.7 Advantages and Disadvantages

### Advantages ✅

**Multiple Version Support**
- Support old versions while developing new ones
- Critical for enterprise customers

**Stability**
- Release branches are stable
- Only bug fixes, no new features

**Flexibility**
- Different customers on different versions
- Can backport critical fixes

**Clear History**
- Easy to see what's in each version
- Tagged releases

### Disadvantages ⚠️

**Maintenance Overhead**
- Must maintain multiple branches
- Time spent on old versions

**Complexity**
- More branches to manage
- Must remember to forward-port fixes

**Merge Conflicts**
- Branches diverge over time
- Harder to apply fixes to old versions

**Resource Intensive**
- Testing multiple versions
- Supporting multiple versions

---

## 9.8 When to Use Release Branching

### ✅ Use Release Branching When:

- **Desktop/mobile software** - Users on different versions
- **Enterprise software** - Customers can't upgrade immediately
- **API versioning** - Multiple API versions in production
- **Library/framework** - Users depend on specific versions
- **Long support cycles** - Years of support required
- **Regulatory compliance** - Validated versions must be maintained

**Examples:**
- Windows (multiple versions supported)
- Node.js (LTS versions)
- Ubuntu Linux (LTS releases)
- iOS/Android apps
- WordPress plugins

### ❌ Don't Use Release Branching When:

- **Web applications** - All users on same version
- **SaaS products** - Can update everyone at once
- **Internal tools** - Control over deployment
- **Small team** - Overhead too high
- **Rapid iteration** - Want speed over stability

---

## 9.9 Combined Strategies

### Release Branching + Feature Branches

```bash
# Develop features on main
git checkout main
git checkout -b feature/new-feature
# ... develop ...
git checkout main
git merge feature/new-feature

# Create release
git checkout -b release/1.0
git tag v1.0.0

# Fix bugs with feature branches
git checkout release/1.0
git checkout -b fix/bug-in-release
# ... fix ...
git checkout release/1.0
git merge fix/bug-in-release
git tag v1.0.1
```

### Release Branching + Git Flow

```bash
# Use Git Flow develop branch
git checkout develop
# ... feature development ...

# Create release from develop
git checkout -b release/1.0
# ... prepare release ...

# Merge to main
git checkout main
git merge release/1.0
git tag v1.0.0

# Keep release/1.0 for maintenance
# Don't delete it like in pure Git Flow
```

---

## 🎤 Complete Section Transcript

Welcome to Section 9! In this section, we'll learn about Release Branching, a strategy for managing multiple versions of software in production.

Release Branching is essential when you need to support multiple versions of your software simultaneously. Think about desktop applications, mobile apps, or enterprise software where customers might be on version 1.0 while you're developing version 2.0 and even version 3.0. Each version needs its own branch so you can fix bugs in old versions without affecting new development.

The core concept is simple: create a separate branch for each released version that needs ongoing support. New development happens on main, but release branches receive only bug fixes and critical updates. No new features go into release branches - only bug fixes.

Let me walk you through the complete workflow. First, initial development. You develop features on main just like normal, merging feature branches when they're complete. When you're ready for your first release, you create a release branch.

Phase two is creating a release. When version 1.0 is ready, you create a release/1.0 branch from main, update the version number to 1.0.0, tag it, and push it. This branch now represents version 1.0 in production. Meanwhile, main continues forward with development for version 2.0.

Phase three is bug fixes. When bugs are found in version 1.0, you checkout the release/1.0 branch, create a fix branch, fix the bug, merge it back to release/1.0, bump the patch version to 1.0.1, tag it, and push. Then critically, you forward port that fix to main so version 2.0 doesn't have the same bug. You can use cherry-pick for this.

Phase four is supporting multiple versions. Eventually you release version 2.0, creating a release/2.0 branch. Now you're supporting three lines of development: release/1.0 for maintenance only, release/2.0 for active support, and main for developing 3.0. When a security fix is needed in all versions, you create the fix, apply it to 1.0, cherry-pick it to 2.0, and cherry-pick it to main. Each gets its own patch version tag.

Let me show you a real-world example: a video editing desktop application. In January, you develop version 1.0 with basic editing and effects. You create a release/1.0 branch and ship it to customers. In April, a customer reports a crash bug. You fix it on release/1.0, tag it as 1.0.1, and ship the emergency update. You also port the fix to main. In June, you release version 2.0 with 4K support and cloud sync. In August, you're supporting both versions - fixing bugs specific to 1.0 on its branch and bugs that affect both versions by applying the fix to both branches. In December, you announce version 1.0 end of life and stop supporting it.

An important concept is semantic versioning. Version numbers follow the format MAJOR.MINOR.PATCH. The patch number increases for bug fixes with no new features. The minor number increases for new features that are backward compatible. The major number increases for breaking changes. So 1.0.0 to 1.0.1 is a bug fix, 1.0.1 to 1.1.0 adds features, and 1.1.0 to 2.0.0 has breaking changes.

Best practices include clear branch naming like release/1.0 and release/2.0, defining a support lifecycle where current versions get full support, previous versions get bug fixes only, and legacy versions get critical security fixes only. Document your releases in a RELEASES.md file with version numbers, dates, and changes. Use conventional commits for automatic changelog generation.

The advantages of release branching are clear. You can support multiple versions, which is critical for enterprise customers. Release branches are stable since they only get bug fixes. You have flexibility for different customers on different versions. And you have a clear history of what's in each version.

The disadvantages are also real. There's maintenance overhead maintaining multiple branches and spending time on old versions. There's complexity managing more branches and remembering to forward-port fixes. Merge conflicts happen as branches diverge over time. And it's resource intensive testing and supporting multiple versions.

Use release branching when you have desktop or mobile software where users are on different versions, enterprise software where customers can't upgrade immediately, API versioning with multiple versions in production, libraries or frameworks where users depend on specific versions, long support cycles requiring years of support, or regulatory compliance requiring validated versions to be maintained. Companies like Microsoft with Windows, the Node.js team with LTS versions, and Ubuntu with LTS releases all use release branching.

Don't use release branching for web applications where all users are on the same version, SaaS products where you can update everyone at once, internal tools where you control deployment, small teams where the overhead is too high, or when you want rapid iteration over stability.

Release branching can be combined with other strategies. You can use feature branches for development and release branches for version management. Or you can use Git Flow's develop branch but keep release branches for maintenance instead of deleting them.

Release branching is essential for software that needs to support multiple versions. It adds complexity, but for many types of software, it's absolutely necessary. Master this strategy, and you'll be able to support enterprise customers and manage complex version requirements with confidence.

Excellent work! You now understand Release Branching. In the next section, we'll learn about the Forking Workflow, which is popular in open source projects.

See you in Section 10!

---

## ✅ Section Summary

**Core Concept:**
- One branch per version in production
- Bug fixes only on release branches
- New features on main
- Forward port fixes to main

**Workflow:**
1. Develop on main
2. Create release branch when ready
3. Tag release
4. Fix bugs on release branch
5. Forward port fixes to main
6. Support multiple versions

**Best For:**
- Desktop/mobile applications
- Enterprise software
- API versioning
- Libraries/frameworks
- Long support cycles

**Semantic Versioning:**
- MAJOR.MINOR.PATCH
- 1.0.0 → 1.0.1 (bug fix)
- 1.0.1 → 1.1.0 (new feature)
- 1.1.0 → 2.0.0 (breaking change)

---

## 📝 Practice Exercise

Practice release branching:

```bash
# Setup and develop 1.0
git init release-practice
cd release-practice
git checkout -b main

echo "# App" > README.md
git add README.md
git commit -m "Initial commit"

# Add features for 1.0
echo "Feature 1" > feature1.txt
git add feature1.txt
git commit -m "Add feature 1"

# Release 1.0
git checkout -b release/1.0
echo "1.0.0" > VERSION
git add VERSION
git commit -m "Release 1.0.0"
git tag v1.0.0

# Continue development on main for 2.0
git checkout main
echo "Feature 2" > feature2.txt
git add feature2.txt
git commit -m "Add feature 2 for version 2.0"

# Bug fix in 1.0
git checkout release/1.0
echo "Bug fix" > fix.txt
git add fix.txt
git commit -m "Fix bug in 1.0"
echo "1.0.1" > VERSION
git add VERSION
git commit -m "Version 1.0.1"
git tag v1.0.1

# Forward port to main
git checkout main
git cherry-pick <commit-hash>

# Release 2.0
git checkout -b release/2.0
echo "2.0.0" > VERSION
git add VERSION
git commit -m "Release 2.0.0"
git tag v2.0.0

# View all releases
git tag
git log --oneline --graph --all
```

---

## 🔗 Next Section

Continue to [Section 10: Forking Workflow](../10-forking-workflow/README.md) →
