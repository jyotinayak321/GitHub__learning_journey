


# 📚 CLASS 5 — EXTENDED: Everything Else You Must Know

---

# PART 6 — git diff (Deep Dive)

## What is it?

You already know basic `git diff` — but there is much more to it.

`git diff` shows you **exactly what changed** — line by line, file by file.

---

### Where to go:

Open **VS Code** → Terminal → `` Ctrl + ` ``

```bash
cd Desktop/git-practice
```

---

### ✅ STEP 1 — All types of diff

Make a change to a file first:

```bash
echo "new line added" >> README.md
```

**Compare working directory vs last commit:**
```bash
git diff
```

Output:
```diff
- old line
+ new line added
```

Red = deleted, Green = added. Simple!

**Compare staging area vs last commit:**
```bash
git add README.md
git diff --staged
```

**Compare two branches:**
```bash
git diff main feature-rebase-demo
```

**Compare two specific commits:**
```bash
git log --oneline
git diff a1b2c3d e4f5g6h
```

**See only which files changed — not the actual lines:**
```bash
git diff --name-only
git diff --stat
```

Output of --stat:
```
README.md | 1 +
1 file changed, 1 insertion(+)
```

---

# PART 7 — git restore (Undo Without Reset)

## What is it?

`git restore` is the **safest way to undo changes** in your working directory or staging area.

Think of it as a smaller, safer version of reset — for files, not commits.

---

### ✅ STEP 2 — Practice git restore

Make some changes to a file:

```bash
echo "oops wrong change" >> README.md
git status
```

**Undo changes in working directory** — go back to last commit version:

```bash
git restore README.md
cat README.md
```

The "oops wrong change" line is gone! ✅

Now stage a file and then unstage it:

```bash
echo "some work" >> README.md
git add README.md
git status
```

Output:
```
Changes to be committed:
  modified: README.md
```

**Unstage it — remove from staging but keep the change:**

```bash
git restore --staged README.md
git status
```

Output:
```
Changes not staged for commit:
  modified: README.md
```

File is back in working directory — not staged anymore. ✅

**Restore a file from a specific commit:**

```bash
git restore --source HEAD~2 README.md
```

This brings the version of README.md from 2 commits ago — without touching anything else!

---

# PART 8 — git stash (Deep Dive)

## You Know Basic Stash — Now Go Deeper

---

### ✅ STEP 3 — Named stashes

Basic stash does not have a name — hard to remember what is in it.

Always give your stash a name:

```bash
echo "login work in progress" >> app.py
git stash save "login feature WIP"

echo "dashboard work in progress" >> README.md
git stash save "dashboard feature WIP"
```

See all stashes with names:

```bash
git stash list
```

Output:
```
stash@{0}: On main: dashboard feature WIP
stash@{1}: On main: login feature WIP
```

Apply a specific stash by number:

```bash
git stash apply stash@{1}
```

This brings back the login work — without removing it from stash list.

**pop vs apply — know the difference:**

| Command | What it does |
|---|---|
| `git stash pop` | Apply stash AND remove it from list |
| `git stash apply` | Apply stash but KEEP it in list |

Use `apply` when you want to apply the same stash to multiple branches.

---

### ✅ STEP 4 — Stash only specific files

Sometimes you want to stash only one file — not everything:

```bash
echo "file A changes" >> app.py
echo "file B changes" >> README.md

git stash push -m "only app.py" app.py
```

Only `app.py` got stashed — README.md changes are still there!

---

### ✅ STEP 5 — See what is inside a stash

Before applying — check what is in it:

```bash
git stash show stash@{0}
```

Output:
```
app.py | 1 +
1 file changed, 1 insertion(+)
```

See the actual line changes:

```bash
git stash show -p stash@{0}
```

---

### ✅ STEP 6 — Create a branch from stash

This is very useful — you were working on main, realized the work should be on a feature branch:

```bash
echo "new feature work" >> app.py
git stash save "new feature"

git stash branch feature-from-stash stash@{0}
```

Git automatically:
- Creates the branch
- Switches to it
- Applies the stash
- Drops the stash

All in one command! ✅

---

# PART 9 — git log (Deep Dive)

## You Know Basic log — Now Use it Like a Pro

---

### ✅ STEP 7 — Powerful log commands

```bash
git log --oneline
```

This you know. Now try these:

**See visual graph of all branches:**
```bash
git log --oneline --graph --all --decorate
```

Output:
```
* f3a91bc (HEAD -> main) feat: latest work
* e2b80ab fix: bug fix
| * d1c79cd (feature-login) feat: login page
|/
* c9a68bc docs: update README
```

**See commits by a specific person:**
```bash
git log --author="Jyoti"
```

**See commits from last 7 days:**
```bash
git log --since="7 days ago"
git log --since="2024-01-01"
git log --after="yesterday"
```

**Search commits by message keyword:**
```bash
git log --grep="login"
```

This shows all commits whose message contains "login."

**See which files changed in each commit:**
```bash
git log --stat
```

**See the actual changes in each commit:**
```bash
git log -p
```

**See only last 3 commits:**
```bash
git log -3
```

**Custom beautiful format:**
```bash
git log --pretty=format:"%h - %an - %ar - %s"
```

Output:
```
f3a91bc - Jyoti - 2 hours ago - feat: latest work
e2b80ab - Jyoti - 1 day ago - fix: bug fix
```

Where: `%h` = short hash, `%an` = author name, `%ar` = relative time, `%s` = subject

---

### ✅ STEP 8 — Set up a beautiful log alias

Type this long command once and save it as a shortcut:

```bash
git config --global alias.lg "log --oneline --graph --all --decorate"
```

Now just type:

```bash
git lg
```

And you get the beautiful graph view! Professional developers always have aliases like this.

---

# PART 10 — git commit (Deep Dive)

## Writing Commits Like a Senior Developer

---

### ✅ STEP 9 — Amend the last commit

You just committed — then realized you forgot to add a file, or made a typo in the message:

```bash
echo "forgot this file" > forgot.txt
git add forgot.txt
git commit --amend --no-edit
```

`--no-edit` means keep the same commit message — just add the file.

Or change the message too:

```bash
git commit --amend -m "feat: correct and complete message"
```

⚠️ Only do this BEFORE pushing. Never amend a pushed commit.

---

### ✅ STEP 10 — Empty commit

Sometimes you want to trigger a CI/CD pipeline without changing any code:

```bash
git commit --allow-empty -m "ci: trigger deployment"
```

This creates a commit with no file changes — useful for triggering GitHub Actions!

---

### ✅ STEP 11 — Commit message best practices

This is exactly what Google and top companies follow — called **Conventional Commits:**

```
type(scope): short description

longer explanation if needed

footer notes
```

**Types you must know:**

| Type | When to use |
|---|---|
| `feat` | New feature added |
| `fix` | Bug fixed |
| `docs` | Documentation changed |
| `style` | Formatting — no logic change |
| `refactor` | Code restructured — no new feature |
| `test` | Tests added or changed |
| `chore` | Build process, dependencies |
| `perf` | Performance improved |

**Real examples:**
```
feat(auth): add JWT login functionality
fix(api): resolve CORS error on /resume endpoint
docs(readme): add Docker setup instructions
refactor(faiss): optimize vector search query
test(career): add unit tests for skill gap module
chore(deps): upgrade langchain to 0.2.0
```

Notice — these are **exactly how you should commit** on your AI Career Mentor project! 🎯

---

# PART 11 — git blame

## What is it?

`git blame` shows you — for every single line of a file — **who wrote it, when, and in which commit.**

Very useful when you find a bug and want to know who introduced it.

---

### ✅ STEP 12 — Practice git blame

```bash
git blame README.md
```

Output:
```
a3f9c2b (Jyoti  2024-01-15 10:23:41) # My First Git Project
f8a21bc (Jyoti  2024-01-15 11:45:02) GitHub connected successfully!
9c3d2a1 (Jyoti  2024-01-16 09:12:33) Edited directly on GitHub
```

Each line shows: **commit hash → author → date → line content**

**See blame for specific lines only:**
```bash
git blame -L 1,5 README.md
```

This shows blame for lines 1 to 5 only.

In VS Code — install **GitLens extension** and you see blame information directly on every line while coding! Senior developers use this every day.

---

# PART 12 — git bisect (Find the Bug Commit)

## What is it?

Imagine your app was working fine last week — but today it is broken.

You have 50 commits in between — which one broke it?

Checking each commit manually = hours of work.

`git bisect` uses **binary search** — finds the bad commit in just 6-7 steps, even if there are 100 commits!

---

### How Binary Search Works Here:

```
50 commits to check

Step 1: Check commit 25 — good or bad?
  Bad → problem is in commits 1-25

Step 2: Check commit 12 — good or bad?
  Good → problem is in commits 12-25

Step 3: Check commit 18 — good or bad?
  Bad → problem is in commits 12-18

...found in 6 steps instead of 50!
```

---

### ✅ STEP 13 — Practice git bisect

```bash
git bisect start
git bisect bad HEAD
git bisect good a3f9c2b
```

Where `a3f9c2b` = a commit you know was working fine.

Git automatically checks out a commit in the middle and asks — is this good or bad?

You test your app → then tell Git:

```bash
git bisect good
```

Or if the bug exists:

```bash
git bisect bad
```

Keep repeating — Git narrows down automatically.

When Git finds the exact commit that introduced the bug:

```
Output:
e8d2a10 is the first bad commit
fix: update search function
```

Now you know exactly which commit broke things! ✅

When done:

```bash
git bisect reset
```

This brings you back to HEAD.

---

# PART 13 — .gitignore (Deep Dive)

## Advanced Patterns You Must Know

---

### ✅ STEP 14 — Complete .gitignore for your AI Career Mentor project

Open VS Code → create `.gitignore` file → paste this:

```
# Python
__pycache__/
*.py[cod]
*.pyc
*.pyo
.Python
env/
venv/
.venv/
*.egg-info/
dist/
build/

# Environment variables — NEVER commit these!
.env
.env.local
.env.production

# IDE files
.vscode/
.idea/
*.swp

# OS files
.DS_Store
Thumbs.db

# Logs
*.log
logs/

# Database
*.db
*.sqlite3

# Docker
.docker/

# Node (if any frontend)
node_modules/
npm-debug.log

# ML models — use Git LFS instead
*.h5
*.pkl
*.pt
*.pth

# FAISS index files
*.index

# Test coverage
.coverage
htmlcov/

# Jupyter notebooks checkpoints
.ipynb_checkpoints/
```

Commit this file:

```bash
git add .gitignore
git commit -m "chore: add comprehensive gitignore"
```

---

### ✅ STEP 15 — What if you already committed a file you should have ignored?

This happens to everyone! You committed `.env` by mistake.

**Remove it from Git tracking — but keep the file on your computer:**

```bash
git rm --cached .env
```

Add it to .gitignore:

```bash
echo ".env" >> .gitignore
git add .gitignore
git commit -m "fix: remove .env from tracking, add to gitignore"
git push
```

The file is still on your laptop — but Git no longer tracks it. ✅

**If you committed a whole folder by mistake:**

```bash
git rm --cached -r node_modules/
```

---

# PART 14 — git shortlog and git show

## Quick but Useful Commands

---

### ✅ STEP 16

**See contribution count by each person:**

```bash
git shortlog -sn
```

Output:
```
  24  Jyoti
   8  Teammate Name
```

**See everything about one specific commit:**

```bash
git show HEAD
git show a3f9c2b
```

Output shows: commit hash, author, date, message, and every line that changed.

**See a specific file at a specific commit — without checkout:**

```bash
git show HEAD~3:README.md
```

This shows you what README.md looked like 3 commits ago — without changing anything!

---

## 🧠 Complete Revision — Class 5 Full

| Command | What it does |
|---|---|
| `git diff --staged` | See staged changes |
| `git diff branch1 branch2` | Compare two branches |
| `git restore file` | Undo working directory changes |
| `git restore --staged file` | Unstage a file |
| `git stash save "name"` | Named stash |
| `git stash apply stash@{1}` | Apply specific stash |
| `git stash branch name` | Create branch from stash |
| `git log --graph --all` | Visual branch history |
| `git log --grep="keyword"` | Search commits by message |
| `git log --author="name"` | Filter by author |
| `git commit --amend` | Fix last commit |
| `git commit --allow-empty` | Empty commit |
| `git blame file` | See who wrote each line |
| `git bisect start/good/bad` | Find which commit broke things |
| `git rm --cached file` | Stop tracking a file |
| `git show HEAD~2:file` | See old version of file |
| `git shortlog -sn` | Contribution count |

---

## 💡 Top Interview Questions From This Extended Class

**Q: How do you find which commit introduced a bug?**

Use `git bisect`. Mark a known good commit and the current bad state — Git uses binary search to find the exact commit that broke things in just a few steps.

**Q: What does git blame do?**

It shows who wrote each line of a file, when, and in which commit. It helps trace the origin of any piece of code.

**Q: I accidentally committed my .env file — what do I do?**

Run `git rm --cached .env` to stop tracking it, add it to `.gitignore`, commit the change, and push. Also immediately rotate your API keys because they were exposed.

**Q: What is the difference between git stash pop and git stash apply?**

Pop applies the stash and removes it from the stash list. Apply applies it but keeps it in the list — useful when you want to apply the same stash to multiple branches.

---

## 🎯 Final Practice Task for Complete Class 5

Do all of this in VS Code terminal:

1. Use `git log --pretty=format:"%h - %an - %s"` and save it as alias `git lg2`
2. Make a commit with wrong message → amend it
3. Use `git blame` on any file → identify which commit added which line
4. Make a change → stash it with a name → switch branch → apply stash
5. Make 4 commits → use `git log --graph --all` to visualize them
6. Use `git show HEAD~1:README.md` to see old version of README
7. Create proper `.gitignore` for your AI Career Mentor project
8. Use `git diff --stat` to see what changed in your last commit

---
