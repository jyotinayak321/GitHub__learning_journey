# 📚 CLASS 6 — GitHub Actions & CI/CD

---

## First — What Problem Does This Solve?

Imagine this happens every day at your job:

> You write code → push to GitHub → manually run tests → manually build → manually deploy → hope nothing broke

This takes **30-40 minutes every single day.** For a team of 10 developers — that's 400 minutes wasted daily.

**GitHub Actions solves this:**

> You push code → **everything happens automatically** → tests run → build happens → deployed to server

You do nothing. GitHub does everything. This is called **CI/CD.**

<img width="1472" height="720" alt="image" src="https://github.com/user-attachments/assets/996adab8-992d-4a7b-8628-65e3fec32b56" />

------

## What is CI and CD? — Simple Definitions

**CI = Continuous Integration**
Every time anyone pushes code — automatically run tests. Find problems immediately, not after 2 weeks.

**CD = Continuous Deployment**
Every time tests pass — automatically deploy to the live server. No manual steps.

---

## How GitHub Actions Works — 3 Things to Know

**Thing 1 — Workflow file**
You create a file called `.github/workflows/main.yml` in your project. GitHub reads this file and follows its instructions.

**Thing 2 — Trigger**
You tell GitHub WHEN to run — "run this when I push to main" or "run when a PR is opened."

**Thing 3 — Jobs and Steps**
Inside the workflow, you define jobs (like "test", "build", "deploy") and each job has steps (individual commands to run).
<img width="1472" height="1124" alt="image" src="https://github.com/user-attachments/assets/b984652b-830a-44ba-8d58-5a5e1ee12a91" />

---Click on every line in the file above — each one will explain itself! 👆

---

## Practical — Set Up CI/CD for Your AI Career Mentor Project

### Where to go:

Open **VS Code** → open your `AI-Career-Mentor` project folder → open terminal with `` Ctrl + ` ``

---

### ✅ STEP 1 — Create the workflow folder

```bash
mkdir -p .github/workflows
```

The `-p` flag creates both folders at once — `.github` and `workflows` inside it.

---

### ✅ STEP 2 — Create your first workflow file

```bash
code .github/workflows/ci.yml
```

This opens a new file in VS Code. Paste this complete workflow:

```yaml
name: CI — AI Career Mentor

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - name: Get the code
        uses: actions/checkout@v3

      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: "3.11"

      - name: Install dependencies
        run: pip install -r requirements.txt

      - name: Run tests
        run: pytest --tb=short

      - name: Check code is importable
        run: python -c "import app; print('App imports OK')"
```

Save the file — `Ctrl + S`

---

### ✅ STEP 3 — Push it to GitHub

```bash
git add .github/
git commit -m "ci: add GitHub Actions workflow for automated testing"
git push
```

Now go to **github.com → your repo → Actions tab**

You will see your workflow running! Yellow circle = running, Green check = passed, Red X = failed.

---

### ✅ STEP 4 — Add GitHub Secrets for your API keys

Your project uses Groq API key, MongoDB URL etc. You cannot put them in the workflow file — that is public!

Instead, store them as GitHub Secrets:

1. Go to your GitHub repo
2. Click **Settings** tab
3. Left sidebar → **Secrets and variables** → **Actions**
4. Click **New repository secret**
5. Add these one by one:

| Secret name | Value |
|---|---|
| `GROQ_API_KEY` | your actual Groq API key |
| `MONGODB_URL` | your MongoDB Atlas connection string |
| `SECRET_KEY` | your FastAPI secret key |

Now use them in your workflow:

```yaml
      - name: Run tests
        env:
          GROQ_API_KEY: ${{ secrets.GROQ_API_KEY }}
          MONGODB_URL: ${{ secrets.MONGODB_URL }}
        run: pytest --tb=short
```

Secrets are hidden — even GitHub cannot see the actual values. Only your workflow can use them. ✅

---

### ✅ STEP 5 — Add a Deploy job (auto-deploy to Render)

Add this AFTER your test job in the same file:

```yaml
  deploy:
    runs-on: ubuntu-latest
    needs: test
    if: github.ref == 'refs/heads/main'

    steps:
      - name: Trigger Render deployment
        run: |
          curl -X POST "${{ secrets.RENDER_DEPLOY_HOOK }}"
```

What does each part mean:

`needs: test` → only run deploy if the test job passed first

`if: github.ref == 'refs/heads/main'` → only deploy when pushing to main, not feature branches

`curl -X POST` → sends a signal to Render saying "please deploy now"

To get the Render deploy hook URL:
1. Go to **render.com** → your service
2. Settings → **Deploy Hook** → copy the URL
3. Add it to GitHub Secrets as `RENDER_DEPLOY_HOOK`

---

### ✅ STEP 6 — Full workflow for your project

Here is the complete production-ready workflow for your AI Career Mentor:

```yaml
name: CI/CD — AI Career Mentor

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
        uses: actions/checkout@v3

      - name: Set up Python 3.11
        uses: actions/setup-python@v4
        with:
          python-version: "3.11"

      - name: Cache pip packages
        uses: actions/cache@v3
        with:
          path: ~/.cache/pip
          key: ${{ runner.os }}-pip-${{ hashFiles('requirements.txt') }}

      - name: Install dependencies
        run: pip install -r requirements.txt

      - name: Run all tests
        env:
          GROQ_API_KEY: ${{ secrets.GROQ_API_KEY }}
          MONGODB_URL: ${{ secrets.MONGODB_URL }}
        run: pytest --tb=short -v

      - name: Verify app starts correctly
        env:
          GROQ_API_KEY: ${{ secrets.GROQ_API_KEY }}
          MONGODB_URL: ${{ secrets.MONGODB_URL }}
        run: python -c "from app.main import app; print('FastAPI app OK')"

  deploy:
    runs-on: ubuntu-latest
    needs: test
    if: github.ref == 'refs/heads/main'

    steps:
      - name: Deploy to Render
        run: |
          curl -X POST "${{ secrets.RENDER_DEPLOY_HOOK }}"
          echo "Deployment triggered successfully!"
```

---

### ✅ STEP 7 — Understand the Status Badges

After your workflow runs — add a badge to your README so everyone can see your CI status:

```markdown
![CI Status](https://github.com/jyotinayak321/ai-career-mentor/actions/workflows/ci.yml/badge.svg)
```

This shows a green badge on your GitHub repo:

`CI — passing ✅`

Recruiters see this and immediately know your project has automated tests. This is a huge plus! 🎯

---

## How to Read Workflow Failures

When your workflow fails — go to Actions tab → click the failed run → click the failed step.

You will see the exact error. Common errors:

| Error | What it means | Fix |
|---|---|---|
| `ModuleNotFoundError` | Package missing in requirements.txt | Add the package |
| `pytest: command not found` | pytest not installed | Add `pytest` to requirements.txt |
| `Connection refused` | MongoDB not reachable | Check MONGODB_URL secret |
| `Permission denied` | Wrong file permissions | Check your file paths |

---Press both buttons above — see what happens when tests pass and when they fail! 👆

<img width="1472" height="518" alt="image" src="https://github.com/user-attachments/assets/68fea82d-f544-463d-a05e-0152a04a0413" />


---

## 🧠 Quick Revision — Class 6

| Concept | What it means |
|---|---|
| CI | Auto-run tests on every push |
| CD | Auto-deploy when tests pass |
| Workflow file | `.github/workflows/ci.yml` |
| Trigger | When the workflow runs — push, PR, schedule |
| Job | A group of steps on one machine |
| Step | One individual command |
| `runs-on` | Fresh Linux machine GitHub provides |
| `uses` | Pre-built action from GitHub Marketplace |
| `run` | Your own bash command |
| Secrets | Hidden API keys stored safely in GitHub |
| `needs` | Run this job only after another job passes |

---

## 💡 Interview Questions — With Answers

**Q: What is the difference between CI and CD?**

CI — Continuous Integration — means automatically running tests every time code is pushed. CD — Continuous Deployment — means automatically deploying to production when tests pass. CI catches bugs early, CD removes manual deployment steps.

**Q: What happens if tests fail in a CI pipeline?**

The pipeline stops at the failing step. The deploy job does not run. The developer gets an email notification. The PR shows a red cross — merge is blocked until the failure is fixed.

**Q: What are GitHub Secrets and why do you use them?**

GitHub Secrets store sensitive values like API keys and passwords safely. They are encrypted and never shown in logs. You reference them in workflow files using `${{ secrets.SECRET_NAME }}` — the actual value is never visible publicly.

**Q: What does `needs: test` do in a workflow?**

It creates a dependency between jobs. The deploy job will only start if the test job completed successfully. This prevents broken code from being deployed.

---

## 🎯 Practice Task — Do This Yourself

1. Go to VS Code → open your `git-practice` project
2. Create `.github/workflows/ci.yml`
3. Write a simple workflow that runs on push to main
4. Add a step that runs `echo "Tests passed!"` (simple start)
5. Push to GitHub → go to Actions tab → watch it run
6. Check the green checkmark ✅
7. Now break it on purpose — `run: exit 1` → push → see it fail ❌
8. Fix it → push again → see it pass ✅

---

