PART 1 — Multiple Jobs: Parallel vs Sequential
What is the difference?
By default, jobs run in parallel — at the same time, on different machines.
But sometimes you want them to run one after another — like tests must pass before deploy runs.

<img width="1472" height="680" alt="image" src="https://github.com/user-attachments/assets/6f6a1f35-99e6-476e-8bbc-1ef2617ace89" />


  ::view-transition-group(*),
  ::view-transition-old(*),
  ::view-transition-new(*) {
    animation-duration: 0.25s;
    animation-timing-function: cubic-bezier(0.19, 1, 0.22, 1);
  }
VvisualizeVvisualize show_widgetPractical — Multiple Jobs in YAML
Parallel jobs — no dependency:
yamljobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - run: pip install flake8
      - run: flake8 app/

  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - run: pip install -r requirements.txt
      - run: pytest

  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - run: docker build -t my-app .
All three start at the same time — saves time!
Sequential jobs — using needs:
yamljobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - run: pytest

  build:
    runs-on: ubuntu-latest
    needs: test          # only runs if test passes
    steps:
      - uses: actions/checkout@v3
      - run: docker build -t my-app .

  deploy:
    runs-on: ubuntu-latest
    needs: build         # only runs if build passes
    steps:
      - run: echo "Deploying to production!"

PART 2 — Matrix Builds
What is it?
Test your code on multiple Python versions or operating systems — all at the same time!
Very useful because your users may use Python 3.9, 3.10, or 3.11 — you want to make sure your app works on all of them.

Practical — Matrix Strategy
yamljobs:
  test:
    runs-on: ubuntu-latest

    strategy:
      matrix:
        python-version: ["3.9", "3.10", "3.11"]

    steps:
      - uses: actions/checkout@v3

      - name: Set up Python ${{ matrix.python-version }}
        uses: actions/setup-python@v4
        with:
          python-version: ${{ matrix.python-version }}

      - run: pip install -r requirements.txt
      - run: pytest
What happens:
GitHub creates 3 separate machines and runs the job on each:

Machine 1 → Python 3.9
Machine 2 → Python 3.10
Machine 3 → Python 3.11

All run at the same time! You get 3 results in your Actions tab.
Matrix with multiple dimensions — OS and Python:
yaml    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest]
        python-version: ["3.10", "3.11"]
This creates 4 combinations:

Ubuntu + Python 3.10
Ubuntu + Python 3.11
Windows + Python 3.10
Windows + Python 3.11

Exclude a specific combination:
yaml    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest]
        python-version: ["3.10", "3.11"]
        exclude:
          - os: windows-latest
            python-version: "3.10"
Continue even if one matrix job fails:
yaml    strategy:
      fail-fast: false    # don't cancel others if one fails
      matrix:
        python-version: ["3.9", "3.10", "3.11"]

PART 3 — Caching: Speed Up Your Workflows
What is it?
Every time your workflow runs — it downloads and installs all packages from scratch.
Installing 50 packages takes 2-3 minutes every single run.
Caching saves the downloaded packages — so next time, installation takes 10 seconds instead of 3 minutes!

Practical — Cache pip packages
yaml    steps:
      - uses: actions/checkout@v3

      - uses: actions/setup-python@v4
        with:
          python-version: "3.11"

      - name: Cache pip packages
        uses: actions/cache@v3
        with:
          path: ~/.cache/pip
          key: ${{ runner.os }}-pip-${{ hashFiles('requirements.txt') }}
          restore-keys: |
            ${{ runner.os }}-pip-

      - name: Install dependencies
        run: pip install -r requirements.txt
How the key works:
key: ubuntu-pip-abc123def456
Where abc123def456 = hash of your requirements.txt file.
If requirements.txt changes → hash changes → new key → fresh install.
If requirements.txt did not change → same key → uses cached packages → super fast!
Cache for Node.js (if you have React frontend):
yaml      - name: Cache node modules
        uses: actions/cache@v3
        with:
          path: ~/.npm
          key: ${{ runner.os }}-node-${{ hashFiles('package-lock.json') }}

PART 4 — More Triggers: Schedule and Manual
You already know push and pull_request.
Now learn the other two important triggers:

Trigger 1 — Schedule (Cron)
Run your workflow automatically at a specific time — like a cron job.
yamlon:
  schedule:
    - cron: "0 9 * * 1-5"    # 9 AM every weekday
How to read cron format:
┌─── minute (0-59)
│  ┌── hour (0-23)
│  │  ┌─ day of month (1-31)
│  │  │  ┌ month (1-12)
│  │  │  │  ┌ day of week (0=Sun, 1=Mon...5=Fri)
│  │  │  │  │
0  9  *  *  1-5
Common examples:
yaml- cron: "0 0 * * *"       # midnight every day
- cron: "0 9 * * 1"       # 9 AM every Monday
- cron: "*/30 * * * *"    # every 30 minutes
- cron: "0 0 1 * *"       # midnight on 1st of every month
Real use case for your AI Career Mentor:
yamlon:
  schedule:
    - cron: "0 2 * * *"    # 2 AM daily

jobs:
  refresh-job-data:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - run: python scripts/refresh_jobs.py
This automatically refreshes job listings every night at 2 AM — without you doing anything!

Trigger 2 — Workflow Dispatch (Manual)
Run your workflow manually with a button click on GitHub — useful for deployments you want to control.
yamlon:
  workflow_dispatch:
    inputs:
      environment:
        description: "Deploy to which environment?"
        required: true
        default: "staging"
        type: choice
        options:
          - staging
          - production
      debug_mode:
        description: "Enable debug mode?"
        type: boolean
        default: false
Now go to GitHub → Actions tab → your workflow → "Run workflow" button appears!
You click it, choose environment, click Run — and the workflow starts manually.
Use the inputs inside the workflow:
yaml      - name: Deploy
        run: |
          echo "Deploying to: ${{ inputs.environment }}"
          echo "Debug mode: ${{ inputs.debug_mode }}"

PART 5 — Environment Variables vs Secrets
People confuse these — learn the difference clearly.
Environment VariablesSecretsVisible in logs?YesNo — always hiddenSensitive data?NoYesExampleAPP_ENV=productionGROQ_API_KEY=gsk_...Where to setworkflow file directlyGitHub Settings → Secrets

Practical — Using Both Together
yamljobs:
  deploy:
    runs-on: ubuntu-latest

    env:
      APP_NAME: ai-career-mentor      # visible — not sensitive
      APP_ENV: production             # visible — not sensitive
      PORT: 8000                      # visible — not sensitive

    steps:
      - uses: actions/checkout@v3

      - name: Run application
        env:
          GROQ_API_KEY: ${{ secrets.GROQ_API_KEY }}      # hidden
          MONGODB_URL: ${{ secrets.MONGODB_URL }}         # hidden
          SECRET_KEY: ${{ secrets.SECRET_KEY }}           # hidden
        run: |
          echo "App: $APP_NAME"
          echo "Env: $APP_ENV"
          python app/main.py
Three levels of env variables:
yamlenv:
  GLOBAL_VAR: "applies to ALL jobs"    # at top level

jobs:
  test:
    env:
      JOB_VAR: "applies to this job"   # at job level

    steps:
      - name: My step
        env:
          STEP_VAR: "only this step"   # at step level
        run: echo $STEP_VAR

PART 6 — Artifacts: Save and Share Files Between Jobs
What is an artifact?
An artifact is a file your workflow produces — like a test report, a built app, a coverage report — that you want to save or share between jobs.

Practical — Upload and Download Artifacts
Job 1 — produce a file and upload it:
yaml  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - run: pip install -r requirements.txt

      - name: Run tests with coverage report
        run: pytest --cov=app --cov-report=html

      - name: Upload coverage report
        uses: actions/upload-artifact@v3
        with:
          name: coverage-report
          path: htmlcov/
          retention-days: 7
Job 2 — download that file and use it:
yaml  deploy:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - name: Download coverage report
        uses: actions/download-artifact@v3
        with:
          name: coverage-report

      - name: Show coverage files
        run: ls -la
Real use cases for artifacts:

Test coverage HTML reports — download and read them
Built React app files — share between build and deploy jobs
Log files from a failed run — download to debug
Generated PDFs or reports


PART 7 — Reusable Workflows
What is it?
If you have 5 different projects — and all need the same test setup — you do not copy the YAML 5 times.
You create one reusable workflow and call it from all 5 projects.

Practical — Create a Reusable Workflow
Step 1 — Create the reusable workflow:
In .github/workflows/reusable-test.yml:
yamlname: Reusable test workflow

on:
  workflow_call:               # this makes it reusable!
    inputs:
      python-version:
        required: true
        type: string
    secrets:
      GROQ_API_KEY:
        required: true

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-python@v4
        with:
          python-version: ${{ inputs.python-version }}
      - run: pip install -r requirements.txt
      - run: pytest
        env:
          GROQ_API_KEY: ${{ secrets.GROQ_API_KEY }}
Step 2 — Call it from another workflow:
In .github/workflows/ci.yml:
yamlname: CI

on: [push]

jobs:
  run-tests:
    uses: ./.github/workflows/reusable-test.yml
    with:
      python-version: "3.11"
    secrets:
      GROQ_API_KEY: ${{ secrets.GROQ_API_KEY }}
One workflow defined once — used everywhere!

PART 8 — Complete Production Workflow
Full real-world CI/CD for your AI Career Mentor project
Copy this complete file into .github/workflows/ci-cd.yml:
yamlname: CI/CD — AI Career Mentor

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]
  schedule:
    - cron: "0 2 * * *"      # health check every night
  workflow_dispatch:
    inputs:
      environment:
        description: "Deploy to?"
        type: choice
        options: [staging, production]
        default: staging

env:
  APP_NAME: ai-career-mentor
  PYTHON_VERSION: "3.11"

jobs:

  lint:
    name: Code quality check
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-python@v4
        with:
          python-version: ${{ env.PYTHON_VERSION }}
      - run: pip install flake8 black
      - name: Check formatting
        run: black --check app/
      - name: Check style
        run: flake8 app/ --max-line-length=100

  test:
    name: Run tests
    runs-on: ubuntu-latest
    needs: lint

    strategy:
      fail-fast: false
      matrix:
        python-version: ["3.10", "3.11"]

    steps:
      - uses: actions/checkout@v3

      - uses: actions/setup-python@v4
        with:
          python-version: ${{ matrix.python-version }}

      - name: Cache pip
        uses: actions/cache@v3
        with:
          path: ~/.cache/pip
          key: ${{ runner.os }}-pip-${{ hashFiles('requirements.txt') }}

      - name: Install dependencies
        run: pip install -r requirements.txt

      - name: Run tests with coverage
        env:
          GROQ_API_KEY: ${{ secrets.GROQ_API_KEY }}
          MONGODB_URL: ${{ secrets.MONGODB_URL }}
          SECRET_KEY: ${{ secrets.SECRET_KEY }}
        run: pytest --cov=app --cov-report=xml --tb=short -v

      - name: Upload coverage report
        uses: actions/upload-artifact@v3
        with:
          name: coverage-py${{ matrix.python-version }}
          path: coverage.xml
          retention-days: 5

  deploy:
    name: Deploy to Render
    runs-on: ubuntu-latest
    needs: test
    if: github.ref == 'refs/heads/main' && github.event_name == 'push'

    steps:
      - name: Trigger deployment
        run: |
          curl -X POST "${{ secrets.RENDER_DEPLOY_HOOK }}"
          echo "Deployed $APP_NAME to production!"

      - name: Post deployment message
        run: |
          echo "Deployment complete!"
          echo "App: $APP_NAME"
          echo "Time: $(date)"
This single file gives you:

Code quality check on every push
Tests on Python 3.10 and 3.11 simultaneously
Coverage reports saved as artifacts
Auto-deploy to Render only when main branch tests pass
Nightly health check at 2 AM
Manual deploy button when needed


🧠 Complete Revision — Class 6 Full
TopicKey pointParallel jobsNo needs — all start together — fasterSequential jobsneeds: jobname — waits for previousMatrix buildsTest multiple versions simultaneouslyfail-fast: falseDo not cancel others if one matrix failsCachingactions/cache@v3 — saves install timeCache keyBased on file hash — auto-refreshes when deps changeSchedule triggerCron syntax — run at specific timeManual triggerworkflow_dispatch — button on GitHubEnv variablesNon-sensitive — write directly in YAMLSecretsSensitive — store in GitHub SettingsArtifactsupload-artifact / download-artifact — share filesReusable workflowworkflow_call — define once, use everywhere

💡 Interview Questions — With Answers
Q: How do you speed up a GitHub Actions workflow?
Three ways — use caching to avoid reinstalling packages every time, run independent jobs in parallel, and use matrix builds wisely so you are not running redundant combinations.
Q: What is the difference between environment variables and secrets in GitHub Actions?
Environment variables are for non-sensitive configuration like app name or port number — they appear in logs. Secrets are for sensitive values like API keys and passwords — they are encrypted and never shown in logs even if you try to print them.
Q: How do you prevent the deploy job from running when tests fail?
Use needs: test on the deploy job. This creates a dependency — deploy only runs if test completed successfully. If test fails, deploy is automatically skipped.
Q: What are artifacts in GitHub Actions?
Artifacts are files produced during a workflow run — like test coverage reports, built application files, or log files. You upload them with actions/upload-artifact and download them with actions/download-artifact. They are stored for a set number of days.

🎯 Final Practice Task — Complete Class 6
Do all of this on your AI Career Mentor project:

Add a lint job using flake8 that runs before tests
Add matrix builds for Python 3.10 and 3.11
Add caching for pip packages
Add a scheduled run at midnight using cron
Add workflow_dispatch with an environment input
Upload test coverage as an artifact — download it from Actions tab
Make sure deploy only runs on main branch pushes
Push everything → go to Actions tab → verify all jobs pass ✅
