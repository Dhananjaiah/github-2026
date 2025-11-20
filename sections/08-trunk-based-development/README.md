# Section 8: Trunk-Based Development

## 📖 Overview

Trunk-Based Development (TBD) is a branching strategy where developers collaborate on code in a single branch called "trunk" (main), using very short-lived feature branches or committing directly to trunk. It's the foundation of continuous integration and deployment at companies like Google, Facebook, and Amazon.

**Duration:** 30 minutes

## 🎯 Learning Objectives

By the end of this section, you will:
- Understand Trunk-Based Development principles
- Implement short-lived branches effectively
- Use feature flags for incomplete features
- Practice continuous integration
- Know when TBD is the right choice

---

## 8.1 What is Trunk-Based Development?

### Core Concept

All developers work on a single branch (trunk/main) with very short-lived feature branches (lasting hours to days, not weeks) or direct commits to trunk. Changes are integrated continuously, often multiple times per day.

### Key Characteristics

1. **Single main branch** - One source of truth
2. **Short-lived branches** - Feature branches last <24 hours
3. **Frequent integration** - Multiple commits per day to main
4. **Feature flags** - Hide incomplete features
5. **Automated testing** - Comprehensive CI/CD
6. **Small commits** - Incremental changes

### Visual Diagram

```
Trunk (main):  A - B - C - D - E - F - G - H - I - J
                   |       |       |
Short-lived:       b1      d1      f1
                   |       |       |
                (2hrs)  (4hrs)  (1hr)

Contrast with long-lived branches:
Other workflows: A ----------- (week) ----------- Z
```

---

## 8.2 Two Variants of TBD

### Variant 1: Committing Directly to Trunk

**Used by small, high-trust teams**

```bash
# Clone repository
git clone https://github.com/company/repo.git
cd repo

# Make changes directly on main
git checkout main
git pull origin main

# Make small change
echo "New feature code" > feature.js
git add feature.js
git commit -m "Add feature X part 1"
git push origin main

# Continue working
echo "More feature code" >> feature.js
git add feature.js
git commit -m "Add feature X part 2"
git push origin main

# Complete feature
echo "Final feature code" >> feature.js
git add feature.js
git commit -m "Complete feature X"
git push origin main
```

**Requirements:**
- Very high test coverage (>80%)
- Fast automated tests (<10 minutes)
- Experienced team
- High trust

### Variant 2: Short-Lived Feature Branches

**Used by most teams**

```bash
# Create short-lived branch
git checkout main
git pull origin main
git checkout -b feature/user-notification

# Work quickly (goal: merge within 24 hours)
echo "Notification system" > notification.js
git add notification.js
git commit -m "Add notification system"

# Push and create PR immediately
git push origin feature/user-notification
# Create PR on GitHub

# Get quick review (within hours)
# Merge to main
git checkout main
git merge feature/user-notification
git push origin main

# Delete branch
git branch -d feature/user-notification
git push origin --delete feature/user-notification
```

**Requirements:**
- Code review required
- Automated tests required
- Merge within 1-2 days maximum

---

## 8.3 Feature Flags (Feature Toggles)

### Why Feature Flags?

Feature flags let you commit incomplete features to trunk without affecting users. You can deploy code with features turned off, test in production, and enable features when ready.

### Basic Feature Flag Implementation

**JavaScript Example:**

```javascript
// featureFlags.js
const featureFlags = {
    newDashboard: false,  // Not ready yet
    darkMode: true,       // Ready for users
    aiSearch: false       // Still developing
};

export default featureFlags;

// Using feature flags
import featureFlags from './featureFlags';

function renderDashboard() {
    if (featureFlags.newDashboard) {
        return <NewDashboard />;
    } else {
        return <OldDashboard />;
    }
}

function renderSearch() {
    if (featureFlags.aiSearch) {
        return <AISearch />;
    } else {
        return <StandardSearch />;
    }
}
```

**Python Example:**

```python
# feature_flags.py
FEATURE_FLAGS = {
    'new_dashboard': False,
    'dark_mode': True,
    'ai_search': False
}

def is_enabled(feature_name):
    return FEATURE_FLAGS.get(feature_name, False)

# Using feature flags
from feature_flags import is_enabled

def render_dashboard():
    if is_enabled('new_dashboard'):
        return render_new_dashboard()
    else:
        return render_old_dashboard()
```

### Advanced Feature Flag Strategies

**Environment-based flags:**
```javascript
const featureFlags = {
    newFeature: process.env.NODE_ENV === 'development',
    betaFeature: ['development', 'staging'].includes(process.env.NODE_ENV)
};
```

**User-based flags:**
```javascript
function isFeatureEnabled(featureName, user) {
    if (featureName === 'betaUI') {
        return user.isBetaTester || user.isAdmin;
    }
    return featureFlags[featureName];
}
```

**Gradual rollout:**
```javascript
function isFeatureEnabled(featureName, userId) {
    if (featureName === 'newAlgorithm') {
        // Enable for 10% of users
        return (userId % 10) === 0;
    }
    return featureFlags[featureName];
}
```

---

## 8.4 Complete TBD Workflow

### Daily Workflow Example

**Developer's Day:**

```bash
# Morning - Start work
git checkout main
git pull origin main

# 9:00 AM - Start small feature
git checkout -b feature/add-export-button
echo "Export button" > export.js
git add export.js
git commit -m "Add export button UI"
git push origin feature/add-export-button
# Create PR immediately

# 10:00 AM - Review comes back, merge
git checkout main
git pull origin main
git branch -d feature/add-export-button

# 10:30 AM - Start next task
git checkout -b feature/export-csv-logic
echo "CSV export" > csv.js
git add csv.js
git commit -m "Add CSV export logic"
git push origin feature/export-csv-logic
# Create PR

# 11:30 AM - Merge second feature
git checkout main
git pull origin main
git branch -d feature/export-csv-logic

# Afternoon - Similar pattern continues
# Multiple small features merged throughout the day
```

**Key Points:**
- Create small, focused branches
- Get code into main quickly
- Multiple integrations per day
- Continuous progress

---

## 8.5 Real-World Example: E-commerce Site

### Scenario: Adding Shopping Cart

**Traditional Approach (Long-lived branch):**
```
Week 1-2: Develop entire shopping cart on feature branch
Week 3: Merge conflicts, integration issues
Week 4: Finally merge, bugs discovered
```

**TBD Approach (Multiple small merges):**

**Day 1 - Morning:**
```bash
# Add cart data structure
git checkout -b add-cart-model
echo "Cart model" > models/cart.js
git add models/cart.js
git commit -m "Add cart data model"
git push origin add-cart-model
# PR created, reviewed, merged within 2 hours
```

**Day 1 - Afternoon:**
```bash
# Add to cart button (behind feature flag)
git checkout main
git pull origin main
git checkout -b add-cart-button
echo "if (featureFlags.cart) { <AddToCartButton /> }" > cartButton.js
git add cartButton.js
git commit -m "Add cart button (behind feature flag)"
git push origin add-cart-button
# PR created, reviewed, merged
```

**Day 2 - Morning:**
```bash
# Cart API endpoints
git checkout main
git pull origin main
git checkout -b cart-api
echo "POST /api/cart/add" > api/cart.js
git add api/cart.js
git commit -m "Add cart API endpoints"
git push origin cart-api
# Merged
```

**Day 2 - Afternoon:**
```bash
# Cart display component
git checkout main
git pull origin main
git checkout -b cart-display
echo "if (featureFlags.cart) { <CartView /> }" > cart.js
git add cart.js
git commit -m "Add cart display component"
git push origin cart-display
# Merged
```

**Day 3:**
```bash
# Complete integration and testing
git checkout main
git pull origin main
git checkout -b cart-integration
echo "Full cart flow" > integration.js
git add integration.js
git commit -m "Complete cart integration"
git push origin cart-integration
# Merged

# Enable feature flag
echo "featureFlags.cart = true" > featureFlags.js
git add featureFlags.js
git commit -m "Enable shopping cart for all users"
git push origin main
```

**Result:** Shopping cart developed in 3 days with 5 merges to main, each reviewed and tested. No large merge conflicts, no integration surprises.

---

## 8.6 Continuous Integration Best Practices

### Fast Automated Tests

```bash
# Test suite must run quickly
npm test  # Should complete in <10 minutes

# Break tests into categories
npm run test:unit      # <2 minutes
npm run test:integration  # <5 minutes
npm run test:e2e       # <10 minutes
```

### Pre-commit Hooks

```bash
# .git/hooks/pre-commit
#!/bin/bash

# Run linter
npm run lint
if [ $? -ne 0 ]; then
    echo "Linting failed. Please fix errors."
    exit 1
fi

# Run unit tests
npm run test:unit
if [ $? -ne 0 ]; then
    echo "Tests failed. Please fix."
    exit 1
fi

echo "Pre-commit checks passed!"
```

### CI Pipeline

```yaml
# .github/workflows/ci.yml
name: CI

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      
      - name: Install dependencies
        run: npm install
      
      - name: Lint
        run: npm run lint
      
      - name: Unit tests
        run: npm run test:unit
      
      - name: Integration tests
        run: npm run test:integration
      
      - name: Build
        run: npm run build
      
      - name: Deploy to staging
        if: github.ref == 'refs/heads/main'
        run: npm run deploy:staging
```

---

## 8.7 Advantages and Disadvantages

### Advantages ✅

**Fast Integration**
- Changes integrated immediately
- No long-lived branch conflicts
- Easier to merge small changes

**Continuous Deployment**
- Deploy multiple times per day
- Fast feedback
- Quick bug fixes

**Simplified Workflow**
- Only one branch to worry about
- No complex branching strategies
- Less cognitive overhead

**Better Collaboration**
- Everyone sees latest changes
- No surprises during integration
- Encourages communication

**Quality Focus**
- Forces good testing practices
- Encourages small, reviewable changes
- Promotes continuous improvement

### Disadvantages ⚠️

**Requires Discipline**
- Must keep trunk stable
- Must commit small changes
- Must fix failures immediately

**High Test Coverage Required**
- Needs comprehensive automated tests
- Tests must be fast
- Expensive to set up initially

**Feature Flag Complexity**
- Must manage feature flags
- Need to clean up old flags
- Can become complex

**Not for Everyone**
- Steep learning curve
- Requires mature practices
- Needs experienced team

**Tooling Required**
- Good CI/CD essential
- Feature flag system needed
- Monitoring critical

---

## 8.8 When to Use TBD

### ✅ Use TBD When:

- **High deployment frequency** - Deploying many times per day
- **Experienced team** - Team comfortable with CI/CD
- **Good test coverage** - Comprehensive automated tests
- **Modern application** - Web apps, APIs, microservices
- **Single version** - Only one production version
- **Fast feedback valued** - Want to catch issues early

**Companies using TBD:**
- Google (thousands of commits/day to monorepo)
- Facebook
- Amazon
- Netflix
- Spotify

### ❌ Don't Use TBD When:

- **Inexperienced team** - Team new to Git/CI/CD
- **Poor test coverage** - Limited automated tests
- **Multiple versions** - Supporting many versions
- **Slow tests** - Tests take hours to run
- **Regulated industry** - Requires extensive approval
- **Mobile apps** - App store delays deployment

---

## 8.9 Transitioning to TBD

### Step-by-Step Migration

**Phase 1: Improve Testing (Month 1)**
```bash
# Increase test coverage
- Add unit tests
- Add integration tests
- Set coverage goals (>80%)
- Make tests fast (<10 min)
```

**Phase 2: Shorten Branch Lifetimes (Month 2)**
```bash
# Gradually reduce branch lifetime
- Week 1: Merge within 1 week
- Week 2: Merge within 3 days
- Week 3: Merge within 1 day
- Week 4: Merge within hours
```

**Phase 3: Implement Feature Flags (Month 3)**
```bash
# Add feature flag system
- Set up feature flag infrastructure
- Start using flags for new features
- Practice enabling/disabling features
```

**Phase 4: Increase Integration Frequency (Month 4)**
```bash
# Commit to main more often
- Multiple commits per day
- Small, incremental changes
- Quick code reviews
```

**Phase 5: Full TBD (Month 5+)**
```bash
# Operating at full TBD
- All developers committing frequently
- Feature flags in use
- Deployment multiple times per day
- High confidence in main branch
```

---

## 8.10 Tips for Success

### Keep Changes Small

```bash
# ❌ Large change (bad)
git commit -m "Complete rewrite of authentication system"
# 50 files changed, 5000 lines added

# ✅ Small changes (good)
git commit -m "Extract authentication logic to separate module"
# 3 files changed, 150 lines added

git commit -m "Add JWT token generation"
# 2 files changed, 80 lines added

git commit -m "Add token validation middleware"
# 2 files changed, 60 lines added
```

### Review Quickly

```bash
# PRs should be reviewed within 1-4 hours
# Not days or weeks

# For TBD to work, reviews must be fast
# Set team expectations:
# - Check for PRs every 2-3 hours
# - Review small PRs immediately
# - Prioritize PR reviews over new work
```

### Fix Failures Immediately

```bash
# If build breaks on main, stop everything
# All hands on deck to fix it

# Options:
# 1. Fix forward (preferred)
git revert <bad-commit>
git push origin main

# 2. Rollback (if necessary)
git reset --hard <last-good-commit>
git push origin main --force
```

### Clean Up Feature Flags

```javascript
// Track feature flag age
const featureFlags = {
    oldFeature: true,  // TODO: Remove after 2024-01-01
    newFeature: false, // Added 2024-06-01
};

// Periodically clean up old flags
// When feature is stable, remove the flag entirely
```

---

## 🎤 Complete Section Transcript

Welcome to Section 8! In this section, we're going to learn about Trunk-Based Development, or TBD, one of the most advanced and efficient branching strategies used by tech giants like Google, Facebook, and Amazon.

So what is Trunk-Based Development? The core concept is that all developers work on a single branch called the trunk or main, using either very short-lived feature branches that last just hours to days, or sometimes even committing directly to trunk. The key is frequent integration - developers integrate their changes multiple times per day, not once a week or once a sprint.

There are two main variants of TBD. The first variant is committing directly to trunk. This is used by small, high-trust teams with very high test coverage. Developers clone the repository, make small changes directly on main, commit, push, and repeat. This requires at least 80% test coverage, fast automated tests that run in under ten minutes, an experienced team, and high trust among team members.

The second variant, which most teams use, involves short-lived feature branches. You create a feature branch, work quickly with the goal of merging within 24 hours, push and create a pull request immediately, get a quick review within hours, merge to main, and delete the branch. The key difference from other workflows is the speed - branches live for hours or at most a couple of days, not weeks or months.

A critical enabler of TBD is feature flags, also called feature toggles. Feature flags let you commit incomplete features to trunk without affecting users. You deploy code with features turned off, test in production with the feature hidden, and enable the feature when it's ready. This is how Google can have thousands of developers committing to the same repository multiple times per day.

Let me show you a simple feature flag implementation. In JavaScript, you might have a featureFlags object with boolean values. Then in your code, you check if a feature is enabled before showing it. If the flag is false, users see the old behavior. If true, they see the new behavior. You can make this even more powerful with environment-based flags, user-based flags for beta testing, or gradual rollout where you enable a feature for 10% of users first.

Let's look at a real-world example. Imagine you're adding a shopping cart to an e-commerce site. With traditional long-lived branches, you might spend two weeks developing the entire cart on a feature branch, then face merge conflicts and integration issues in week three, and finally merge in week four only to discover bugs.

With TBD, you break it into small pieces. Day one morning, you add the cart data model and merge it within two hours. Day one afternoon, you add the "add to cart" button behind a feature flag and merge it. Day two morning, you add cart API endpoints and merge. Day two afternoon, you add the cart display component and merge. Day three, you complete the integration and enable the feature flag. The entire shopping cart is developed in three days with five merges to main, each reviewed and tested, with no large merge conflicts and no integration surprises.

For TBD to work, you need excellent continuous integration practices. Your automated test suite must run quickly - ideally under ten minutes. You should have pre-commit hooks that run linters and unit tests before allowing a commit. Your CI pipeline should run on every push, testing, building, and deploying automatically.

Let's talk about advantages. TBD provides fast integration with no long-lived branch conflicts. It enables continuous deployment with deployments multiple times per day. It simplifies the workflow since there's only one branch. It improves collaboration because everyone sees the latest changes. And it forces quality focus by requiring good testing practices and small, reviewable changes.

But TBD also has disadvantages. It requires discipline - you must keep trunk stable and fix failures immediately. It requires high test coverage with comprehensive, fast automated tests that are expensive to set up initially. It adds feature flag complexity that needs management. It has a steep learning curve and requires a mature, experienced team. And it needs good tooling - solid CI/CD, feature flag systems, and monitoring are essential.

When should you use TBD? It's perfect for teams with high deployment frequency, experienced developers, good test coverage, modern web applications, and a single production version. Companies like Google have thousands of commits per day to their monorepo using TBD.

Don't use TBD if you have an inexperienced team, poor test coverage, multiple versions to support, slow tests, strict regulatory requirements, or mobile apps where app store delays prevent rapid deployment.

If you want to transition to TBD, do it gradually. Month one, improve your testing. Month two, shorten branch lifetimes from weeks to days to hours. Month three, implement feature flags. Month four, increase integration frequency. Month five and beyond, operate at full TBD with everyone committing frequently.

Tips for success: keep changes small - 150 lines is better than 5000. Review PRs quickly - within one to four hours, not days. Fix failures immediately - if the build breaks on main, stop everything and fix it. And clean up old feature flags regularly.

Trunk-Based Development is powerful but demanding. It requires excellent practices and discipline. But teams that master it achieve incredible velocity and quality. It's the foundation of continuous delivery at the world's most successful tech companies.

Great work! You now understand Trunk-Based Development. In the next section, we'll learn about Release Branching, which is useful when you need to support multiple production versions.

See you in Section 9!

---

## ✅ Section Summary

**Core Principles:**
- Single trunk (main) branch
- Very short-lived feature branches (<24 hours)
- Frequent integration (multiple times per day)
- Feature flags for incomplete features
- Comprehensive automated testing

**Two Variants:**
1. Direct commits to trunk (small teams)
2. Short-lived feature branches (most teams)

**Requirements:**
- High test coverage (>80%)
- Fast tests (<10 minutes)
- Quick code reviews (<4 hours)
- Feature flag system
- Strong CI/CD pipeline

**Best For:**
- Web applications
- Continuous deployment
- Experienced teams
- Single production version

---

## 📝 Practice Exercise

Practice TBD with short-lived branches:

```bash
# Setup
mkdir tbd-practice
cd tbd-practice
git init
git checkout -b main

echo "# App" > README.md
git add README.md
git commit -m "Initial commit"

# Simulate a day of TBD
# Morning - Feature 1
git checkout -b feature/add-header
echo "Header" > header.html
git add header.html
git commit -m "Add header component"
git checkout main
git merge feature/add-header
git branch -d feature/add-header

# Mid-morning - Feature 2
git checkout -b feature/add-navigation
echo "Navigation" > nav.html
git add nav.html
git commit -m "Add navigation"
git checkout main
git merge feature/add-navigation
git branch -d feature/add-navigation

# Afternoon - Feature 3 with flag
git checkout -b feature/add-footer
echo "if (flags.footer) { Footer }" > footer.html
git add footer.html
git commit -m "Add footer (behind flag)"
git checkout main
git merge feature/add-footer
git branch -d feature/add-footer

# Enable feature
echo "flags.footer = true" > flags.js
git add flags.js
git commit -m "Enable footer feature"

# View history (notice short-lived branches)
git log --oneline --graph
```

---

## 🔗 Next Section

Continue to [Section 9: Release Branching Strategy](../09-release-branching/README.md) →
