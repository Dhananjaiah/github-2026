# Section 11: Merge vs Rebase - Deep Dive

## 📖 Overview

Understanding the difference between merge and rebase is crucial for maintaining clean Git history. Both integrate changes from one branch to another, but they do it very differently.

**Duration:** 30 minutes

## 🎯 Learning Objectives

By the end of this section, you will:
- Understand how merge and rebase work
- Know when to use each approach
- Master interactive rebase
- Avoid common pitfalls
- Choose the right strategy for your team

---

## 11.1 What is Merging?

### How Merge Works

Merge creates a new "merge commit" that combines changes from two branches.

```
Before merge:
main:     A --- B --- C
               \
feature:        D --- E

After git merge feature:
main:     A --- B --- C --- F (merge commit)
               \         /
feature:        D --- E
```

### Merge Command

```bash
git checkout main
git merge feature
```

### Characteristics

✅ **Preserves history** - All commits remain
✅ **Non-destructive** - Doesn't rewrite commits
✅ **Shows branch points** - Clear where branches diverged
⚠️ **Cluttered history** - Many merge commits
⚠️ **Complex graph** - Hard to follow linear path

---

## 11.2 What is Rebasing?

### How Rebase Works

Rebase moves your commits to a new base, rewriting history.

```
Before rebase:
main:     A --- B --- C
               \
feature:        D --- E

After git rebase main:
main:     A --- B --- C
                       \
feature:                D' --- E'
```

D and E are rewritten as D' and E' on top of C.

### Rebase Command

```bash
git checkout feature
git rebase main
```

### Characteristics

✅ **Clean history** - Linear, easy to follow
✅ **No merge commits** - Cleaner log
✅ **Easy to understand** - Straightforward timeline
⚠️ **Rewrites history** - Changes commit SHAs
⚠️ **Can cause conflicts** - Need to resolve per commit
⚠️ **Dangerous if shared** - Don't rebase public branches

---

## 11.3 Side-by-Side Comparison

### Example Scenario

```bash
# Initial setup
git checkout main
echo "A" > file.txt
git add file.txt
git commit -m "Commit A"

echo "B" >> file.txt
git add file.txt
git commit -m "Commit B"

# Create feature branch
git checkout -b feature
echo "D" >> file.txt
git add file.txt
git commit -m "Commit D"

echo "E" >> file.txt
git add file.txt
git commit -m "Commit E"

# Meanwhile, main progresses
git checkout main
echo "C" >> file.txt
git add file.txt
git commit -m "Commit C"
```

**Current state:**
```
main:     A --- B --- C
               \
feature:        D --- E
```

### Option 1: Merge

```bash
git checkout main
git merge feature
```

**Result:**
```
main:     A --- B --- C --- F (merge commit)
               \         /
feature:        D --- E
```

**Log output:**
```
git log --oneline --graph

* F Merge branch 'feature'
|\
| * E Commit E
| * D Commit D
* | C Commit C
|/
* B Commit B
* A Commit A
```

### Option 2: Rebase

```bash
git checkout feature
git rebase main
```

**Result:**
```
main:     A --- B --- C
                       \
feature:                D' --- E'
```

**Log output:**
```
git log --oneline --graph feature

* E' Commit E
* D' Commit D
* C Commit C
* B Commit B
* A Commit A
```

---

## 11.4 When to Use Merge

### ✅ Use Merge When:

**1. Working on Public Branches**
```bash
# Public branches that others use
git checkout main
git merge feature
```

**2. Preserving Branch History**
```bash
# When you want to see where branches diverged
git merge --no-ff feature
```

**3. Multiple People on Same Branch**
```bash
# Team members working on feature branch
git pull origin feature  # This does fetch + merge
```

**4. Want to Show Collaboration**
```bash
# Merge commits show "Feature X" was a unit of work
git merge feature/user-authentication
```

### Merge Strategies

**Fast-Forward Merge (Default when possible):**
```bash
main:     A --- B
               \
feature:        C --- D

After merge:
main:     A --- B --- C --- D
```

No merge commit needed!

**No Fast-Forward (Force merge commit):**
```bash
git merge --no-ff feature

Creates merge commit even when fast-forward possible
```

**Squash Merge (Combine all commits):**
```bash
git merge --squash feature

All feature commits → single commit on main
```

---

## 11.5 When to Use Rebase

### ✅ Use Rebase When:

**1. Cleaning Up Local Commits**
```bash
# Before pushing, clean up messy commits
git rebase -i HEAD~3
```

**2. Keeping Feature Branch Updated**
```bash
# Update feature branch with latest main
git checkout feature
git rebase main
```

**3. Linear History Preference**
```bash
# Team prefers linear history
git rebase main
git checkout main
git merge feature  # Fast-forward merge
```

**4. Before Creating PR**
```bash
# Clean up before submitting for review
git rebase -i main
```

---

## 11.6 Interactive Rebase

Interactive rebase is a powerful tool for rewriting history.

### Basic Interactive Rebase

```bash
# Rebase last 3 commits
git rebase -i HEAD~3
```

**Editor opens with:**
```
pick abc123 Add feature A
pick def456 Fix typo
pick ghi789 Add feature B

# Commands:
# p, pick = use commit
# r, reword = use commit, but edit message
# e, edit = use commit, but stop for amending
# s, squash = use commit, but meld into previous
# f, fixup = like squash, but discard message
# d, drop = remove commit
```

### Common Interactive Rebase Operations

**1. Squashing Commits**
```
pick abc123 Add feature A
squash def456 Fix typo
squash ghi789 Add more to feature A

Result: All three commits → one commit
```

**2. Rewording Commit Messages**
```
pick abc123 Add feature A
reword def456 Fix typo
pick ghi789 Add feature B

You'll be prompted to change the message for def456
```

**3. Reordering Commits**
```
pick ghi789 Add feature B
pick abc123 Add feature A
pick def456 Fix typo

Commits will be applied in this new order
```

**4. Dropping Commits**
```
pick abc123 Add feature A
drop def456 Experimental change that didn't work
pick ghi789 Add feature B

def456 will be removed from history
```

**5. Editing Commits**
```
pick abc123 Add feature A
edit def456 Fix typo
pick ghi789 Add feature B

Rebase will stop at def456, allowing you to:
- Amend the commit
- Split it into multiple commits
- Make additional changes
```

### Real-World Example: Cleaning Up History

**Before (messy commits):**
```bash
git log --oneline

ghi789 Fix typo in previous commit
def456 WIP: working on feature
abc123 Add feature - forgot file
xyz999 Add feature implementation
```

**Clean it up:**
```bash
git rebase -i HEAD~4

# Change to:
pick xyz999 Add feature implementation
fixup abc123 Add feature - forgot file
fixup def456 WIP: working on feature
fixup ghi789 Fix typo in previous commit

# Result: One clean commit
```

**After:**
```bash
git log --oneline

xyz999' Implement user authentication feature
```

---

## 11.7 The Golden Rule of Rebase

### 🚨 NEVER REBASE PUBLIC BRANCHES 🚨

**❌ NEVER DO THIS:**
```bash
# main is public - everyone uses it
git checkout main
git rebase feature  # DON'T!

# This rewrites main's history
# Everyone else's main is now incompatible
```

**✅ DO THIS INSTEAD:**
```bash
git checkout main
git merge feature
```

### Why Is This Rule Critical?

**Scenario: Rebasing a Public Branch**

**Alice's perspective:**
```bash
main:     A --- B --- C
               \
feature:        D --- E

Alice rebases main (BAD!)
main:     A --- B' --- C' --- D --- E
```

**Bob's perspective:**
```bash
# Bob pulls and gets:
error: Your local changes to the following files would be overwritten

# Bob's main still has: A --- B --- C
# Remote main now has: A --- B' --- C' --- D --- E
# B and B' are different commits!
# Complete chaos!
```

### Safe Rebasing

**✅ Safe to rebase:**
- Your local commits not yet pushed
- Your feature branch (if you're the only one working on it)
- Cleaning up before creating PR

**❌ Never rebase:**
- main/master branch
- develop branch (in Git Flow)
- Any branch others are using
- Any commits you've already pushed (if others pulled them)

---

## 11.8 Handling Conflicts

### Merge Conflicts

```bash
git merge feature

# Conflict!
Auto-merging file.txt
CONFLICT (content): Merge conflict in file.txt
Automatic merge failed; fix conflicts and then commit the result.

# Fix conflicts in file.txt
vim file.txt

# Stage resolved file
git add file.txt

# Complete merge
git commit
```

**One conflict resolution per merge.**

### Rebase Conflicts

```bash
git rebase main

# Conflict!
CONFLICT (content): Merge conflict in file.txt
error: could not apply abc123... Commit message

# Fix conflicts
vim file.txt
git add file.txt

# Continue rebase
git rebase --continue

# Another conflict might appear!
# Fix and continue again

# Or abort
git rebase --abort
```

**May need to resolve conflicts for each commit being rebased.**

---

## 11.9 Practical Workflows

### Workflow 1: Feature Branch with Merge

```bash
# Create feature
git checkout -b feature/new-ui
# ... make commits ...

# Update from main (using merge)
git checkout main
git pull origin main
git checkout feature/new-ui
git merge main

# More work
# ... make commits ...

# Final merge to main
git checkout main
git merge feature/new-ui --no-ff
git push origin main
```

**Result:** Preserves all history and branch structure.

### Workflow 2: Feature Branch with Rebase

```bash
# Create feature
git checkout -b feature/new-ui
# ... make commits ...

# Update from main (using rebase)
git fetch origin
git rebase origin/main

# More work
# ... make commits ...

# Clean up before PR
git rebase -i origin/main

# Final merge (fast-forward)
git checkout main
git merge feature/new-ui
git push origin main
```

**Result:** Clean, linear history.

### Workflow 3: Hybrid Approach (Recommended)

```bash
# Rebase locally, merge publicly
git checkout -b feature/new-ui
# ... make commits ...

# Regularly update with rebase
git fetch origin
git rebase origin/main

# Clean up commits
git rebase -i origin/main

# Merge to main
git checkout main
git merge feature/new-ui --no-ff
git push origin main
```

**Result:** Clean feature history + clear merge points.

---

## 11.10 Team Guidelines

### For Teams Preferring Merge

**GitHub Settings:**
```
Settings → Branches → Branch protection rules
✅ Allow merge commits
❌ Allow squash commits (optional)
❌ Allow rebase commits (optional)
```

**Guidelines:**
- Use `git merge --no-ff` for features
- Keep feature branches clean
- Write good commit messages
- Delete branches after merging

### For Teams Preferring Rebase

**GitHub Settings:**
```
Settings → Branches → Branch protection rules
❌ Allow merge commits
✅ Allow squash commits (optional)
✅ Allow rebase commits
```

**Guidelines:**
- Use `git rebase` to update feature branches
- Clean up with `git rebase -i` before PR
- Fast-forward merge to main
- Never rebase shared branches

### For Teams Using Hybrid

**GitHub Settings:**
```
Settings → Branches → Branch protection rules
✅ Allow merge commits
✅ Allow squash commits
✅ Allow rebase commits
```

**Guidelines:**
- Rebase feature branches locally
- Use interactive rebase to clean history
- Merge feature to main with `--no-ff`
- Squash small fixes, keep feature structure

---

## 11.11 Comparison Summary

| Aspect | Merge | Rebase |
|--------|-------|--------|
| **History** | Preserves exact history | Rewrites history |
| **Graph** | Non-linear | Linear |
| **Conflicts** | Once per merge | Once per commit |
| **Safety** | Safe for public branches | Dangerous for public branches |
| **Use case** | Public branches | Local cleanup |
| **Readability** | Shows collaboration | Shows progression |
| **Complexity** | Simple | More complex |

---

## 🎤 Complete Section Transcript

Welcome to Section 11! This is a crucial section where we'll dive deep into the differences between merge and rebase, two fundamental Git operations that developers often find confusing.

Both merge and rebase integrate changes from one branch into another, but they do it in completely different ways. Understanding when to use each one is essential for maintaining clean Git history and avoiding disasters.

Let's start with merging. When you merge a branch, Git creates a new "merge commit" that combines changes from two branches. If you have main with commits A, B, C and a feature branch with commits D and E, merging feature into main creates a new commit F that has two parents: C and E. This preserves the exact history of how the work happened.

The characteristics of merging are that it preserves complete history, it's non-destructive since it doesn't rewrite any commits, and it clearly shows where branches diverged and converged. However, it can create a cluttered history with many merge commits, and the graph can become complex and hard to follow in large projects.

Now let's look at rebasing. When you rebase, Git moves your commits to a new base, effectively rewriting history. Using the same example, if you rebase feature onto main, Git takes commits D and E, creates new versions of them called D-prime and E-prime, and places them on top of C. The original D and E are no longer part of the branch.

The characteristics of rebasing are that it creates clean, linear history that's easy to follow, there are no merge commits cluttering the log, and the timeline is straightforward. However, it rewrites history by changing commit SHAs, it can cause conflicts that need to be resolved for each commit being rebased, and it's dangerous if used on shared branches.

Let me show you a side-by-side comparison. Imagine you created a feature branch, made commits D and E, and meanwhile main progressed with commit C. If you merge feature into main, you get a merge commit F with two parents, creating a diamond shape in the history. If you rebase feature onto main, commits D and E are rewritten as D-prime and E-prime on top of C, creating a linear history.

When should you use merge? Use merge when working on public branches that others use. Use it when you want to preserve the history of how work happened and where branches diverged. Use it when multiple people are working on the same branch. And use it when you want merge commits to show that a feature was a unit of work.

When should you use rebase? Use rebase when cleaning up local commits before pushing. Use it when keeping your feature branch updated with the latest main. Use it when your team prefers linear history. And use it before creating a pull request to clean up your commits.

Interactive rebase is one of Git's most powerful features. When you run "git rebase -i HEAD~3", Git opens an editor showing your last three commits. You can pick, reword, edit, squash, fixup, or drop commits. Squash combines commits together. Reword lets you change commit messages. Edit lets you stop and make changes. Drop removes commits entirely. You can even reorder commits.

Here's a real-world example. Before rebasing, you might have messy commits like "WIP: working on feature", "Add feature - forgot file", and "Fix typo in previous commit". Using interactive rebase, you can squash these into one clean commit like "Implement user authentication feature". This makes your PR much easier to review.

Now, the golden rule of rebasing, and I cannot stress this enough: NEVER REBASE PUBLIC BRANCHES. Never rebase main, never rebase develop, never rebase any branch that other people are using. Why? Because rebasing rewrites history. If you rebase main and push it, everyone else's main becomes incompatible with the rebase main. Their commits are based on the old history, and now that history doesn't exist. It creates complete chaos.

What's safe to rebase? Your local commits that you haven't pushed yet. Your feature branch if you're the only one working on it. Cleaning up commits before creating a pull request. What's never safe to rebase? Any branch others are using, any commits you've already pushed that others have pulled, and definitely not main or develop.

Handling conflicts is different between merge and rebase. With merge, you resolve conflicts once and complete the merge with one commit. With rebase, you might need to resolve conflicts for each commit being rebased. If you have five commits and they all conflict, you resolve, continue, resolve, continue, five times. You can always abort with "git rebase --abort" if it gets too messy.

Let me show you three practical workflows. The first workflow uses merge exclusively. You create a feature branch, periodically merge main into it to stay updated, and finally merge it to main with --no-ff. This preserves all history and branch structure.

The second workflow uses rebase. You create a feature branch, rebase onto main to stay updated, clean up with interactive rebase before creating your PR, and finally fast-forward merge to main. This creates clean, linear history.

The third workflow, which I recommend for most teams, is a hybrid approach. You rebase locally to keep your feature branch updated and clean up commits. But you merge publicly to preserve the feature as a unit of work. This gives you both clean feature history and clear merge points showing when features were integrated.

For team guidelines, if your team prefers merge, configure GitHub to allow merge commits, use --no-ff for features, and write good commit messages. If your team prefers rebase, configure GitHub to allow rebase commits, use rebase to update feature branches, clean up with interactive rebase, and never rebase shared branches. For hybrid approaches, allow all commit types and use rebase locally but merge publicly.

To summarize: merge preserves exact history but can be cluttered, rebase creates linear history but rewrites commits. Merge is safe for public branches, rebase is dangerous for them. Merge handles conflicts once, rebase may require resolving conflicts multiple times. Use merge for collaboration and public branches. Use rebase for local cleanup and linear history. And always remember the golden rule: never rebase public branches.

Excellent work! You now understand the difference between merge and rebase. In the final section, we'll cover best practices and real-world examples that tie everything together.

See you in Section 12!

---

## ✅ Section Summary

**Merge:**
- Creates merge commits
- Preserves exact history
- Safe for public branches
- Use for collaboration

**Rebase:**
- Rewrites history linearly
- Clean, straightforward history
- Dangerous for public branches
- Use for local cleanup

**Golden Rule:**
🚨 Never rebase public branches!

**Best Practice:**
- Rebase locally
- Merge publicly
- Use interactive rebase to clean up
- Communicate with your team

---

## 📝 Practice Exercise

Practice merge vs rebase:

```bash
# Setup
git init merge-rebase-practice
cd merge-rebase-practice
git checkout -b main

# Create commits
echo "A" > file.txt
git add file.txt
git commit -m "Commit A"

echo "B" >> file.txt
git add file.txt
git commit -m "Commit B"

# Create feature branch
git checkout -b feature
echo "D" >> file.txt
git add file.txt
git commit -m "Commit D"

echo "E" >> file.txt
git add file.txt
git commit -m "Commit E"

# Main progresses
git checkout main
echo "C" >> file.txt
git add file.txt
git commit -m "Commit C"

# Try merge
git branch feature-merge feature
git merge feature-merge
git log --oneline --graph

# Reset and try rebase
git reset --hard HEAD~1
git checkout feature
git rebase main
git log --oneline --graph

# Compare the histories!
```

---

## 🔗 Next Section

Continue to [Section 12: Best Practices and Real-World Examples](../12-best-practices/README.md) →
