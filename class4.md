# 📚 CLASS 4 — Pull Requests & Real Team Collaboration

## First, Understand the Concept

Until now, you were working **alone** on your project.

But in real companies — Google, Amazon, DRDO — **10, 50, 100+ developers** work on the same project.

Imagine this situation:

> You joined Google as a fresher.
> Your senior says — "Add a new feature to our search algorithm."
> You cannot directly touch the main code — it runs for **billions of users!**
> So you work on a **separate copy**, and then **request** your senior to review and merge it.

**That request = Pull Request (PR)**

---

## What is a Pull Request?

> A Pull Request is a way of saying:
> **"I made some changes. Please review my code and merge it into the main project."**

It is the **heart of team collaboration** in the software industry.

Every company uses PRs:
- Google ✅
- Amazon ✅
- Microsoft ✅
- DRDO ✅
- Every startup ✅

---

## The Complete Team Workflow — Big Picture

```
Create branch → Write code → Push branch → Open PR
→ Team reviews → Make changes if needed → PR approved → Merge → Done
```

---

## Practical — Full Pull Request Workflow

### ✅ STEP 1 — Start Fresh From Main

Always start new work from an updated main branch:

```bash
cd Desktop/git-practice
git checkout main
git pull
```

This makes sure your starting point has the **latest code**.

---

### ✅ STEP 2 — Create a Feature Branch

```bash
git checkout -b feature/add-about-page
```

**Branch naming rules in industry:**
- `feature/` → new feature
- `fix/` → bug fix
- `docs/` → documentation update
- `hotfix/` → urgent production fix

Always use **lowercase with hyphens** — no spaces, no capital letters.

---

### ✅ STEP 3 — Do Your Work and Commit

Create a new file:

```bash
echo "This is the About page" > about.html
```

Add more content:

```bash
echo "Author: Jyoti Nayak" >> about.html
echo "Project: Git Learning" >> about.html
```

Now commit your work:

```bash
git add .
git commit -m "feat: add about page with author info"
```

Make one more small change and commit again:

```bash
echo "Contact: rajeshware321@gmail.com" >> about.html
git add .
git commit -m "feat: add contact info to about page"
```

Check your commits:

```bash
git log --oneline
```

**Output:**
```
b3f92cd feat: add contact info to about page
a1e84bc feat: add about page with author info
f8a21bc feat: update README
a3f9c2b feat: add README file
```

---

### ✅ STEP 4 — Push Your Branch to GitHub

This is important — you are pushing the **feature branch**, not main:

```bash
git push -u origin feature/add-about-page
```

**Output:**
```
Enumerating objects: 4, done.
To github.com:jyotinayak321/git-practice.git
 * [new branch]  feature/add-about-page -> feature/add-about-page
Branch set up to track remote branch 'feature/add-about-page' from 'origin'.
```

Your feature branch is now on GitHub! ✅

---

### ✅ STEP 5 — Open a Pull Request on GitHub

1. Go to **github.com/jyotinayak321/git-practice**
2. You will see a **yellow banner** that says:

```
feature/add-about-page had recent pushes — Compare & pull request
```

3. Click **"Compare & pull request"**

4. Fill in the PR form:

**Title:**
```
feat: add about page with author and contact info
```

**Description — write this clearly:**
```
## What I changed
- Created about.html with author information
- Added contact details to the about page

## Why I changed it
- Users need to know who built this project
- Contact info helps people reach out

## How to test
- Open about.html in browser
- Verify author name and email are correct
```

5. Click **"Create pull request"**

**Your PR is now open!** 🎉

---

### ✅ STEP 6 — Understanding the PR Page

On the PR page you will see these tabs:

| Tab | What it shows |
|---|---|
| **Conversation** | Comments and discussion |
| **Commits** | All commits in this PR |
| **Files changed** | Exactly what lines were added/removed |

Click on **"Files changed"** tab.

You will see:
- Lines in **green** = new lines you added
- Lines in **red** = lines you removed

This is exactly what your senior/reviewer will see when they review your code.

---

### ✅ STEP 7 — Review Code Like a Senior Developer

In real teams, someone else reviews your PR. Let us learn how to review properly.

**On the "Files changed" tab:**

1. Hover over any line number
2. A **blue + button** appears
3. Click it to add a comment on that specific line

**Good review comments look like:**

✅ `"This function name is not clear. Can you rename it to get_user_data()?"` 

✅ `"Please add error handling here — what if the file doesn't exist?"`

✅ `"Great approach! But consider using a constant instead of hardcoding this value."`

❌ `"This is wrong."` ← never write this — explain WHY

---

### ✅ STEP 8 — Make Changes Based on Review

Imagine your reviewer said — "Please add a GitHub profile link to the about page."

Go back to VS Code terminal:

```bash
git checkout feature/add-about-page
echo "GitHub: github.com/jyotinayak321" >> about.html
git add .
git commit -m "feat: add GitHub profile link to about page"
git push
```

**The PR automatically updates!** You do not need to open a new PR. The new commit appears in the same PR.

This is the normal cycle:
```
Push → Review → Make changes → Push again → Review again → Approve → Merge
```

---

### ✅ STEP 9 — Merge the Pull Request

When everything looks good:

1. Go to the PR on GitHub
2. Scroll down to the bottom
3. Click the green **"Merge pull request"** button
4. Click **"Confirm merge"**

**Output on GitHub:**
```
Pull request successfully merged and closed ✅
```

5. Click **"Delete branch"** — clean up after merging (good practice)

---

### ✅ STEP 10 — Update Your Local Main Branch

Your GitHub main branch is now updated. Bring those changes to your laptop:

```bash
git checkout main
git pull
```

```bash
git log --oneline
```

**Output:**
```
d9f3a21 Merge pull request #1 — feat: add about page
b3f92cd feat: add contact info to about page
a1e84bc feat: add about page with author info
f8a21bc feat: update README
a3f9c2b feat: add README file
```

Your feature is now safely in main! ✅

---

### ✅ STEP 11 — Delete Local Branch Too

```bash
git branch -d feature/add-about-page
```

Clean workspace = clean mind. Always delete branches after merging.

---

## 🔥 BONUS — Fork Workflow for Open Source

This is how you contribute to **real open source projects** like LangChain, FastAPI, or any GitHub project.

### The difference:

| Pull Request (Team) | Fork + Pull Request (Open Source) |
|---|---|
| You have access to the repo | You do NOT have access |
| You push branches directly | You fork first, then push |
| Same team | Any stranger can contribute |

### How to contribute to open source:

**Step 1 — Fork the project**

Go to any GitHub repo → Click **"Fork"** button (top right)

This creates **your own copy** of that repo under your account.

**Step 2 — Clone your fork**

```bash
git clone git@github.com:jyotinayak321/langchain.git
cd langchain
```

**Step 3 — Add original repo as upstream**

```bash
git remote add upstream git@github.com:langchain-ai/langchain.git
git remote -v
```

**Output:**
```
origin    git@github.com:jyotinayak321/langchain.git (your fork)
upstream  git@github.com:langchain-ai/langchain.git (original)
```

**Step 4 — Always sync with original before working**

```bash
git fetch upstream
git merge upstream/main
```

**Step 5 — Make changes and push to YOUR fork**

```bash
git checkout -b fix/typo-in-docs
# make your changes
git add .
git commit -m "fix: correct typo in README"
git push origin fix/typo-in-docs
```

**Step 6 — Open PR from your fork to original repo**

Go to **github.com/jyotinayak321/langchain**

GitHub will show — "This branch is 1 commit ahead of langchain-ai:main"

Click **"Contribute"** → **"Open pull request"**

Your PR goes to the original project! If they merge it — **you are an open source contributor!** 🎉

---

## 🧠 Quick Revision — Class 4

| Action | Command / Step |
|---|---|
| Start fresh | `git checkout main` → `git pull` |
| Create feature branch | `git checkout -b feature/name` |
| Push branch | `git push -u origin feature/name` |
| Open PR | GitHub website → Compare & pull request |
| Update PR | Just push more commits to same branch |
| Merge PR | GitHub → Merge pull request button |
| Sync after merge | `git checkout main` → `git pull` |
| Fork workflow | Fork → Clone → upstream → PR |

---

## 💡 Interview Tips

**Q: What is the difference between Fork and Clone?**

Clone = download a repo to your computer. Fork = create your own copy of someone's repo on GitHub. You fork when you want to contribute to a project you don't own.

**Q: Can you explain your PR workflow?**

Say this in interview — "I create a feature branch from main, write my code with meaningful commits, push the branch, and open a PR with a clear description. After code review, I address all comments, then the PR is merged by the reviewer."

**Q: What do you check during a code review?**

Logic errors, edge cases, naming conventions, code duplication, missing error handling, and security issues like exposed API keys.

---

## 🎯 Practice Task

Do this completely on your own:

1. Create a new repo on GitHub called `pr-practice`
2. Clone it to your computer
3. Create branch `feature/add-homepage`
4. Create `index.html` with some content
5. Commit and push the branch
6. Open a Pull Request on GitHub
7. Edit the file again → push → watch PR update automatically
8. Merge the PR
9. Pull to local → delete the branch
10. Check `git log --oneline` — verify merge commit is there

---

