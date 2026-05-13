# 📚 CLASS 2 — Branching & Merging

## Pehle Theory — Real life se samjho

Socho tumhara **AI Career Mentor project** chal raha hai. Ek din:
- Tumhe **naya feature** banana hai — "Resume Analyzer"
- Saath mein ek **bug fix** bhi karna hai
- Aur **main code** stable rehna chahiye

Bina Git branch ke? Sab ek jagah karo — **chaos!**
Git branch ke saath? **Teen alag timelines** — koi interference nahi!

```
main branch    ●──────────────────────────────●
                \                            /
feature branch   ●──●──●  (resume analyzer) ●  ← merge
```

---

## Branch kya hoti hai — bilkul simple

> Branch = tumhari project ki **ek alag copy** jisme tum safely kaam kar sako bina main code chhede.

- Jab branch banate ho — ek **naya pointer** banta hai current commit pe
- Dono branches **independent** hoti hain — ek mein change doosri pe nahi aata
- Kaam khatam? **Merge karo** — sab ek ho jaata hai

---

## HEAD pointer — samajhna zaroori hai

```
main    ●──●──●  ← HEAD (tum yahan ho)
```

**HEAD** = Git ka GPS — "tum abhi kahan ho" batata hai.
Branch switch karo — HEAD wahan chala jaata hai.

---

## Ab Practical — Class 1 wala folder kholo

VS Code terminal mein:

```bash
cd Desktop/git-practice
git log --oneline
```

Tumhara previous kaam dikhega — good, wahan se aage badho.

---

### ✅ STEP 1 — Branches dekho

```bash
git branch
```

**Output:**
```
* main
```

`*` matlab — tum abhi `main` pe ho.

---

### ✅ STEP 2 — Naya branch banao

```bash
git branch feature-resume
```

Ab branches dekho:

```bash
git branch
```

**Output:**
```
  feature-resume
* main
```

Branch bani — lekin tum abhi bhi `main` pe ho!

---

### ✅ STEP 3 — Branch pe jaao (switch)

```bash
git checkout feature-resume
```

**Output:**
```
Switched to branch 'feature-resume'
```

Ya modern way (dono same hain):

```bash
git switch feature-resume
```

Verify karo — tum kahan ho:

```bash
git branch
```

**Output:**
```
* feature-resume
  main
```

`*` ab `feature-resume` pe hai — tum shift ho gaye! ✅

---

### ✅ STEP 4 — Shortcut: branch banao aur seedha jao

```bash
git checkout -b feature-skills
```

Yeh **ek command mein** branch banata + switch karta hai. Ab wapas aao:

```bash
git checkout feature-resume
```

---

### ✅ STEP 5 — Feature branch mein kaam karo

Abhi tum `feature-resume` branch pe ho. Ek file banao:

```bash
echo "def analyze_resume(): return 'ATS Score: 85%'" > resume.py
git status
```

**Output:**
```
On branch feature-resume
Untracked files:
  resume.py
```

Stage aur commit karo:

```bash
git add resume.py
git commit -m "feat: add resume analyzer function"
```

Ek aur change karo:

```bash
echo "def score_skills(skills): return len(skills) * 10" >> resume.py
git add .
git commit -m "feat: add skill scoring function"
```

History dekho — sirf is branch ki:

```bash
git log --oneline
```

**Output:**
```
f8a21bc feat: add skill scoring function
c3d90ef feat: add resume analyzer function
a3f9c2b feat: add README file
```

---

### ✅ STEP 6 — Main branch unchanged hai — dekho!

```bash
git checkout main
```

Ab dekho:

```bash
ls
```

**Output:**
```
README.md  app.py
```

`resume.py` nahi hai! Kyunki woh `feature-resume` branch mein hai — **main bilkul safe hai!** ✅

---

### ✅ STEP 7 — Merge karo — feature ko main mein laao

Pehle `main` pe raho (already ho):

```bash
git branch
```

```
  feature-resume
  feature-skills
* main
```

Ab merge karo:

```bash
git merge feature-resume
```

**Output:**
```
Updating a3f9c2b..f8a21bc
Fast-forward
 resume.py | 2 ++
 1 file changed, 2 insertions(+)
```

Ab dekho:

```bash
ls
```

**Output:**
```
README.md  app.py  resume.py
```

`resume.py` aa gaya main mein! 🎉

---

### ✅ STEP 8 — Kaam ho gaya, branch delete karo

```bash
git branch -d feature-resume
```

**Output:**
```
Deleted branch feature-resume (was f8a21bc).
```

---

### ✅ STEP 9 — Merge Conflict — ghabrao mat!

Yeh **most important** concept hai — industry mein roz hota hai.

**Conflict kab hota hai?**
Jab **dono branches** ne **same file ki same line** change ki ho.

Chalo artificially create karte hain:

**Step 1 — main mein app.py change karo:**

```bash
git checkout main
echo "print('Version from MAIN')" > app.py
git add .
git commit -m "fix: update message in main"
```

**Step 2 — naya branch banao aur wahan bhi same file change karo:**

```bash
git checkout -b hotfix-message
echo "print('Version from HOTFIX')" > app.py
git add .
git commit -m "fix: update message in hotfix"
```

**Step 3 — main pe wapas aao aur merge karo:**

```bash
git checkout main
git merge hotfix-message
```

**Output — CONFLICT!**

```
Auto-merging app.py
CONFLICT (content): Merge conflict in app.py
Automatic merge failed; fix conflicts then commit the result.
```

**Step 4 — file kholke dekho:**

```bash
cat app.py
```

**Output:**

```
<<<<<<< HEAD
print('Version from MAIN')
=======
print('Version from HOTFIX')
>>>>>>> hotfix-message
```

Yeh markers ka matlab:
```
<<<<<<< HEAD          ← tumhari (main branch) wali line
print('Version from MAIN')
=======               ← divider
print('Version from HOTFIX')
>>>>>>> hotfix-message ← dusri branch wali line
```

**Step 5 — manually decide karo kya rakhna hai:**

```bash
echo "print('Final merged version')" > app.py
```

**Step 6 — resolve karke commit karo:**

```bash
git add app.py
git commit -m "fix: resolve merge conflict in app.py"
```

**Output:**
```
[main 9c4f2a1] fix: resolve merge conflict in app.py
```

Conflict resolve! ✅

---

### ✅ STEP 10 — Poori history ek saath dekho

```bash
git log --oneline --graph --all
```

**Output:**

```
*   9c4f2a1 fix: resolve merge conflict
|\
| * 3b1d8f2 fix: update message in hotfix
* | 7e9c3a1 fix: update message in main
|/
* f8a21bc feat: add skill scoring function
* c3d90ef feat: add resume analyzer function
* a3f9c2b feat: add README file
```

Yeh graph tumhara **poora branching history** dikhata hai — kitna clean! 😍

---

## 🧠 Quick Revision — Class 2

| Command | Kaam |
|---|---|
| `git branch` | Branches dekho |
| `git branch name` | Branch banao |
| `git checkout name` | Branch pe jao |
| `git checkout -b name` | Banao + jao ek saath |
| `git switch name` | Modern switch |
| `git merge name` | Branch merge karo |
| `git branch -d name` | Branch delete karo |
| `git log --graph --all` | Visual history |

---

## 💡 Interview Tips

**Q: Merge aur Rebase mein fark?**
Merge = history preserve karta hai (merge commit banta hai). Rebase = linear clean history (commits replay hoti hain). Team branches ke liye merge, personal cleanup ke liye rebase.

**Q: Fast-forward merge kab hota hai?**
Jab main branch mein koi naya commit nahi tha feature branch banne ke baad — sirf pointer aage badh jaata hai, koi merge commit nahi banta.

**Q: Conflict avoid kaise karein?**
Roz `git pull` karo, chhoti chhoti branches banao, zyada time branch pe mat rakhna — jaldi merge karo.

---

## 🎯 Aaj ki Practice

Khud se yeh karo **bina dekhe:**

1. `my-project` naam ka folder banao, `git init` karo
2. `main.py` banao, commit karo
3. `feature-login` branch banao, kuch code likho, commit karo
4. `main` pe wapas aao, merge karo
5. Manually ek conflict create karo aur resolve karo
6. `git log --graph --all` se history dekho

---


