# Exercise 4: CI/CD with GitHub Actions 🔄

## What is CI/CD?

**CI/CD = Continuous Integration / Continuous Deployment**

- **CI (Continuous Integration)**: Automatically test your code on every change
- **CD (Continuous Deployment)**: Automatically deploy your code when it passes tests

Think of it as having a **robot assistant** that:
1. ✅ Runs your tests on every PR
2. ✅ Checks code quality
3. ✅ Builds your project
4. ✅ Deploys when everything passes

## Why You Need CI/CD

| Without CI/CD | With CI/CD |
|---------------|------------|
| Manual testing on every PR | Automatic tests on every PR |
| "It works on my machine!" | Consistent test environment |
| Bugs reach production | Bugs caught before merge |
| Manual deployment | Automatic deployment |
| Human error | Reliable automation |

## GitHub Actions Basics

### Key Concepts

```
Workflow    = A complete automated process (defined in .github/workflows/)
Job         = A set of steps that run on the same runner
Step        = Individual action (run command, use action, etc.)
Action      = Reusable unit of code (like a function)
Runner      = Server that executes your workflow
Trigger     = Event that starts a workflow (push, PR, schedule, etc.)
```

### Workflow File Structure

```yaml
name: Workflow Name

on:                    # When to run
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  job-name:
    runs-on: ubuntu-latest    # Type of runner
    
    steps:
      - name: Step name
        uses: action-name@version    # Use pre-built action
        # OR
        run: command-here            # Run shell command
```

---

## 🎯 Exercise 4.1: Your First CI Workflow

Let's create a simple workflow that runs on every PR!

### Step 1: Create the Workflow Directory

```bash
cd /home/neo/.openclaw/workspace/github-learning-lab

mkdir -p .github/workflows
```

### Step 2: Create a Basic CI Workflow

Create `.github/workflows/ci.yml`:

```yaml
name: CI Pipeline

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
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
```

### Step 3: Commit and Push

```bash
git add .github/workflows/ci.yml
git commit -m "Add basic CI workflow"
git push origin main
```

### Step 4: Watch It Run

1. Go to your repo on GitHub
2. Click **"Actions"** tab
3. You should see your workflow running!
4. Click on it to see the logs

### Step 5: Trigger on PR

Create a test PR to see CI run:

```bash
git checkout -b feature/test-ci
echo "# Test" > TEST.md
git add TEST.md
git commit -m "Test CI trigger"
git push -u origin feature/test-ci
```

Then create a PR on GitHub and watch the CI check run!

---

## 🎯 Exercise 4.2: Multi-Job Workflow

Let's create a more realistic workflow with multiple jobs:

```yaml
name: Full CI/CD Pipeline

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  # Job 1: Lint Code
  lint:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      
      - name: Check file structure
        run: |
          echo "🔍 Checking file structure..."
          ls -la
          echo "✅ Structure check passed"
  
  # Job 2: Run Tests
  test:
    runs-on: ubuntu-latest
    needs: lint  # Wait for lint to complete
    
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      
      - name: Run tests
        run: |
          echo "🧪 Running tests..."
          # Simulate tests
          sleep 2
          echo "✅ All tests passed!"
  
  # Job 3: Build (runs after test)
  build:
    runs-on: ubuntu-latest
    needs: test  # Wait for test to complete
    
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      
      - name: Build project
        run: |
          echo "🔨 Building project..."
          sleep 1
          echo "✅ Build successful!"
```

**Key Points:**
- `needs: lint` - Job depends on another job completing first
- Jobs run in **parallel** by default
- Use `needs` to create **dependencies**

---

## 🎯 Exercise 4.3: Matrix Builds

Test your code on multiple environments simultaneously:

```yaml
name: Matrix Build

on:
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ${{ matrix.os }}
    strategy:
      matrix:
        os: [ubuntu-latest, macos-latest, windows-latest]
        node: [18, 20, 22]
    
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node }}
      
      - name: Run tests
        run: |
          echo "Testing on ${{ matrix.os }} with Node ${{ matrix.node }}"
          # Your actual test commands here
          echo "✅ Passed on ${{ matrix.os }} Node ${{ matrix.node }}"
```

**This runs 9 jobs in parallel!** (3 OS × 3 Node versions)

---

## 🎯 Exercise 4.4: Status Checks in PRs

CI/CD becomes powerful when integrated with Pull Requests!

### Set Up Branch Protection with Required Checks

1. Go to **Settings** → **Branches** → **Add rule**
2. Branch name pattern: `main`
3. Check ✅ **Require status checks to pass**
4. Under "Status checks that are required", add:
   - `CI Pipeline/test`
   - `CI Pipeline/lint`
5. Check ✅ **Require branches to be up to date before merging**
6. Click **Create**

### What This Does

Now when you create a PR:
- ✅ GitHub shows checkmarks ✗❌ for each CI job
- ✅ You **cannot merge** until all checks pass
- ✅ Protects main from broken code

### Visual in PR

```
✓ CI Pipeline / lint (ubuntu-latest)
✓ CI Pipeline / test (ubuntu-latest)
✓ CI Pipeline / build (ubuntu-latest)

This branch has no conflicts with the base branch
Merge is allowed when all checks pass ✅
```

---

## 🎯 Exercise 4.5: Common CI/CD Patterns

### Pattern 1: Node.js Project

```yaml
name: Node.js CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Run linter
        run: npm run lint
      
      - name: Run tests
        run: npm test
      
      - name: Run build
        run: npm run build
```

### Pattern 2: Python Project

```yaml
name: Python CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        python-version: ['3.9', '3.10', '3.11']
    
    steps:
      - uses: actions/checkout@v4
      
      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: ${{ matrix.python-version }}
      
      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install -r requirements.txt
          pip install pytest
      
      - name: Run tests
        run: pytest
```

### Pattern 3: Docker Build

```yaml
name: Docker Build

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v4
      
      - name: Build Docker image
        run: docker build -t myapp:${{ github.sha }} .
      
      - name: Run tests
        run: docker run --rm myapp:${{ github.sha }} npm test
```

---

## 🎯 Exercise 4.6: Deploy on Merge (CD)

Automatically deploy when code merges to main:

```yaml
name: Deploy to Production

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v4
      
      - name: Deploy
        run: |
          echo "🚀 Deploying to production..."
          # Your deployment commands here
          # Example for Vercel:
          # npm run deploy
          # Example for AWS:
          # aws s3 sync ./dist s3://my-bucket
          echo "✅ Deployment complete!"
```

**⚠️ Security Note:** For real deployments, use **GitHub Secrets** for credentials:

```yaml
      - name: Deploy
        env:
          API_KEY: ${{ secrets.DEPLOY_API_KEY }}
        run: |
          echo "Deploying with API key..."
          # Use $API_KEY in your deployment
```

Then add the secret in **Settings** → **Secrets and variables** → **Actions** → **New repository secret**

---

## 🎯 Exercise 4.7: Conditional Workflows

Run workflows based on specific conditions:

```yaml
name: Conditional CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  # Only run on PRs from certain branches
  pr-check:
    if: github.event_name == 'pull_request'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: echo "Running PR checks..."
  
  # Only run on main branch pushes
  deploy:
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: echo "Deploying to production..."
  
  # Skip if only docs changed
  test:
    if: "!contains(github.event.head_commit.message, '[skip ci]')"
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: echo "Running tests..."
```

---

## 🎯 Exercise 4.8: Workflow Commands & Annotations

Make CI output more informative:

```yaml
name: Advanced CI

on:
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v4
      
      - name: Run tests with annotations
        run: |
          echo "::group::Setup"
          echo "Setting up environment..."
          echo "::endgroup::"
          
          echo "::group::Tests"
          echo "::warning::This is a warning message"
          echo "::error::This is an error message"
          echo "Running actual tests..."
          echo "::endgroup::"
          
          echo "::notice::Tests completed successfully!"
```

**GitHub Actions Commands:**
- `::group::` / `::endgroup::` - Collapsible sections
- `::warning::` - Shows ⚠️ in logs
- `::error::` - Shows ❌ in logs
- `::notice::` - Shows ℹ️ in logs

---

## 🎯 Practice Exercise: Build Your Own CI

Create a workflow that:
1. ✅ Runs on every PR to main
2. ✅ Checks that required files exist (README.md, LICENSE, etc.)
3. ✅ Validates YAML files in `.github/workflows/`
4. ✅ Runs a simple "test" (echo commands)
5. ✅ Fails if any check fails

**Starter template:**

```yaml
name: File Validation

on:
  pull_request:
    branches: [main]

jobs:
  validate:
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v4
      
      - name: Check required files
        run: |
          echo "🔍 Validating repository structure..."
          
          # Check README
          if [ ! -f README.md ]; then
            echo "::error::README.md is missing!"
            exit 1
          fi
          echo "✅ README.md found"
          
          # Check LICENSE
          if [ ! -f LICENSE ]; then
            echo "::error::LICENSE is missing!"
            exit 1
          fi
          echo "✅ LICENSE found"
          
          echo "✅ All required files present!"
```

---

## Key Takeaways

✅ **CI/CD automates testing and deployment** - No more manual checks!  
✅ **GitHub Actions uses YAML workflows** - Define in `.github/workflows/`  
✅ **Jobs can depend on each other** - Use `needs` for sequencing  
✅ **Status checks protect branches** - Require CI to pass before merge  
✅ **Matrix builds test multiple envs** - Parallel testing across OS/versions  
✅ **Use secrets for credentials** - Never hardcode API keys  
✅ **Conditional workflows save time** - Only run when needed  

---

## Next Steps

1. **Add CI to your learning lab** - Create a real workflow file
2. **Explore GitHub Marketplace** - 1000s of pre-built actions
3. **Learn GitHub Actions CLI** - Test workflows locally
4. **Set up required status checks** - Protect your main branch
5. **Try deployment workflows** - Automate your deployments

## Resources

- [GitHub Actions Docs](https://docs.github.com/en/actions)
- [GitHub Marketplace](https://github.com/marketplace?type=actions)
- [Awesome Actions](https://github.com/sdras/awesome-actions)
- [GitHub Actions Examples](https://github.com/actions/workflows)

---

**Ready to practice?** Create your first workflow and watch GitHub Actions work its magic! 🎊
