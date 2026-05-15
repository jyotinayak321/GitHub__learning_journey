

---

# 📚 CLASS 3 — GitHub: Taking Your Code Online

## First, Understand the Big Picture

Until now, everything was **local** — only on your computer.

Think of it like this:

> Your computer = your personal diary (only you can see it)
> GitHub = a public library (everyone can see, collaborate, contribute)

When you **push** your code to GitHub:
- Your code is **safe** even if your laptop breaks
- Others can **see and use** your code
- Recruiters can **check your work**
- Teams can **collaborate** together

---

## Two Ways to Connect GitHub — HTTPS vs SSH

| | HTTPS | SSH |
|---|---|---|
| Password needed? | Yes, every time | No, set up once — done forever |
| Speed | Slower | Faster |
| Recommended? | Beginners | Everyone (including you now) |

**We will set up SSH today** — because you are past the beginner stage.

---

## Practical — Step by Step

### ✅ STEP 1 — Create a GitHub Account

Go to **github.com** → Sign Up → Use your email → Choose a username.

**Tip for your username:** Keep it professional. Something like `jyotinayak321` — recruiters will see this!

---

### ✅ STEP 2 — Generate SSH Key on Your Computer

Open **VS Code terminal** and type:

```bash
ssh-keygen -t ed25519 -C "your-email@gmail.com"
```

It will ask three questions — **just press Enter three times** (use all defaults):

```
Enter file in which to save the key: [Press Enter]
Enter passphrase: [Press Enter]
Enter same passphrase again: [Press Enter]
```

**Output you will see:**
```
Your identification has been saved in C:/Users/rajes/.ssh/id_ed25519
Your public key has been saved in C:/Users/rajes/.ssh/id_ed25519.pub
```

Two files just got created:
- `id_ed25519` → **Private key** — stays on YOUR computer, never share this
- `id_ed25519.pub` → **Public key** — you give this to GitHub

Think of it like a **lock and key**:
- Private key = your key (only you have it)
- Public key = the lock (you put it on GitHub's door)

---

### ✅ STEP 3 — Copy Your Public Key

```bash
cat ~/.ssh/id_ed25519.pub
```

**Output will look like this:**
```
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAI... your-email@gmail.com
```

**Select all of this text and copy it.**

---

### ✅ STEP 4 — Add SSH Key to GitHub

1. Go to **github.com** → Click your profile photo (top right)
2. Click **Settings**
3. Left sidebar → **SSH and GPG keys**
4. Click **New SSH key** (green button)
5. Title: `My Laptop` (or anything you remember)
6. Key: **Paste** what you copied
7. Click **Add SSH key**

**Test if it works:**

```bash
ssh -T git@github.com
```

**Output:**
```
Hi jyotinayak321! You've successfully authenticated!
```

SSH is ready! ✅ You will **never type a password again** for Git.

---

### ✅ STEP 5 — Create a New Repository on GitHub

1. Go to **github.com**
2. Click the **+** button (top right) → **New repository**
3. Fill in the details:
   - Repository name: `git-practice`
   - Description: `My Git learning project`
   - Keep it **Public**
   - **Do NOT check** "Add README" — we already have one locally
4. Click **Create repository**

You will see a page with instructions. We will use the **SSH option**.

---

### ✅ STEP 6 — Connect Your Local Repo to GitHub

Go back to VS Code terminal:

```bash
cd Desktop/git-practice
```

Now connect your local project to GitHub:

```bash
git remote add origin git@github.com:jyotinayak321/git-practice.git
```

**What does this mean?**
- `remote` = a connection to an online server
- `add` = add a new connection
- `origin` = the name we give this connection (standard name, everyone uses "origin")
- The URL = your GitHub repo address

**Verify the connection:**

```bash
git remote -v
```

**Output:**
```
origin  git@github.com:jyotinayak321/git-practice.git (fetch)
origin  git@github.com:jyotinayak321/git-practice.git (push)
```

Two lines show — one for downloading (fetch), one for uploading (push). ✅

---

### ✅ STEP 7 — Push Your Code to GitHub

```bash
git push -u origin main
```

**What does each word mean?**
- `push` = upload your commits to GitHub
- `-u` = set up tracking (do this only the first time)
- `origin` = send it to the GitHub connection we just created
- `main` = push this specific branch

**Output:**
```
Enumerating objects: 8, done.
Counting objects: 100% (8/8), done.
Writing objects: 100% (8/8), 756 bytes | 756.00 KiB/s, done.
Branch 'main' set up to track remote branch 'main' from 'origin'.
```

**Now go to github.com/jyotinayak321/git-practice**

Your code is live on the internet! 🎉

---

### ✅ STEP 8 — Make a Change and Push Again

Make a small change:

```bash
echo "GitHub connected successfully!" >> README.md
git add .
git commit -m "docs: update README with GitHub note"
git push
```

Notice — this time you only typed `git push` (no `-u origin main`). Because tracking is already set up from the first push.

Go to GitHub — refresh the page — your new change is there instantly! ✅

---

### ✅ STEP 9 — Pull — Bring Changes FROM GitHub

Imagine someone else changed your code on GitHub (or you edited directly on the website).

**Edit a file directly on GitHub:**
1. Go to your repo on GitHub
2. Click on `README.md`
3. Click the **pencil icon** (edit)
4. Add a line: `Edited directly on GitHub`
5. Scroll down → Click **Commit changes**

Now your GitHub has a newer version than your local computer.

**Bring those changes to your computer:**

```bash
git pull
```

**Output:**
```
Updating f8a21bc..9c3d2a1
Fast-forward
 README.md | 1 +
 1 file changed, 1 insertion(+)
```

Your local file now has the GitHub changes. ✅

---

### ✅ STEP 10 — The Golden Rule of Team Work

Always follow this order every single day:

```
Start work  →  git pull  →  make changes  →  git add  →  git commit  →  git push
```

**Why pull first?**

If someone else pushed changes while you were working — and you push without pulling — Git will **reject your push**. Always pull before you push.

---

## 🧠 Quick Revision — Class 3

| Command | What it does |
|---|---|
| `ssh-keygen -t ed25519 -C "email"` | Generate SSH key |
| `ssh -T git@github.com` | Test SSH connection |
| `git remote add origin <url>` | Connect local repo to GitHub |
| `git remote -v` | See connected remotes |
| `git push -u origin main` | First time push |
| `git push` | Push after first time |
| `git pull` | Get latest changes from GitHub |

---

## 💡 Interview Tips

**Q: What is the difference between git fetch and git pull?**

`git fetch` only **downloads** the changes but does NOT apply them to your code. `git pull` downloads AND **merges** them automatically. Use `git fetch` when you want to see what changed before applying.

**Q: What is origin?**

Origin is just a **nickname** for your GitHub repository URL. You can rename it or have multiple remotes. For example, open source projects have two remotes — `origin` (your fork) and `upstream` (the original project).

**Q: What if push is rejected?**

```bash
git pull
# resolve any conflicts
git push
```

Always pull first — then push.

---

## 🎯 Practice Task for Today

Do all of this **on your own** without looking:

1. Create a new GitHub repository called `my-portfolio`
2. Set up SSH connection
3. Create a local folder, initialize Git
4. Create 3 files — `index.html`, `style.css`, `README.md`
5. Commit each file **separately** (3 different commits)
6. Push everything to GitHub
7. Edit `README.md` directly on GitHub website
8. Pull those changes to your local computer
9. Check `git log --oneline` — you should see all commits

---

