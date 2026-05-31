# Exercise 2: Forking Workflow 🍴

## What is Forking?

A **fork** is a **copy of a repository** under your own GitHub account. It's how you contribute to projects you don't own!

## When to Fork

✅ Contributing to open source projects  
✅ Working on a team where you don't have write access  
✅ Experimenting without affecting the original  
✅ Building upon someone else's work  

## The Forking Workflow

```
1. Fork the repo on GitHub        → Click "Fork" button
2. Clone YOUR fork locally        → git clone https://github.com/YOUR-USERNAME/repo.git
3. Add "upstream" remote          → git remote add upstream https://github.com/ORIGINAL-OWNER/repo.git
4. Make changes in YOUR fork      → Create branch, commit, push
5. Create PR to ORIGINAL repo     → From your fork → original repo
6. Original owner reviews & merges
7. Sync your fork with upstream   → Pull latest changes
```

## 🎯 Exercise 2.1: Fork Your Own Repo (Practice!)

Let's practice the forking workflow by forking your own repo!

### Step 1: Create a Fork on GitHub

1. Go to: `https://github.com/yaghta/github-learning-lab`
2. Click **"Fork"** (top right)
3. Select your account (`yaghta`) as the destination
4. Click **"Create fork"**

You now have:
- **Upstream:** `https://github.com/yaghta/github-learning-lab` (original)
- **Your fork:** `https://github.com/yaghta/github-learning-lab` (wait, same name?)

*Note: Since it's your own repo, the fork will have the same path. In real scenarios, you'd fork someone else's repo.*

### Step 2: Set Up Your Local Repo for Forking

```bash
cd /home/neo/.openclaw/workspace/github-learning-lab

# Check current remotes
git remote -v
# Should show: origin -> https://github.com/yaghta/github-learning-lab

# Rename origin to "upstream" (the original)
git remote rename origin upstream

# Verify
git remote -v
# Should show: upstream -> https://github.com/yaghta/github-learning-lab

# Add YOUR fork as "origin"
git remote add origin https://github.com/yaghta/github-learning-lab.git

# Verify again
git remote -v
# Should show:
#   origin -> https://github.com/yaghta/github-learning-lab.git (YOUR fork)
#   upstream -> https://github.com/yaghta/github-learning-lab (original)
```

*In a real scenario with another person's repo:*
```bash
# origin = YOUR fork (where you push)
# upstream = ORIGINAL repo (where you sync from)
```

### Step 3: Create a Feature Branch in Your Fork

```bash
# Create a new feature branch
git checkout -b feature/forking-practice

# Make a change
echo "# Forking Practice" > FORKING-PRACTICE.md
echo "" >> FORKING-PRACTICE.md
echo "This file was created in my fork, not the upstream." >> FORKING-PRACTICE.md
echo "" >> FORKING-PRACTICE.md
echo "## What I'm learning:" >> FORKING-PRACTICE.md
echo "- How to fork repos" >> FORKING-PRACTICE.md
echo "- Managing upstream remote" >> FORKING-PRACTICE.md
echo "- Creating PRs to original repo" >> FORKING-PRACTICE.md

# Commit and push to YOUR fork
git add FORKING-PRACTICE.md
git commit -m "Add forking practice file from my fork"
git push -u origin feature/forking-practice
```

### Step 4: Create a PR to the Upstream Repo

1. Go to your fork: `https://github.com/yaghta/github-learning-lab`
2. You should see: *"feature/forking-practice had recent pushes"*
3. Click **"Compare & pull request"**
4. **Important:** Change the base repository!
   - **Base:** `yaghta/github-learning-lab` → Click dropdown → Select `yaghta/github-learning-lab` (the upstream/original)
   - **Head:** `yaghta/github-learning-lab:feature/forking-practice` (your fork's branch)
5. Title: `Add forking practice file from fork`
6. Description:
   ```markdown
   ## What this PR does
   - Adds FORKING-PRACTICE.md to demonstrate forking workflow
   
   ## Workflow used
   1. Forked the repo
   2. Cloned my fork locally
   3. Added upstream remote
   4. Created feature branch
   5. Pushed to my fork
   6. Opening PR to upstream
   
   ## Learning goals
   Practicing the forking workflow for open source contributions
   ```
7. Click **"Create pull request"**

### Step 5: Review and Merge

1. Review your PR (just like Exercise 1)
2. Approve and merge
3. Delete the branch

## 🎯 Exercise 2.2: Syncing Your Fork with Upstream

This is crucial! When the upstream repo changes, you need to sync your fork.

### Step 1: Simulate Upstream Changes

Let's pretend the upstream repo got updated. We'll add a file directly to main on GitHub:

1. Go to: `https://github.com/yaghta/github-learning-lab`
2. Click **"Add file"** → **"Create new file"**
3. Name it: `UPSTREAM-UPDATE.md`
4. Add content:
   ```markdown
   # Upstream Update
   
   This file was added directly to the upstream repo.
   
   Your fork is now OUT OF DATE! ⚠️
   ```
5. Commit directly to `main`

### Step 2: Check Your Fork's Status

1. Go to your fork: `https://github.com/yaghta/github-learning-lab`
2. You should see: *"This branch is 1 commit behind yaghta/github-learning-lab"*

### Step 3: Sync Your Fork (GitHub UI)

1. On your fork's main page
2. Click **"Sync fork"**
3. Click **"Update branch"**
4. Your fork is now up to date!

### Step 4: Sync Your Fork (Command Line)

```bash
cd /home/neo/.openclaw/workspace/github-learning-lab

# Make sure you're on main
git checkout main

# Fetch from upstream
git fetch upstream

# Merge upstream changes into your local main
git merge upstream/main

# Push to YOUR fork's main
git push origin main

# Verify
git log --oneline -3
# Should show the UPSTREAM-UPDATE.md commit
```

## 🎯 Exercise 2.3: Real-World Forking Scenario

Let's practice contributing to an actual open source project!

### Option A: Fork a Small Test Repo

1. Find a practice repo like: `https://github.com/firstcontributions/first-contributions`
2. Fork it
3. Clone YOUR fork
4. Add upstream: `git remote add upstream https://github.com/firstcontributions/first-contributions.git`
5. Follow their contribution guide

### Option B: Fork Your Own Repo (Advanced)

Create a more complex scenario:

```bash
# In your local repo
git checkout -b feature/advanced-forking

# Create a multi-file feature
mkdir -p docs/contributing
echo "# Contributing Guide" > docs/contributing/README.md
echo "" >> docs/contributing/README.md
echo "## How to Contribute" >> docs/contributing/README.md
echo "1. Fork the repo" >> docs/contributing/README.md
echo "2. Create a branch" >> docs/contributing/README.md
echo "3. Make your changes" >> docs/contributing/README.md
echo "4. Submit a PR" >> docs/contributing/README.md

git add docs/contributing/README.md
git commit -m "Add contributing guide"
git push -u origin feature/advanced-forking
```

Then create a PR from your fork to upstream (same as Exercise 2.1).

## Key Commands Cheat Sheet

```bash
# Setup (one-time)
git remote rename origin upstream
git remote add origin https://github.com/YOUR-USERNAME/repo.git

# Sync fork
git fetch upstream
git checkout main
git merge upstream/main
git push origin main

# Create feature branch
git checkout -b feature/my-feature
# Make changes...
git add .
git commit -m "My changes"
git push -u origin feature/my-feature

# Create PR on GitHub from your fork to upstream
```

## Key Takeaways

✅ **Fork = Your copy** of someone else's repo  
✅ **Upstream = Original repo** you sync from  
✅ **Origin = Your fork** you push to  
✅ **Always sync before starting** new work  
✅ **PR from fork to upstream**, not the other way  
✅ **Forking is how open source works!**

## Real-World Example

When you want to contribute to `facebook/react`:

```bash
# 1. Fork on GitHub: facebook/react → your-username/react

# 2. Clone YOUR fork
git clone https://github.com/your-username/react.git

# 3. Add upstream
cd react
git remote add upstream https://github.com/facebook/react.git

# 4. Sync and create branch
git fetch upstream
git checkout main
git merge upstream/main
git checkout -b fix/broken-button

# 5. Make changes, commit, push to YOUR fork
# 6. Open PR from your-username/react → facebook/react
```

## Next Up

Ready for the final level? Move to **Exercise 3: Collaboration Patterns** 🚀
