# Section 2: Basic Git Commands

## 📖 Overview

In this section, you'll learn the essential Git commands that you'll use every single day as a developer. By the end, you'll be comfortable creating repositories, tracking changes, and managing your code history.

**Duration:** 30 minutes

## 🎯 Learning Objectives

By the end of this section, you will:
- Create and initialize Git repositories
- Track and commit changes
- View project history
- Understand the Git workflow in practice
- Use essential Git commands confidently

---

## 2.1 Creating Your First Repository

### Method 1: Starting from Scratch (Local First)

Create a new project and initialize Git:

```bash
# Create a new directory
mkdir my-first-project
cd my-first-project

# Initialize Git repository
git init

# Verify Git was initialized
ls -la
```

**What happened?** Git created a hidden `.git` folder that stores all the version history.

### Method 2: Cloning an Existing Repository

Download a repository from GitHub:

```bash
# Clone a repository
git clone https://github.com/username/repository-name.git

# Enter the cloned directory
cd repository-name
```

**Use case:** When you want to work on an existing project or contribute to someone else's code.

---

## 2.2 Checking Repository Status

The most important command you'll use:

```bash
git status
```

**What it shows:**
- Which files have been modified
- Which files are staged for commit
- Which files are untracked (new files Git doesn't know about)

### Example Output:

```bash
On branch main
Your branch is up to date with 'origin/main'.

Changes not staged for commit:
  modified:   index.html

Untracked files:
  new-file.txt

no changes added to commit
```

**Pro tip:** Run `git status` frequently! It helps you understand what's happening.

---

## 2.3 The Three States of Git

Understanding these three states is crucial:

```
┌─────────────────┐      ┌─────────────────┐      ┌─────────────────┐
│   Working       │      │    Staging      │      │   Repository    │
│   Directory     │─────▶│     Area        │─────▶│    (Commits)    │
│                 │ add  │                 │commit│                 │
└─────────────────┘      └─────────────────┘      └─────────────────┘
  (Modified files)        (Ready to commit)         (Saved history)
```

1. **Working Directory** - Files you're currently editing
2. **Staging Area** - Files ready to be committed
3. **Repository** - Committed snapshots (history)

---

## 2.4 Adding Files to Staging Area

### Adding Specific Files

```bash
# Add a single file
git add filename.txt

# Add multiple specific files
git add file1.txt file2.txt file3.txt
```

### Adding All Files

```bash
# Add all changed and new files
git add .

# Or (same thing)
git add --all
```

### Adding by Pattern

```bash
# Add all JavaScript files
git add *.js

# Add all files in a directory
git add src/
```

### Practical Example

```bash
# Check status
git status

# Output shows:
# Untracked files:
#   index.html
#   style.css
#   script.js

# Add all files
git add .

# Check status again
git status

# Output shows:
# Changes to be committed:
#   new file:   index.html
#   new file:   style.css
#   new file:   script.js
```

---

## 2.5 Committing Changes

A commit is a snapshot of your project at a specific point in time.

### Basic Commit

```bash
git commit -m "Your commit message here"
```

### Commit Message Best Practices

✅ **Good Commit Messages:**
```bash
git commit -m "Add user login functionality"
git commit -m "Fix navigation menu on mobile devices"
git commit -m "Update README with installation instructions"
```

❌ **Bad Commit Messages:**
```bash
git commit -m "changes"
git commit -m "fix stuff"
git commit -m "asdf"
```

### Commit Message Guidelines

1. **Be specific** - Describe what changed
2. **Use present tense** - "Add feature" not "Added feature"
3. **Keep it short** - 50 characters or less
4. **Start with a verb** - Add, Fix, Update, Remove, etc.

### Adding and Committing in One Step

If files are already tracked (not new):

```bash
git commit -am "Update existing files"
```

This stages and commits modified files in one command.

---

## 2.6 Viewing Commit History

### Basic Log

```bash
git log
```

**Output:**
```
commit a1b2c3d4e5f6g7h8i9j0 (HEAD -> main)
Author: John Doe <john@example.com>
Date:   Wed Nov 20 10:00:00 2024 +0000

    Add user login functionality

commit z9y8x7w6v5u4t3s2r1q0
Author: John Doe <john@example.com>
Date:   Wed Nov 20 09:00:00 2024 +0000

    Initial commit
```

### Condensed Log (One Line Per Commit)

```bash
git log --oneline
```

**Output:**
```
a1b2c3d Add user login functionality
z9y8x7w Initial commit
```

### Visual Graph

```bash
git log --oneline --graph --all
```

Shows a visual representation of branches and merges.

### Last N Commits

```bash
# Show last 5 commits
git log -5

# Show last 3 commits
git log -3
```

---

## 2.7 Viewing Changes

### See What Changed (Not Staged)

```bash
git diff
```

Shows changes in your working directory that aren't staged yet.

### See Staged Changes

```bash
git diff --staged
```

Shows what will be included in your next commit.

### Practical Example

```bash
# Modify a file
echo "New line" >> index.html

# See what changed
git diff

# Output shows:
# +New line

# Stage the change
git add index.html

# Now diff won't show anything (it's staged)
git diff

# But this will show staged changes
git diff --staged
```

---

## 2.8 Undoing Changes

### Discard Changes in Working Directory

```bash
# Discard changes to a specific file
git checkout -- filename.txt

# Or using the newer syntax
git restore filename.txt
```

### Unstage Files

```bash
# Unstage a file (keep the changes)
git reset HEAD filename.txt

# Or using the newer syntax
git restore --staged filename.txt
```

### Amend Last Commit

Made a mistake in your last commit message?

```bash
# Fix the commit message
git commit --amend -m "New commit message"

# Add forgotten files to last commit
git add forgotten-file.txt
git commit --amend --no-edit
```

**Warning:** Only amend commits that haven't been pushed to GitHub!

---

## 2.9 Removing Files

### Remove File from Git and File System

```bash
git rm filename.txt
git commit -m "Remove filename.txt"
```

### Remove from Git but Keep on File System

```bash
git rm --cached filename.txt
git commit -m "Stop tracking filename.txt"
```

**Use case:** You accidentally committed a file that should be ignored (like passwords or API keys).

---

## 2.10 Moving/Renaming Files

### Rename a File

```bash
git mv old-name.txt new-name.txt
git commit -m "Rename old-name.txt to new-name.txt"
```

This is equivalent to:
```bash
mv old-name.txt new-name.txt
git rm old-name.txt
git add new-name.txt
```

---

## 2.11 Ignoring Files (.gitignore)

Some files shouldn't be tracked by Git (passwords, dependencies, system files).

### Creating .gitignore

Create a file named `.gitignore` in your project root:

```bash
# .gitignore file

# Node.js dependencies
node_modules/

# Environment variables
.env
.env.local

# Operating system files
.DS_Store
Thumbs.db

# IDE files
.vscode/
.idea/

# Build output
dist/
build/
*.log
```

### Pattern Rules

```bash
# Ignore specific file
secret.txt

# Ignore all files with extension
*.log

# Ignore entire directory
logs/

# Ignore files in root only
/config.json

# Negative pattern (don't ignore)
!important.log
```

### Applying .gitignore to Already Tracked Files

```bash
# Remove from Git but keep on disk
git rm --cached filename.txt

# Add .gitignore
git add .gitignore

# Commit
git commit -m "Add .gitignore and untrack sensitive files"
```

---

## 2.12 Practical Workflow Example

Here's a complete workflow from start to finish:

```bash
# 1. Create and enter project directory
mkdir my-website
cd my-website

# 2. Initialize Git
git init

# 3. Create files
echo "<h1>Hello World</h1>" > index.html
echo "body { margin: 0; }" > style.css

# 4. Check status
git status

# 5. Add files to staging
git add .

# 6. Check status again
git status

# 7. Commit
git commit -m "Initial commit: Add index.html and style.css"

# 8. Make changes
echo "<p>Welcome to my site</p>" >> index.html

# 9. See what changed
git diff

# 10. Stage and commit
git add index.html
git commit -m "Add welcome message to homepage"

# 11. View history
git log --oneline
```

---

## 🎤 Complete Section Transcript

Welcome back! In this section, we're going to learn the basic Git commands that you'll use every single day.

Let's start by creating our first repository. There are two ways to do this. The first way is to start from scratch on your computer. You create a new folder, navigate into it, and run "git init". This initializes a new Git repository by creating a hidden .git folder that will store all your version history.

The second way is to clone an existing repository from GitHub using "git clone" followed by the repository URL. This downloads the entire project, including all its history, to your computer.

Now, the most important command you'll ever learn is "git status". This command shows you what's happening in your repository right now. It tells you which files have changed, which files are ready to be committed, and which files are new and not being tracked yet. I recommend running "git status" constantly - it's your best friend when working with Git.

To understand Git, you need to know about the three states. First is the working directory - this is where you make changes to your files. Second is the staging area - this is where you prepare changes to be saved. Think of it like packing a box - you choose what goes in. Third is the repository - this is where your snapshots are permanently saved.

To move files from your working directory to the staging area, you use "git add". You can add specific files by naming them, or you can add everything with "git add ." The dot means everything in the current directory.

Once files are staged, you commit them with "git commit". The commit message is really important. It should be clear and describe what changed. Good commit messages say things like "Add user login functionality" or "Fix mobile navigation bug". Bad commit messages say things like "changes" or "fix stuff" - these don't help anyone understand what happened.

You can view your commit history with "git log". This shows all the commits, who made them, when they were made, and the commit messages. If you want a shorter view, use "git log --oneline" which shows one line per commit.

Sometimes you want to see exactly what changed in your files. The "git diff" command shows you the differences between versions. By itself, it shows changes in your working directory. With the "--staged" flag, it shows what's about to be committed.

What if you make a mistake? Git has several ways to undo things. You can discard changes in your working directory with "git restore". You can unstage files with "git restore --staged". And if you made a mistake in your last commit message, you can fix it with "git commit --amend". Just be careful - only amend commits that you haven't pushed to GitHub yet.

There are also commands for removing and renaming files. "git rm" removes a file, and "git mv" renames a file. These commands both change your files and stage those changes in one step.

One more important concept is the .gitignore file. This is a special file where you list files and folders that Git should ignore. Things like passwords, API keys, dependencies like node_modules, and system files like .DS_Store should never be committed to Git. Create a .gitignore file in your project root and list these files there.

Let me show you a complete workflow. You create a new project, initialize Git, create some files, add them to staging, commit them with a message, make some changes, check what changed with git diff, add and commit the changes, and finally view your history with git log. This is the basic cycle you'll repeat over and over as a developer.

These commands might seem like a lot at first, but you'll use them so often that they'll become second nature. The key commands to remember are: git status to check what's happening, git add to stage changes, git commit to save snapshots, and git log to view history. Everything else builds on these basics.

Practice these commands with a sample project. Create some files, make changes, commit them, and play around. The best way to learn Git is by using it.

Great work! You now know the fundamental Git commands. In the next section, we'll dive deeper into branches, which is where Git becomes really powerful.

See you in Section 3!

---

## ✅ Section Summary

**Key Commands:**
- `git init` - Create a new repository
- `git clone` - Copy an existing repository
- `git status` - Check repository status
- `git add` - Stage changes
- `git commit` - Save a snapshot
- `git log` - View history
- `git diff` - See what changed
- `git restore` - Undo changes
- `.gitignore` - Ignore files

**Workflow:**
1. Make changes to files
2. Check status with `git status`
3. Stage changes with `git add`
4. Commit with `git commit -m "message"`
5. Repeat!

---

## 📝 Practice Exercise

Create a simple project to practice:

```bash
# 1. Create a new project
mkdir practice-repo
cd practice-repo
git init

# 2. Create a README
echo "# My Practice Project" > README.md
git add README.md
git commit -m "Initial commit"

# 3. Create more files
echo "console.log('Hello');" > app.js
echo "body { margin: 0; }" > style.css

# 4. Stage and commit
git add .
git commit -m "Add JavaScript and CSS files"

# 5. Make a change
echo "// Main application" >> app.js

# 6. Check what changed
git diff

# 7. Commit the change
git add app.js
git commit -m "Add comment to app.js"

# 8. View history
git log --oneline

# 9. Create .gitignore
echo "node_modules/" > .gitignore
git add .gitignore
git commit -m "Add .gitignore"
```

**Verify your work:**
- [ ] Repository is initialized
- [ ] Multiple commits exist
- [ ] Commit messages are clear
- [ ] .gitignore file is created
- [ ] You understand what each command does

---

## 🔗 Next Section

Ready to learn about branches? Continue to [Section 3: Understanding Branches](../03-understanding-branches/README.md) →

---

## 📚 Additional Resources

- [Git Basics Documentation](https://git-scm.com/book/en/v2/Git-Basics-Getting-a-Git-Repository)
- [Git Cheat Sheet](https://education.github.com/git-cheat-sheet-education.pdf)
- [Interactive Git Tutorial](https://learngitbranching.js.org/)
