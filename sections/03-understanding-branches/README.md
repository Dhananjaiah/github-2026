# Section 3: Understanding Branches

## 📖 Overview

Branches are one of Git's most powerful features. They allow you to work on different versions of your project simultaneously. In this section, you'll master branching fundamentals that are essential for all branching strategies.

**Duration:** 25 minutes

## 🎯 Learning Objectives

By the end of this section, you will:
- Understand what branches are and why they're important
- Create and switch between branches
- Merge branches together
- Delete branches
- Understand branch naming conventions

---

## 3.1 What Are Branches?

### The Concept

A branch is like a parallel universe for your code. You can create a branch to try out a new feature without affecting your main code. If the experiment works, you merge it back. If it doesn't, you delete the branch.

### Real-World Analogy

Imagine you're writing a book:
- **Main branch** - Your published manuscript
- **Feature branch** - A copy where you try adding a new chapter
- If readers like it → merge it into the main book
- If readers don't like it → delete the experiment, main book is unchanged

### Visual Diagram

```
main branch:     A --- B --- C --- D
                          \
feature branch:            E --- F
```

Each letter represents a commit. The feature branch splits off from B, adds commits E and F, while main continues independently.

---

## 3.2 Why Use Branches?

### Benefits

1. **Isolation** - Work on features without breaking main code
2. **Experimentation** - Try ideas safely
3. **Collaboration** - Multiple people work on different features simultaneously
4. **Organization** - Keep different types of work separate
5. **Code Review** - Changes can be reviewed before merging

### Example Scenarios

**Scenario 1: Adding a New Feature**
```
main: Working production code
feature/user-authentication: New login system being developed
```

**Scenario 2: Fixing a Bug**
```
main: Current version
hotfix/critical-security-bug: Urgent fix being developed
```

**Scenario 3: Team Collaboration**
```
main: Production code
feature/payment-integration: Developer A working on payments
feature/email-notifications: Developer B working on emails
```

---

## 3.3 Viewing Branches

### List All Branches

```bash
git branch
```

**Output:**
```
* main
  feature-branch
  another-branch
```

The asterisk (*) shows your current branch.

### List Remote Branches

```bash
git branch -r
```

Shows branches on GitHub (remote).

### List All Branches (Local and Remote)

```bash
git branch -a
```

---

## 3.4 Creating Branches

### Method 1: Create and Stay on Current Branch

```bash
git branch feature-login
```

This creates a new branch but doesn't switch to it.

### Method 2: Create and Switch Immediately

```bash
git checkout -b feature-login
```

Or with the newer syntax:

```bash
git switch -c feature-login
```

**This is the most common method!**

### Visual Example

```bash
# Before creating branch
main: A --- B --- C (HEAD)

# After: git checkout -b feature-login
main:           A --- B --- C
                            |
feature-login:              C (HEAD)
```

Both branches point to the same commit initially.

---

## 3.5 Switching Branches

### Using Checkout (Traditional)

```bash
git checkout branch-name
```

### Using Switch (Newer)

```bash
git switch branch-name
```

### Important Rules

⚠️ **Before switching branches:**
1. Commit your changes, OR
2. Stash your changes (we'll learn this later)

If you have uncommitted changes, Git will warn you:
```
error: Your local changes to the following files would be overwritten by checkout:
    file.txt
Please commit your changes or stash them before you switch branches.
```

### Practical Example

```bash
# Check current branch
git branch
# * main

# Create and switch to new branch
git checkout -b feature-new-design

# Check current branch again
git branch
# * feature-new-design
#   main

# Switch back to main
git checkout main

# Check current branch
git branch
# * main
#   feature-new-design
```

---

## 3.6 Making Changes on a Branch

### Complete Workflow

```bash
# 1. Create new branch
git checkout -b feature-contact-form

# 2. Make changes
echo "<form>Contact Us</form>" > contact.html

# 3. Stage changes
git add contact.html

# 4. Commit changes
git commit -m "Add contact form"

# 5. View branch status
git log --oneline --graph --all
```

**Result:**
```
* a1b2c3d (HEAD -> feature-contact-form) Add contact form
* z9y8x7w (main) Previous commit
```

The branches have now diverged!

---

## 3.7 Merging Branches

Merging combines changes from one branch into another.

### Fast-Forward Merge

When no new commits exist on the target branch:

```bash
# Switch to main
git checkout main

# Merge feature branch
git merge feature-contact-form
```

**Visual:**
```
Before merge:
main:         A --- B
                    \
feature:             C --- D (feature-contact-form)

After fast-forward merge:
main:         A --- B --- C --- D (main, feature-contact-form)
```

### Three-Way Merge

When both branches have new commits:

```bash
# Switch to main
git checkout main

# Merge feature branch
git merge feature-contact-form
```

**Visual:**
```
Before merge:
main:         A --- B --- C
                    \
feature:             D --- E

After three-way merge:
main:         A --- B --- C --- F (merge commit)
                    \         /
feature:             D --- E
```

F is a special "merge commit" that combines changes from both branches.

---

## 3.8 Merge Conflicts

### What is a Merge Conflict?

A conflict occurs when the same part of a file is changed in both branches.

### Example Scenario

```bash
# On main branch
echo "Version A" > file.txt
git add file.txt
git commit -m "Add version A"

# Create and switch to feature branch
git checkout -b feature

# Change the same file
echo "Version B" > file.txt
git add file.txt
git commit -m "Add version B"

# Switch back to main
git checkout main

# Change the file again
echo "Version C" > file.txt
git add file.txt
git commit -m "Add version C"

# Try to merge - CONFLICT!
git merge feature
```

**Git's output:**
```
Auto-merging file.txt
CONFLICT (content): Merge conflict in file.txt
Automatic merge failed; fix conflicts and then commit the result.
```

### Resolving Conflicts

When you open the conflicted file:

```
<<<<<<< HEAD
Version C
=======
Version B
>>>>>>> feature
```

**Explanation:**
- `<<<<<<< HEAD` - Start of current branch's version
- `=======` - Separator
- `>>>>>>> feature` - End of incoming branch's version

**To resolve:**

1. Edit the file to choose what to keep:
```
Version C and B combined
```

2. Remove conflict markers (<<<<<<<, =======, >>>>>>>)

3. Stage the resolved file:
```bash
git add file.txt
```

4. Complete the merge:
```bash
git commit -m "Merge feature branch, resolve conflicts"
```

### Tips for Avoiding Conflicts

1. **Pull frequently** - Keep your branch updated
2. **Commit often** - Smaller changes = fewer conflicts
3. **Communicate** - Coordinate with team members
4. **Merge main into your branch regularly** - Stay in sync

---

## 3.9 Deleting Branches

### Delete a Local Branch

```bash
# Delete a merged branch
git branch -d branch-name

# Force delete (even if not merged)
git branch -D branch-name
```

### Delete a Remote Branch

```bash
git push origin --delete branch-name
```

### When to Delete Branches

✅ **Delete when:**
- Feature is complete and merged
- Experiment failed and you don't need it
- Branch is outdated

❌ **Don't delete:**
- Branches you're still working on
- Branches others might be using
- Main/production branches

### Example

```bash
# Create and merge a feature
git checkout -b quick-fix
echo "Fix" > fix.txt
git add fix.txt
git commit -m "Quick fix"

git checkout main
git merge quick-fix

# Delete the branch (no longer needed)
git branch -d quick-fix

# Verify it's gone
git branch
```

---

## 3.10 Branch Naming Conventions

Good branch names make collaboration easier.

### Common Patterns

```bash
# Feature branches
feature/user-authentication
feature/payment-integration
feature/dark-mode

# Bug fixes
bugfix/login-error
bugfix/memory-leak
fix/typo-in-homepage

# Hotfixes (urgent production fixes)
hotfix/security-vulnerability
hotfix/critical-crash

# Releases
release/v1.0.0
release/2024-11-20

# Experiments
experiment/new-architecture
experiment/performance-test
```

### Naming Best Practices

✅ **Good:**
- `feature/add-user-profile`
- `bugfix/fix-login-redirect`
- `hotfix/security-patch-1`

❌ **Bad:**
- `new-stuff`
- `johns-branch`
- `test123`

### Guidelines

1. **Be descriptive** - Name explains the purpose
2. **Use prefixes** - feature/, bugfix/, hotfix/, etc.
3. **Use hyphens** - Separate words with hyphens, not spaces
4. **Keep it lowercase** - Easier to type
5. **Be consistent** - Follow team conventions

---

## 3.11 Practical Branching Workflow

Here's a complete real-world example:

```bash
# Starting on main branch
git checkout main

# Make sure main is up to date
git pull origin main

# Create a new feature branch
git checkout -b feature/shopping-cart

# Work on the feature
echo "Shopping cart code" > cart.js
git add cart.js
git commit -m "Add shopping cart functionality"

echo "More cart features" >> cart.js
git add cart.js
git commit -m "Add item removal from cart"

# Push branch to GitHub
git push origin feature/shopping-cart

# Switch back to main
git checkout main

# Merge the feature (after it's reviewed)
git merge feature/shopping-cart

# Push updated main
git push origin main

# Delete the feature branch
git branch -d feature/shopping-cart
git push origin --delete feature/shopping-cart
```

---

## 🎤 Complete Section Transcript

Welcome to Section 3! Now we're going to learn about one of Git's most powerful features: branches.

So what is a branch? Think of it as a parallel universe for your code. You can create a branch to experiment with a new feature without touching your main code. If the experiment works out, you merge it back into main. If it doesn't work, you just delete the branch and your main code is completely unaffected.

Here's a real-world analogy. Imagine you're writing a book, and it's already published. Now you want to try adding a new chapter. You don't want to change the published book directly because what if readers don't like the new chapter? Instead, you create a copy, add the chapter there, get feedback, and only if people like it do you add it to the main book. That's exactly what branches do for your code.

Why are branches so important? First, they provide isolation - you can work on a feature without breaking the main code. Second, they enable experimentation - try new ideas safely. Third, they enable collaboration - multiple developers can work on different features at the same time. Fourth, they provide organization - different types of work stay separate. And fifth, they enable code review - changes can be reviewed before they go into production.

Let's learn the basic branch commands. To see all your branches, use "git branch". The branch with an asterisk is your current branch. To create a new branch, you can use "git branch branch-name", but the most common way is "git checkout -b branch-name" which creates a new branch and immediately switches to it.

To switch between branches, use "git checkout branch-name" or the newer "git switch branch-name". But be careful - before switching branches, you need to commit your changes. If you have uncommitted changes, Git will warn you and prevent the switch.

Once you're on a branch, you work normally - make changes, stage them with git add, commit them with git commit. These commits only affect the current branch. Your main branch stays exactly as it was.

When your feature is complete, you merge the branch back into main. Merging combines changes from one branch into another. Switch to the branch you want to merge into - usually main - and run "git merge feature-branch-name".

There are two types of merges. A fast-forward merge happens when the main branch hasn't changed since you created your feature branch. Git simply moves the main pointer forward to include your commits. A three-way merge happens when both branches have new commits. Git creates a special merge commit that combines changes from both branches.

Sometimes, merging causes conflicts. This happens when the same part of a file was changed in both branches. Git doesn't know which version to keep, so it asks you to decide. When you open a conflicted file, you'll see special markers. The section between "HEAD" and the equals signs is your current branch's version. The section between equals signs and the branch name is the incoming branch's version. To resolve the conflict, edit the file to keep what you want, remove the conflict markers, save the file, stage it with git add, and complete the merge with git commit.

After a branch is merged and you don't need it anymore, you can delete it with "git branch -d branch-name". This keeps your repository clean and organized.

Let's talk about branch naming conventions. Good branch names make collaboration much easier. Common patterns include "feature/" for new features, "bugfix/" for bug fixes, "hotfix/" for urgent production fixes, and "release/" for release preparation. For example, "feature/user-authentication" or "bugfix/login-error". Use descriptive names, use hyphens to separate words, keep them lowercase, and be consistent with your team's conventions.

Here's a complete workflow. You start on main, pull the latest changes, create a new feature branch, work on your feature making multiple commits, push your branch to GitHub, get it reviewed by your team, merge it back to main, push the updated main, and finally delete the feature branch both locally and on GitHub.

Branches might seem complex at first, but they're absolutely essential for modern development. Every branching strategy we'll learn later builds on these fundamentals. Practice creating branches, making commits, merging them, and deleting them until you're comfortable with the process.

You're doing great! You now understand the core concepts of branching. In the next sections, we'll explore different branching strategies that teams use in production.

See you in Section 4!

---

## ✅ Section Summary

**Key Commands:**
- `git branch` - List branches
- `git checkout -b name` - Create and switch to branch
- `git switch name` - Switch branches
- `git merge branch` - Merge branch into current branch
- `git branch -d name` - Delete branch

**Key Concepts:**
- Branches create parallel development paths
- Always commit before switching branches
- Merge combines branches
- Conflicts occur when same code is changed in both branches
- Use descriptive branch names with prefixes

**Workflow:**
1. Create branch from main
2. Make commits on branch
3. Merge back to main
4. Delete branch

---

## 📝 Practice Exercise

Practice branching with this exercise:

```bash
# 1. Create a new repository
mkdir branch-practice
cd branch-practice
git init

# 2. Create initial commit
echo "# My Project" > README.md
git add README.md
git commit -m "Initial commit"

# 3. Create a feature branch
git checkout -b feature/add-homepage

# 4. Add content
echo "<h1>Welcome</h1>" > index.html
git add index.html
git commit -m "Add homepage"

# 5. Create another branch from main
git checkout main
git checkout -b feature/add-about

# 6. Add different content
echo "<h1>About Us</h1>" > about.html
git add about.html
git commit -m "Add about page"

# 7. Merge first feature
git checkout main
git merge feature/add-homepage

# 8. Merge second feature
git merge feature/add-about

# 9. View the result
git log --oneline --graph --all

# 10. Clean up
git branch -d feature/add-homepage
git branch -d feature/add-about
```

**Verify:**
- [ ] You created multiple branches
- [ ] You made commits on different branches
- [ ] You merged branches successfully
- [ ] You deleted branches after merging
- [ ] You understand the workflow

---

## 🔗 Next Section

Ready to learn real-world branching strategies? Continue to [Section 4: Feature Branch Workflow](../04-feature-branch-workflow/README.md) →
