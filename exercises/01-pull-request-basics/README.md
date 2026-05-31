# Exercise 1: Pull Request Basics 🔄

## What is a Pull Request?

A **Pull Request (PR)** is GitHub's way of saying: *"Hey, I made some changes. Can you review them before we merge them?"*

Think of it as a **code review checkpoint** before changes become part of the main codebase.

## Why Use PRs?

1. **Code Quality** - Others can review before merging
2. **Discussion** - Team can discuss changes
3. **Testing** - CI/CD can run automated tests
4. **Documentation** - PR descriptions explain *why* changes were made

## The PR Workflow

```
1. Create a branch          → git checkout -b feature/my-feature
2. Make changes            → edit files, commit
3. Push branch             → git push origin feature/my-feature
4. Open PR on GitHub       → GitHub UI → "Compare & pull request"
5. Review & discuss        → Team reviews, suggests changes
6. Merge                   → Once approved, merge into main
```

## 🎯 Exercise 1.1: Create Your First PR

### Step 1: Create a Feature Branch

```bash
cd /home/neo/.openclaw/workspace/github-learning-lab

# Create and switch to a new branch
git checkout -b feature/add-introduction

# Verify you're on the right branch
git branch  # Should show * feature/add-introduction
```

### Step 2: Make a Change

Create a new file called `INTRODUCTION.md` in the root:

```markdown
# Hello, GitHub! 👋

This is my first pull request practice file.

## What I'm Learning

- Creating branches
- Making commits
- Opening pull requests
- Code review process

## My Goals

- Understand the PR workflow
- Practice collaboration
- Get comfortable with GitHub's interface
```

```bash
# Create the file (you can use nano, vim, or any editor)
nano INTRODUCTION.md

# Or use echo for quick creation:
echo "# Hello, GitHub! 👋" > INTRODUCTION.md
```

### Step 3: Commit Your Changes

```bash
# Check what changed
git status

# Add the file to staging
git add INTRODUCTION.md

# Commit with a descriptive message
git commit -m "Add introduction file for PR practice"

# View your commit
git log --oneline -1
```

### Step 4: Push to GitHub

```bash
# Push the branch to your remote repo
git push -u origin feature/add-introduction
```

### Step 5: Open a Pull Request

**On GitHub.com:**

1. Go to: `https://github.com/yaghta/github-learning-lab`
2. You should see a banner: *"feature/add-introduction had recent pushes"*
3. Click **"Compare & pull request"**
4. Fill in the PR details:
   - **Title:** `Add introduction file for PR practice`
   - **Description:** 
     ```
     ## What this PR does
     - Adds INTRODUCTION.md to practice the PR workflow
     
     ## Why
     Learning pull requests, code review, and collaboration
     
     ## Checklist
     - [ ] Code follows project guidelines
     - [ ] Self-reviewed my code
     - [ ] Added tests (not needed for this exercise)
     - [ ] No breaking changes
     ```
5. Click **"Create pull request"**

### Step 6: Review Your PR

Once created, you'll see:
- **Files changed** tab - See your diff
- **Conversation** tab - Discussion thread
- **Checks** tab - CI/CD status (if configured)

### Step 7: Self-Review (Practice!)

Even though it's your own PR, practice reviewing:
1. Click **"Files changed"** tab
2. Review your own code
3. Click the **+** button next to a line to add a comment
4. Write a comment like: *"Good start! Consider adding more details about goals."*
5. Click **"Start a review"** → **"Finish review"** → **"Approve"**

### Step 8: Merge Your PR

Once you're comfortable:
1. Click **"Merge pull request"**
2. Confirm with **"Confirm merge"**
3. Delete the branch (GitHub will offer this)

```bash
# Back in your terminal, sync with main
git checkout main
git pull origin main
```

## 🎯 Exercise 1.2: PR with Changes Requested

Let's practice the full cycle including feedback:

### Step 1: Create Another Branch

```bash
git checkout -b feature/update-readme
```

### Step 2: Make a "Buggy" Change

Edit the README.md and add something that needs fixing:

```markdown
# GitHub Learning Lab 🎓

## What You'll Learn (UPDATED!)

### Level 1: Pull Requests (PRs)
- What PRs are and why they matter
- Creating a PR from a branch
- Reviewing and merging PRs
- PR best practices
- **NEW:** Handling merge conflicts
```

```bash
git add README.md
git commit -m "Update README with new section"
git push -u origin feature/update-readme
```

### Step 3: Open Another PR

1. Go to GitHub
2. Create PR: `Update README with new section`
3. **Don't merge yet!**

### Step 4: Request Changes

1. On the PR, go to **"Files changed"**
2. Find your change
3. Add a comment: *"This section should go in a different order. Let's reorganize."*
4. Click **"Start review"** → **"Request changes"** → Submit

### Step 5: Fix the Issue

```bash
# Make the fix
git checkout feature/update-readme

# Edit the file to fix the issue
# (Reorganize the sections as suggested)

git add README.md
git commit -m "Fix README section order as requested"
git push
```

### Step 6: Address Review

1. On the PR, your comment thread will show the new commit
2. Click **"Resolve conversation"** (you're the author)
3. Add a comment: *"Fixed! Thanks for the review."*
4. Request another review (or approve your own)
5. Merge!

## Key Takeaways

✅ **PRs are for collaboration** - Even solo work benefits from review  
✅ **PR descriptions matter** - Explain *why*, not just *what*  
✅ **Review is constructive** - Focus on code, not the coder  
✅ **Iterations are normal** - Multiple rounds of changes are common  
✅ **Merge when ready** - Don't leave PRs hanging!

## Next Up

Ready to level up? Move to **Exercise 2: Forking Workflow** 🚀
