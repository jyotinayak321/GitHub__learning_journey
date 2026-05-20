# 📚 CLASS 7 — Git Internals & Pro Tools

## Why Learn This?

Most developers use Git like a black box — they type commands and hope it works.

But when something goes wrong — deleted commits, corrupted repo, mystery bugs — **only the developer who understands internals can fix it.**

This is also what **senior developer and Google interviews** ask:

> "How does Git store data internally?"
> "What is a blob object?"
> "How does HEAD work?"

After this class — you will answer everything confidently. 💪

---Click on any box above to learn about it! 👆

---

# PART 1 — Git Objects: How Git Actually Stores Data

## The Most Important Concept in Git Internals

Git does NOT store file differences like most people think.

> Git stores **complete snapshots** — like photographs. Every commit = a photograph of your entire project at that moment.

Git has exactly **4 types of objects.** Everything in Git is one of these four:

<img width="1472" height="760" alt="image" src="https://github.com/user-attachments/assets/e40306a5-ef04-4ce9-8f7f-bd0f26e1644a" />


---### The 4 Object Types — Simple English

**1. Blob — file content storage**

> Blob = the actual content of a file. Just pure content — no filename, no path, nothing else.

Two files with identical content = one blob (Git is smart about deduplication!)

**2. Tree — folder structure**

> Tree = a directory listing. It maps filenames to blobs and other trees.

Like a table of contents: "app.py → blob a1b2c3, src/ → tree f9e8d7"

**3. Commit — the snapshot**

> Commit = points to a tree + stores author, message, date, and parent commit.

This is what you create when you run `git commit`.

**4. Tag — named bookmark**

> Tag = a human-readable name pointing to a specific commit. Like "v1.0" → commit e8d2a1.

---

### Practical — See Git Objects With Your Own Eyes

Open **VS Code terminal:**

```bash
cd Desktop/git-practice
```

**See all objects Git has stored:**

```bash
find .git/objects -type f
```

Output:
```
.git/objects/a3/f9c2b4d8e1a6c7b3d9e4f8a2c1b5d7e9f3a6
.git/objects/e8/d2a10b3c4f5e6d7a8b9c0d1e2f3a4b5c6d7
.git/objects/pack/pack-abc123.idx
```

Each file = one Git object. The first 2 characters of the hash = folder name.

**Look inside a specific object:**

```bash
git cat-file -t a3f9c2b
```

Output:
```
commit
```

`-t` flag = show the type of object.

```bash
git cat-file -p a3f9c2b
```

Output:
```
tree e8d2a10b3c4f5e6d7a8b9c0d1
parent f7b3c9d2e1a4b5c6d7e8f9a0b
author Jyoti <email> 1705312800 +0530
committer Jyoti <email> 1705312800 +0530

feat: add resume analyzer function
```

`-p` flag = show the content of the object.

**Now look at the tree object inside:**

```bash
git cat-file -p e8d2a10
```

Output:
```
100644 blob a1b2c3d4e5f6a7b8c9d0e1f2  app.py
100644 blob f9e8d7c6b5a4f3e2d1c0b9a8  resume.py
100644 blob c3d4e5f6a7b8c9d0e1f2a3b4  README.md
```

You are literally reading Git's internal database! ✅

---

# PART 2 — The HEAD File

## What is HEAD?

HEAD is a simple text file — `.git/HEAD`

It always tells Git: **"where are you right now?"**

---

### Practical — Read HEAD Yourself

```bash
cat .git/HEAD
```

Output when on main branch:
```
ref: refs/heads/main
```

This means: "I am on main branch."

**Check what main branch points to:**

```bash
cat .git/refs/heads/main
```

Output:
```
f3a91bc2d4e5f6a7b8c9d0e1f2a3b4c5d6e7f8a9
```

This is the SHA-1 hash of the **latest commit on main.**

So the chain is:

```
HEAD → refs/heads/main → commit hash → tree → blobs
```

**Detached HEAD — what does it mean?**

When you checkout a specific commit directly:

```bash
git checkout a3f9c2b
cat .git/HEAD
```

Output:
```
a3f9c2b4d8e1a6c7b3d9e4f8a2c1b5d7e9f3a6c
```

Instead of pointing to a branch — HEAD now points directly to a commit.

This is **detached HEAD** — you are floating in history with no branch.

Fix it by creating a branch:

```bash
git checkout -b my-rescue-branch
```

---

# PART 3 — git bisect: Find the Bug in Minutes

## What is the Problem?

Your app was working perfectly on Monday.
Today is Friday — it is broken.
You have 80 commits in between.
Which one introduced the bug?

Checking one by one = hours of work.

`git bisect` uses **binary search** — finds the bad commit in 7 steps even with 100 commits!

<img width="1472" height="608" alt="image" src="https://github.com/user-attachments/assets/a45208b0-40eb-46d3-b31b-fd2198564225" />

---### Practical — Use git bisect

```bash
cd Desktop/git-practice
```

**Step 1 — Start bisect:**

```bash
git bisect start
```

**Step 2 — Tell Git: current state is bad:**

```bash
git bisect bad HEAD
```

**Step 3 — Tell Git: this old commit was good:**

```bash
git log --oneline
```

Copy any old commit hash that you know was working fine:

```bash
git bisect good a3f9c2b
```

**Output:**
```
Bisecting: 3 revisions left to test after this
[e8d2a10] feat: add skill scoring function
```

Git automatically checked out the middle commit!

**Step 4 — Test if the bug exists here:**

Run your app or test — if bug exists:

```bash
git bisect bad
```

If bug does NOT exist:

```bash
git bisect good
```

Keep doing this — Git narrows down automatically.

**Final output — Git tells you the exact bad commit:**

```
e8d2a10 is the first bad commit
commit e8d2a10
Author: Jyoti
Date: Mon Jan 15

    feat: add skill scoring function
```

**Step 5 — Exit bisect:**

```bash
git bisect reset
```

You are back to HEAD. Now you know exactly which commit introduced the bug! ✅

**Automate bisect with a test script:**

```bash
git bisect start
git bisect bad HEAD
git bisect good a3f9c2b
git bisect run python test_app.py
```

Git runs the test script automatically on each commit — no manual good/bad needed!

---

# PART 4 — git blame: Who Wrote This Line?

## What is it?

`git blame` shows **who wrote each line** of a file — with commit hash, author name, and date.

When you find a bug — blame tells you who introduced it and when.

---

### Practical — Use git blame

```bash
git blame README.md
```

**Output:**

```
a3f9c2b (Jyoti 2024-01-15 10:23:41 +0530 1) # My First Git Project
f8a21bc (Jyoti 2024-01-15 11:45:02 +0530 2) GitHub connected successfully!
9c3d2a1 (Jyoti 2024-01-16 09:12:33 +0530 3) Edited directly on GitHub
```

Format: `hash (author date line-number) content`

**Blame specific lines only:**

```bash
git blame -L 5,15 app.py
```

Shows blame for lines 5 to 15 only.

**Ignore whitespace changes:**

```bash
git blame -w app.py
```

**Show the original commit even if file was renamed:**

```bash
git blame -C app.py
```

**In VS Code — install GitLens extension:**

After installing GitLens — every line in your editor shows the blame information right next to the code!

You can see: "Jyoti, 2 days ago, feat: add resume analyzer" — on every single line while you code.

---

# PART 5 — git hooks: Automate Your Workflow

## What are Hooks?

Hooks are **scripts that Git runs automatically** at specific moments.

Think of them like event listeners — "when this event happens, run this script."

<img width="1472" height="640" alt="image" src="https://github.com/user-attachments/assets/7e51fccf-cfe1-42b9-bed4-46390b266204" />


---### Practical — Create Your First Hook

Hooks live in `.git/hooks/` folder.

**Step 1 — See available hook templates:**

```bash
ls .git/hooks/
```

Output:
```
pre-commit.sample
commit-msg.sample
pre-push.sample
post-commit.sample
...
```

These `.sample` files show you examples — remove `.sample` to activate them.

---

**Step 2 — Create a pre-commit hook**

This runs every time before you commit — perfect for running tests automatically:

```bash
code .git/hooks/pre-commit
```

Paste this:

```bash
#!/bin/bash

echo "Running pre-commit checks..."

# Check if Python files have syntax errors
python -m py_compile app.py
if [ $? -ne 0 ]; then
  echo "Python syntax error found! Fix before committing."
  exit 1
fi

echo "All checks passed! Committing..."
exit 0
```

Make it executable:

```bash
chmod +x .git/hooks/pre-commit
```

Now test it — try to commit:

```bash
git add .
git commit -m "test: testing pre-commit hook"
```

Output:
```
Running pre-commit checks...
All checks passed! Committing...
[main a3b4c5] test: testing pre-commit hook
```

If your Python file has errors — the commit is **blocked!** ✅

---

**Step 3 — Create a commit-msg hook**

This validates your commit message format — enforces conventional commits:

```bash
code .git/hooks/commit-msg
```

Paste this:

```bash
#!/bin/bash

commit_message=$(cat "$1")

# Check if message follows conventional commits format
if ! echo "$commit_message" | grep -qE "^(feat|fix|docs|style|refactor|test|chore|perf)(\(.+\))?: .{1,72}"; then
  echo ""
  echo "ERROR: Bad commit message format!"
  echo "Use: type(scope): description"
  echo "Example: feat(auth): add JWT login"
  echo "Types: feat fix docs style refactor test chore perf"
  echo ""
  exit 1
fi

exit 0
```

Make it executable:

```bash
chmod +x .git/hooks/commit-msg
```

Now try a bad commit message:

```bash
git commit -m "did some stuff"
```

Output:
```
ERROR: Bad commit message format!
Use: type(scope): description
Example: feat(auth): add JWT login
```

**Commit blocked!** Now try correct format:

```bash
git commit -m "feat: add pre-commit hook for validation"
```

Commit goes through! ✅

---

**Step 4 — Husky: Share hooks with your team**

The problem with `.git/hooks` is it is **not committed** — teammates do not get your hooks.

Husky solves this — hooks are stored in the project and shared with everyone:

```bash
npm init -y
npm install husky --save-dev
npx husky install
```

Create a hook:

```bash
npx husky add .husky/pre-commit "python -m pytest"
```

Now commit `.husky/` folder — every teammate automatically gets the same hooks!

```bash
git add .husky/
git commit -m "chore: add husky pre-commit hook"
```

---

# PART 6 — git worktree: Work on Multiple Branches Simultaneously

## What is the Problem?

You are working on `feature-login` branch.

Suddenly your manager says — "Fix this critical bug on main RIGHT NOW!"

Without worktree — you have to stash your work, switch branches, fix bug, switch back, unstash.

**With worktree** — open main branch in a completely separate folder simultaneously!

---

### Practical — git worktree

```bash
git worktree add ../hotfix-branch main
```

This creates a new folder `hotfix-branch` next to your project — with main branch checked out.

```bash
cd ../hotfix-branch
```

Now you are in main branch — fix the bug here while your feature branch stays untouched in the original folder!

```bash
echo "bug fixed" >> app.py
git add . && git commit -m "fix: critical bug on main"
```

Go back to your feature work:

```bash
cd ../git-practice
```

Your feature work is exactly where you left it — no stashing needed! ✅

**See all worktrees:**

```bash
git worktree list
```

Output:
```
/Users/rajes/Desktop/git-practice       f3a91bc [main]
/Users/rajes/Desktop/hotfix-branch      a1b2c3d [hotfix]
```

**Remove worktree when done:**

```bash
git worktree remove ../hotfix-branch
```

---

# PART 7 — git LFS: Handle Large Files

## What is the Problem?

Git is designed for **code** — small text files.

But your AI projects have:
- Trained ML models — `.h5`, `.pkl` files (100MB - 2GB)
- Datasets — `.csv` (500MB)
- FAISS index files (can be huge)

Pushing these to GitHub = impossible (100MB limit) and makes your repo huge and slow.

**Git LFS (Large File Storage)** stores large files on a separate server — only a tiny pointer is stored in your repo.

---

### Practical — Set Up Git LFS

**Install Git LFS:**

```bash
git lfs install
```

**Tell LFS which file types to track:**

```bash
git lfs track "*.h5"
git lfs track "*.pkl"
git lfs track "*.pt"
git lfs track "*.index"
git lfs track "*.csv"
```

This creates a `.gitattributes` file automatically.

**Commit the configuration:**

```bash
git add .gitattributes
git commit -m "chore: configure Git LFS for ML model files"
```

Now when you add large files — LFS handles them automatically:

```bash
git add model.h5
git commit -m "feat: add trained career mentor model"
git push
```

GitHub shows the file but stores it on LFS servers. Your repo stays fast and small! ✅

**Check LFS status:**

```bash
git lfs status
git lfs ls-files
```

---

# PART 8 — git aliases: Type Less, Do More

## What are Aliases?

Shortcuts for long commands. Type `git lg` instead of `git log --oneline --graph --all --decorate`.

---

### Practical — Set Up Professional Aliases

```bash
git config --global alias.st "status"
git config --global alias.co "checkout"
git config --global alias.br "branch"
git config --global alias.cm "commit -m"
git config --global alias.lg "log --oneline --graph --all --decorate"
git config --global alias.last "log -1 HEAD --stat"
git config --global alias.undo "reset --soft HEAD~1"
git config --global alias.unstage "restore --staged"
git config --global alias.aliases "config --get-regexp alias"
```

Now use them:

```bash
git st          # instead of git status
git co main     # instead of git checkout main
git lg          # beautiful graph history
git last        # see last commit details
git undo        # undo last commit safely
git aliases     # see all your aliases
```

**See all your aliases in one place:**

```bash
git aliases
```

Output:
```
alias.st status
alias.co checkout
alias.lg log --oneline --graph --all --decorate
alias.undo reset --soft HEAD~1
...
```

---

# PART 9 — git show and git describe

## git show — Inspect Anything

```bash
git show HEAD
```

Shows everything about the latest commit — author, date, message, and every line changed.

```bash
git show HEAD~2
```

Shows the commit from 2 commits ago.

```bash
git show HEAD:app.py
```

Shows the content of `app.py` as it was in the last commit — without changing your current file!

```bash
git show v1.0:README.md
```

Shows README.md as it was when you tagged v1.0.

---

## git describe — Human Readable Version

```bash
git describe --tags
```

Output:
```
v1.0-3-gf3a91bc
```

This means:
- `v1.0` — latest tag
- `3` — 3 commits after that tag
- `gf3a91bc` — short commit hash

Very useful for automatic version numbers in CI/CD!

---

# PART 10 — Packfiles and Garbage Collection

## What are Packfiles?

Over time, Git stores thousands of loose object files.

Periodically, Git **compresses them into one packfile** — like zipping all objects together.

This makes your repo smaller and faster.

---

### Practical — Manage Your Repo Size

**See current size:**

```bash
git count-objects -v
```

Output:
```
count: 12
size: 48
in-pack: 156
packs: 1
size-pack: 84
prune-packable: 0
garbage: 0
size-garbage: 0
```

**Run garbage collection manually:**

```bash
git gc
```

Output:
```
Enumerating objects: 164, done.
Counting objects: 100% (164/164), done.
Delta compression using up to 8 threads
Compressing objects: 100% (82/82), done.
```

**Aggressive cleanup:**

```bash
git gc --aggressive --prune=now
```

This deeply optimizes your repo — makes it smaller and faster. Run monthly on large projects.

---

## 🧠 Complete Revision — Class 7

| Command | What it does |
|---|---|
| `git cat-file -t <hash>` | See type of Git object |
| `git cat-file -p <hash>` | See content of Git object |
| `find .git/objects -type f` | See all stored objects |
| `cat .git/HEAD` | See where HEAD points |
| `git bisect start/good/bad` | Binary search for bug commit |
| `git bisect run script` | Automated bisect |
| `git bisect reset` | Exit bisect mode |
| `git blame file` | Who wrote each line |
| `git blame -L 5,15 file` | Blame specific lines |
| `.git/hooks/pre-commit` | Script runs before every commit |
| `.git/hooks/commit-msg` | Script validates commit message |
| `git worktree add ../folder branch` | Open branch in new folder |
| `git worktree list` | See all open worktrees |
| `git lfs install` | Set up large file storage |
| `git lfs track "*.h5"` | Track file type with LFS |
| `git show HEAD:file` | See old version of file |
| `git describe --tags` | Human readable version string |
| `git gc` | Clean and compress repo |
| `git count-objects -v` | See repo size |

---

## 💡 Interview Questions — With Answers

**Q: How does Git store data internally?**

Git stores everything as objects in the `.git/objects` folder. There are four types — blob stores file content, tree stores directory structure, commit stores snapshot metadata and points to a tree, and tag is a named pointer to a commit. Every object is identified by a SHA-1 hash of its content.

**Q: What is the difference between a blob and a tree?**

A blob stores the raw content of a single file — just bytes, no filename. A tree stores a directory listing — it maps filenames to blob hashes and other tree hashes. Together they represent your entire project structure.

**Q: How do you find which commit introduced a bug?**

Use `git bisect`. Mark the current broken state as bad and an old working commit as good. Git uses binary search to find the exact commit that introduced the bug in just log2(n) steps — so 100 commits takes only 7 checks.

**Q: What are Git hooks and where do they live?**

Hooks are scripts in `.git/hooks/` that Git runs automatically at specific events — pre-commit, commit-msg, pre-push, post-receive. They are used to enforce code quality, validate commit messages, or trigger deployments.

**Q: What is Git LFS and when do you use it?**

Git LFS is Large File Storage — it stores large binary files like ML models and datasets on a separate server, keeping only a small pointer in the Git repo. Use it when files exceed a few megabytes — especially trained models, datasets, and media files.

---

## 🎯 Practice Task — Do All of This

Go to VS Code terminal and complete every step:

1. Run `find .git/objects -type f` — see your stored objects
2. Run `git cat-file -t` and `git cat-file -p` on 3 different hashes
3. Run `cat .git/HEAD` — understand what it shows
4. Create 5 commits → use `git bisect` to find a specific one
5. Run `git blame` on any file — read every column
6. Create a `pre-commit` hook that prints "Checking code..."
7. Create a `commit-msg` hook that rejects messages shorter than 10 characters
8. Set up 5 aliases — `st`, `lg`, `cm`, `undo`, `last`
9. Try `git worktree add` to open a second branch simultaneously
10. Run `git gc` and `git count-objects -v` — compare before and after

---

