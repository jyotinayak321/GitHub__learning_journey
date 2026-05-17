# 📚 CLASS 5 — Advanced Git: Rebase, Reset, Cherry-pick, Tags & Reflog

---

## First — Why This Class Matters

These commands are what **separate a junior developer from a mid/senior developer.**

In your Google or DRDO interview, they will ask:
- "What is the difference between rebase and merge?"
- "How do you undo a committed change safely?"
- "What is cherry-pick and when do you use it?"

After this class — you will answer all of these confidently. 💪
<img width="1472" height="640" alt="image" src="https://github.com/user-attachments/assets/a2823efc-4083-4458-aadf-290149dab25b" />

---

# PART 1 — Git Rebase

## What is Rebase? Simple Explanation First.

Imagine this situation:

You created a branch called `feature-login` from main.
Then your teammate added 2 new commits to main.
Now your branch is **behind** — it does not have your teammate's latest work.

**The problem looks like this:**

```
main:          A → B → C → D → E
                         \
feature-login:            F → G
```

<img width="1472" height="620" alt="image" src="https://github.com/user-attachments/assets/c14b177d-f7b6-4696-bdf8-a6517000c39a" />


Your feature branch started at C.
Main now has D and E — but your branch does not know about them.

**Merge solution** — brings D and E in with a merge commit:
```
main:          A → B → C → D → E → M (merge commit)
                         \         /
feature-login:            F → G --
```

**Rebase solution** — moves your commits ON TOP of the latest main:
```
main:          A → B → C → D → E
                                 \
feature-login:                    F' → G'
```
<img width="1472" height="760" alt="image" src="https://github.com/user-attachments/assets/aa0960cc-e8cf-4c2d-92bf-cb2d41c39fc5" />

Your commits F and G got "replayed" on top of E.
History is clean and straight — no merge commit.

---

## Practical — Git Rebase Step by Step

### Where to go:

Open **VS Code** → Open terminal with `` Ctrl + ` ``

```bash
cd Desktop/git-practice
```

---

### ✅ STEP 1 — Set up the situation

First make sure you are on main and it is clean:

```bash
git checkout main
git status
```

Output should say:
```
On branch main
nothing to commit, working tree clean
```

Now create two commits on main — simulating your teammate's work:

```bash
echo "teammate line 1" >> README.md
git add .
git commit -m "docs: teammate added line 1"

echo "teammate line 2" >> README.md
git add .
git commit -m "docs: teammate added line 2"
```

---

### ✅ STEP 2 — Create your feature branch (from an older point)

```bash
git checkout -b feature-rebase-demo
```

Add your own work on this branch:

```bash
echo "my feature work - step 1" > feature.txt
git add .
git commit -m "feat: add feature step 1"

echo "my feature work - step 2" >> feature.txt
git add .
git commit -m "feat: add feature step 2"
```

Check the history — notice the branching:

```bash
git log --oneline --graph --all
```

**Output:**
```
* f3a91bc (HEAD -> feature-rebase-demo) feat: add feature step 2
* b2e84cd feat: add feature step 1
* 9c3d2a1 (main) docs: teammate added line 2
* 7f1b3e4 docs: teammate added line 1
* a3f9c2b feat: add README file
```

---

### ✅ STEP 3 — Rebase your branch onto main

This means: "Take my commits and put them on top of the latest main"

```bash
git rebase main
```

**Output:**
```
Successfully rebased and updated refs/heads/feature-rebase-demo.
```

Now check history again:

```bash
git log --oneline --graph --all
```

**Output:**
```
* d9f21ac (HEAD -> feature-rebase-demo) feat: add feature step 2
* c8b13fa feat: add feature step 1
* 9c3d2a1 (main) docs: teammate added line 2
* 7f1b3e4 docs: teammate added line 1
* a3f9c2b feat: add README file
```

Your commits are now **on top of main** — straight, clean line! ✅

Notice the commit hashes changed (F became F') — Git created new commits.

---

### ✅ STEP 4 — Interactive Rebase (the most powerful tool)

Interactive rebase lets you **edit your commits before sharing them.**

You can:
- Combine multiple commits into one (squash)
- Change commit messages
- Delete a commit
- Reorder commits

**Use this before opening a Pull Request** — clean up your messy commits.

First make 3 small messy commits:

```bash
echo "line a" >> feature.txt
git add . && git commit -m "wip"

echo "line b" >> feature.txt
git add . && git commit -m "temp"

echo "line c" >> feature.txt
git add . && git commit -m "fix typo"
```

Now combine all 3 into one clean commit:

```bash
git rebase -i HEAD~3
```

**This opens a file in VS Code that looks like:**

```
pick a1b2c3d wip
pick b2c3d4e temp
pick c3d4e5f fix typo
```

**Change it to this** — keep first as `pick`, change others to `squash`:

```
pick a1b2c3d wip
squash b2c3d4e temp
squash c3d4e5f fix typo
```

Save and close the file.

Another file opens — write the final commit message:

```
feat: add feature lines a, b, and c
```

Save and close.

Check history:

```bash
git log --oneline
```

**Output — 3 commits became 1!**
```
e7f92ab feat: add feature lines a, b, and c
d9f21ac feat: add feature step 2
c8b13fa feat: add feature step 1
```

This is exactly what senior developers do before submitting a PR! ✅

---

# PART 2 — Reset and Revert

## The Big Question — How to Undo in Git?

There are 3 ways to undo. Each one is different. Know exactly when to use which one.

| Situation | Use this |
|---|---|
| Undo local commit — not pushed yet | `git reset` |
| Undo a commit that is already pushed | `git revert` |
| Remove a file from staging | `git restore --staged` |

---

## git reset — Three Modes Explained

Think of it like a **time machine with 3 settings:**

```
Your commits:   A → B → C → D (HEAD is here)

After reset to B:

--soft:   A → B   (C and D gone, but changes still in staging)
--mixed:  A → B   (C and D gone, changes back in working dir)
--hard:   A → B   (C and D gone, changes DELETED FOREVER)
```

---

### Where to go:

Stay in the same VS Code terminal, same folder:

```bash
cd Desktop/git-practice
git checkout main
```

---

### ✅ STEP 5 — Practice git reset
<img width="1472" height="602" alt="image" src="https://github.com/user-attachments/assets/4dc5a889-f2af-435d-80e6-9ff1bc79c41c" />

Make 3 test commits:

```bash
echo "commit one" > test.txt && git add . && git commit -m "test: commit one"
echo "commit two" >> test.txt && git add . && git commit -m "test: commit two"
echo "commit three" >> test.txt && git add . && git commit -m "test: commit three"
```

Check history:

```bash
git log --oneline
```

```
c3f9a12 test: commit three
b2e8f34 test: commit two
a1d7e56 test: commit one
...
```

**Try --soft reset** (safest — changes come back to staging):

```bash
git reset --soft HEAD~1
git status
```

Output:
```
Changes to be committed:
  modified: test.txt
```

The last commit is gone — but the changes are still there, ready to commit again. ✅

**Try --mixed reset** (default — changes come back to working directory):

```bash
git reset HEAD~1
git status
```

Output:
```
Changes not staged for commit:
  modified: test.txt
```

Changes are back but not staged. You need to `git add` again.

**Try --hard reset** (dangerous — changes are deleted!):

```bash
git reset --hard HEAD~1
git status
```

Output:
```
nothing to commit, working tree clean
```

That commit AND its changes are completely gone. ⚠️

---

### ✅ STEP 6 — git revert (safe undo for pushed commits)

Never use `git reset` on commits you already pushed to GitHub — it breaks teammates' work.

Instead use `git revert` — it creates a NEW commit that undoes the old one.

```bash
git log --oneline
```

Copy the hash of any commit you want to undo. Example:

```bash
git revert a1d7e56
```

VS Code opens — write a message or just save and close.

**Output:**
```
[main f9c3b21] Revert "test: commit one"
```

A brand new commit appeared that undoes the old one.
The old commit is still in history — nothing was deleted.
This is safe for pushed commits! ✅

---

# PART 3 — Cherry-pick

## What is Cherry-pick?

Imagine you baked 10 cookies — but you only want to give someone **one specific cookie**, not all 10.

Cherry-pick = take **one specific commit** from any branch and copy it to your current branch.

**Real use case:**
- You fixed a critical bug on your feature branch
- Main branch needs that fix RIGHT NOW
- But you are not ready to merge the whole feature yet
- Solution: cherry-pick just that one bug fix commit to main

---

### ✅ STEP 7 — Practice Cherry-pick

Create a new branch with multiple commits:

```bash
git checkout -b cherry-demo

echo "feature code A" > cherry.txt && git add . && git commit -m "feat: feature A"
echo "critical bug fix" >> cherry.txt && git add . && git commit -m "fix: critical bug fix"
echo "feature code B" >> cherry.txt && git add . && git commit -m "feat: feature B"
```

Check the log — note down the hash of the bug fix commit:

```bash
git log --oneline
```

Output:
```
f9c3b21 feat: feature B
e8d2a10 fix: critical bug fix     ← we want THIS one only
d7c1b09 feat: feature A
```

Now go to main and bring only the bug fix:

```bash
git checkout main
git cherry-pick e8d2a10
```

Output:
```
[main 2a3b4c5] fix: critical bug fix
```

Check main's log — only the bug fix is there, not feature A or B:

```bash
git log --oneline
```

```
2a3b4c5 fix: critical bug fix   ← arrived from cherry-demo branch!
...previous commits...
```

Bug fix on main — without merging the whole unfinished feature! ✅

---

# PART 4 — Tags

## What are Tags?

A tag is like putting a **sticky label** on a specific commit.

> "This commit = Version 1.0 of my project"

Tags are used for **releases** — when your app is ready to share with users.

**Two types:**
- Lightweight tag = just a name, nothing else
- Annotated tag = name + your message + date + who tagged it (use this always)

---

### ✅ STEP 8 — Practice Tags

Go to main branch:

```bash
git checkout main
git log --oneline
```

Create an annotated tag on the latest commit:

```bash
git tag -a v1.0 -m "First release — basic Git project"
```

See all tags:

```bash
git tag
```

Output:
```
v1.0
```

See tag details:

```bash
git show v1.0
```

Output shows the commit, your message, date, and author. ✅

Tag an older specific commit:

```bash
git log --oneline
```

Copy any old commit hash, then:

```bash
git tag -a v0.1 a3f9c2b -m "Initial version"
```

Push tags to GitHub:

```bash
git push origin --tags
```

Go to GitHub → your repo → click **"Tags"** tab — you will see v1.0 and v0.1 listed there!

This is how professional projects show their releases. ✅

---

# PART 5 — Reflog (Emergency Rescue Tool)

## What is Reflog?

`git reflog` is your **safety net.**

Even if you accidentally deleted commits with `git reset --hard` — reflog remembers everything for 90 days.

Think of it as Git's secret diary that records **every single thing** that happened.

---

### ✅ STEP 9 — Practice Reflog

First make a commit, then "accidentally" delete it:

```bash
echo "very important work" > important.txt
git add . && git commit -m "feat: very important work"
```

Check it is there:

```bash
git log --oneline
```

Now "accidentally" hard reset and delete it:

```bash
git reset --hard HEAD~1
git log --oneline
```

The commit is gone from log! 😱

**But wait — use reflog to find it:**

```bash
git reflog
```

Output:
```
9c3d2a1 HEAD@{0}: reset: moving to HEAD~1
f7a21bc HEAD@{1}: commit: feat: very important work   ← HERE IT IS!
...
```

Copy the hash `f7a21bc` — now restore it:

```bash
git checkout f7a21bc
```

Output:
```
HEAD is now at f7a21bc feat: very important work
```

Create a branch from here to save it:

```bash
git checkout -b rescued-branch
git checkout main
git merge rescued-branch
```

Your "deleted" commit is back! ✅

**Reflog = your emergency parachute. Always check it before panicking.**
<img width="1472" height="840" alt="image" src="https://github.com/user-attachments/assets/5f325528-1eb1-4679-b208-d2ae46e30e6b" />

---

## 🧠 Quick Revision — Class 5

| Command | What it does |
|---|---|
| `git rebase main` | Move your branch commits on top of main |
| `git rebase -i HEAD~3` | Interactively edit last 3 commits |
| `git reset --soft HEAD~1` | Undo commit, keep changes staged |
| `git reset HEAD~1` | Undo commit, keep changes unstaged |
| `git reset --hard HEAD~1` | Undo commit AND delete changes ⚠️ |
| `git revert <hash>` | Safe undo — creates new commit |
| `git cherry-pick <hash>` | Copy one commit to current branch |
| `git tag -a v1.0 -m "msg"` | Create annotated tag |
| `git push origin --tags` | Push tags to GitHub |
| `git reflog` | See full history — rescue deleted work |

---

## 💡 Interview Questions — With Answers

**Q: What is the difference between rebase and merge?**

Both combine branches. Merge creates a merge commit and preserves history. Rebase rewrites history by replaying commits — giving a clean, linear history. Use merge for shared branches, rebase for personal feature branches before opening a PR.

**Q: When would you use cherry-pick?**

When a specific commit — like a critical bug fix — needs to go to another branch immediately, without merging all the other unfinished work from that branch.

**Q: What is the difference between reset and revert?**

Reset moves the HEAD pointer backwards and can delete commits — dangerous for pushed code. Revert creates a new commit that undoes a previous one — safe for pushed code because it does not rewrite history.

**Q: I accidentally did git reset --hard — is my work gone?**

No! Run `git reflog` immediately. Find your commit hash there and restore it with `git checkout <hash>`.

---

## 🎯 Practice Task — Do This Yourself

Go to VS Code terminal → `cd Desktop/git-practice` and do all of this:

1. Create branch `practice-rebase` → add 2 commits → rebase it onto main
2. Make 3 small commits → use interactive rebase to squash them into 1
3. Make a commit → undo it with `--soft` reset → recommit it with a better message
4. Make a commit → push to GitHub → use `git revert` to undo it safely
5. On a new branch → make 3 commits → cherry-pick only the middle commit to main
6. Create tag `v1.0` on your latest commit → push it to GitHub → check the Tags tab
7. Make a commit → hard reset to delete it → use `git reflog` to rescue it

