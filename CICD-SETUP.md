# CI/CD Setup Instructions

## Problem
GitHub requires special permissions (`workflow` scope) to push workflow files via PAT.

## Solution: Create Workflow via GitHub Web UI

### Step 1: Go to GitHub
Visit: https://github.com/yaghta/github-learning-lab

### Step 2: Create the Workflow File

1. Click **"Add file"** → **"Create new file"**
2. Path: `.github/workflows/ci.yml`
3. Paste the content below
4. Commit message: "Add CI/CD workflow"
5. Click **"Commit changes"**

### Workflow Content

```yaml
name: CI Pipeline

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
      
      - name: Display workflow info
        run: |
          echo "🚀 Running CI pipeline!"
          echo "Branch: ${{ github.ref }}"
          echo "Event: ${{ github.event_name }}"
          echo "Commit: ${{ github.sha }}"
      
      - name: Check if README exists
        run: |
          if [ -f README.md ]; then
            echo "✅ README.md exists"
          else
            echo "❌ README.md missing!"
            exit 1
          fi
      
      - name: Check exercise structure
        run: |
          echo "📁 Checking exercise structure..."
          for dir in exercises/01-* exercises/02-* exercises/03-* exercises/04-*; do
            if [ -d "$dir" ]; then
              echo "✅ Found: $dir"
            else
              echo "⚠️  Missing: $dir"
            fi
          done

  test:
    runs-on: ubuntu-latest
    needs: lint
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
      
      - name: Run tests
        run: |
          echo "🧪 Running tests..."
          
          # Check all required files
          files=(
            "README.md"
            "exercises/01-pull-request-basics/README.md"
            "exercises/02-forking-workflow/README.md"
            "exercises/03-collaboration-patterns/README.md"
            "exercises/04-cicd-github-actions/README.md"
          )
          
          for file in "${files[@]}"; do
            if [ -f "$file" ]; then
              echo "✅ $file exists"
            else
              echo "❌ $file missing!"
              exit 1
            fi
          done
          
          echo "✅ All tests passed!"

  build:
    runs-on: ubuntu-latest
    needs: test
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
      
      - name: Build project
        run: |
          echo "🔨 Building project..."
          echo "✅ Build successful!"
```

### Step 3: Verify It Works

1. Go to the **"Actions"** tab
2. You should see "CI Pipeline" running
3. Click on it to see the jobs
4. Watch it complete! 🎉

### Step 4: Test on a PR

Create a test PR to see CI run:

```bash
git checkout -b feature/test-ci-workflow
echo "# Test" > TEST-CI.md
git add TEST-CI.md
git commit -m "Test CI workflow"
git push -u origin feature/test-ci-workflow
```

Then create a PR on GitHub and watch the checks run!

---

## Why This Happens

GitHub requires the `workflow` scope for PATs that modify `.github/workflows/` files for security reasons. This prevents malicious automation from creating workflows that could exfiltrate data.

### Options to Fix Permanently

1. **Use GitHub Web UI** (easiest) - Create workflow files manually
2. **Update PAT with workflow scope** - Generate new token with `workflow` scope
3. **Use GitHub CLI** - `gh` tool handles auth automatically
4. **Use SSH** - SSH keys don't have this limitation

### Generate New PAT with Workflow Scope

If you want to push workflow files via command line:

1. Go to: https://github.com/settings/tokens
2. Click **"Generate new token (classic)"**
3. Select scopes:
   - ✅ `repo` (Full control of private repositories)
   - ✅ `workflow` (Update GitHub Action workflows)
4. Generate token
5. Update your git remote with new token

---

## Quick Test

After creating the workflow via web UI, try this:

```bash
# Create a test change
git checkout -b feature/ci-test
echo "Test change" >> CI-TEST.md
git add CI-TEST.md
git commit -m "Test CI trigger"
git push -u origin feature/ci-test
```

Then create a PR on GitHub and watch the Actions tab! 🚀
