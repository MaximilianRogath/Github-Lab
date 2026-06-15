# Lab 3 – Push your local repo to GitHub

## What you'll learn

- How to connect your local repo from Lab 2 to an **empty GitHub repository**.
- The three key concepts **Remote**, **Origin**, **Upstream**.
- The commands `git remote`, `git push`, `git pull` – and when you need which.
- How the **bidirectional workflow** works (local ↔ GitHub).

---

## Why do you need this? (The aha moment)

In Lab 1 your repo lived only on **github.com**. In Lab 2 only on **your machine**.
Both are half the story. The real magic happens when both worlds **stay in sync**.

**The big aha moment in this lab:**

| | Local only (Lab 2) | Local + GitHub (Lab 3) |
|---|---|---|
| Backup if your laptop dies | ❌ everything gone | ✅ safe in the cloud |
| Share with colleagues | ❌ only via ZIP file | ✅ send them a URL |
| Work on multiple devices | ❌ manual sync every time | ✅ `git pull` pulls the latest state |
| Code review possible | ❌ no shared place | ✅ Pull Requests (Lab 5) |

→ **Without a remote** = local notebook. **With a remote** = networked project.

### Concrete benefits at a glance

| Benefit | What that means in practice |
|---|---|
| **Backup in the cloud** | Lose your laptop → code stays. |
| **Work across devices** | Commit on your office desktop, keep going on your laptop in the evening. |
| **Share via URL** | Colleagues see your code with one click. |
| **Prerequisite for teamwork** | Pull Requests, reviews, issues – only work with a remote. |
| **Build a portfolio** | Your public repos show what you're working on. |

---

## Prerequisites

- Lab 2 completed.
- `my-local-repo` with at least one commit exists locally.
- GitHub account, signed in via the browser.
- Git Credential Manager works (ships with Git for Windows by default).

## Files in this lab

```
lab3-connect-remote/
└── README.md       ← this document
```

> You work in **your existing `my-local-repo`** from Lab 2. No extra files
> are needed inside the lab folder.

---

## Hands-on

### Step 1 – Create an empty repository on GitHub

In the browser, go to https://github.com/new and fill in:

| Field | Value |
|---|---|
| **Owner** | your username |
| **Repository name** | `my-local-repo` |
| **Description** *(optional)* | `Lab 2 practice repo` |
| **Public** or **Private** | up to you (Public is fine for practice) |
| **Add a README file** | ❌ **DO NOT tick!** |
| **.gitignore** | leave empty |
| **License** | leave empty |

> ⚠️ **Important:** **deliberately do NOT tick** the README/`.gitignore`/License
> checkboxes. Otherwise the remote already has an initial commit, which
> **conflicts** with your local repo that already has commits → `push` would be
> rejected.

Click **"Create repository"**.

> 💡 **Repo name ≠ local folder name.** The name you give here on GitHub will
> appear in the `git remote add origin <url>` URL in a moment. The name of
> your local folder (e.g. `my-local-repo`) is entirely Git's business –
> local and remote are even allowed to have different names.

### Step 2 – Look at GitHub's setup commands

GitHub shows a setup page. The block we care about is the middle one
("…or push an existing repository from the command line") – the three lines
we're about to run.

### Step 3 – Connect locally and push

In PowerShell:

```powershell
cd "$env:USERPROFILE\Documents\my-local-repo"
git remote add origin https://github.com/<YOUR-USERNAME>/my-local-repo.git
git branch -M main
git push -u origin main
```

> Replace `<YOUR-USERNAME>` with your actual GitHub username.

**What the three commands do:**

| Command | Effect |
|---|---|
| `git remote add origin <url>` | Tells your local repo: "There's a twin. It's called `origin`. Here's its address." |
| `git branch -M main` | Makes sure your local branch is called `main` (idempotent, safe to run). |
| `git push -u origin main` | Pushes branch `main` to remote `origin`. `-u` = **set-upstream** → remembers for the future that "main pushes/pulls to origin/main". |

**Expected output (shortened):**

```
Enumerating objects: 9, done.
...
To https://github.com/<YOUR-USERNAME>/my-local-repo.git
 * [new branch]      main -> main
branch 'main' set up to track 'origin/main'.
```

> 💡 **Authentication:** On your very first push, a browser window may pop up
> for the **Git Credential Manager** → sign in to GitHub once, after that it's
> cached and never asked again.

### Step 4 – Verify online

```powershell
Start-Process "https://github.com/<YOUR-USERNAME>/my-local-repo"
```

You should see:
- All your files (e.g. `notes.md`).
- The **complete commit history** from Lab 2.
- Every commit with your avatar and the correct author email.

### Step 5 – Verify the link is correct

```powershell
git remote -v
```

**Expected:**

```
origin  https://github.com/<YOUR-USERNAME>/my-local-repo.git (fetch)
origin  https://github.com/<YOUR-USERNAME>/my-local-repo.git (push)
```

→ "Origin is set up for reading (fetch) and writing (push)." Two lines, same
URL – that's normal.

Check branch tracking:

```powershell
git branch -vv
```

You should see something like:

```
* main 971ef29 [origin/main] Add last note
```

→ The square brackets `[origin/main]` mean: your local `main` is linked to
GitHub's `main` (it's "tracking" it). From now on, plain `git push` is enough
(no need for `origin main`).

### Step 6 – Run the full loop once

This is when the "my local code lives on the internet" feeling really clicks:

```powershell
# 1. Local change
"An entry that's about to go live on GitHub!" | Add-Content notes.md

# 2. Commit
git add notes.md
git commit -m "Add live-test entry"

# 3. Push (no arguments needed, thanks to -u)
git push
```

**Expected from `git push`:**

```
...
To https://github.com/.../my-local-repo.git
   971ef29..abc1234  main -> main
```

→ Refresh the GitHub page in your browser: the new line is in `notes.md`, a
new commit shows in the history. **This is the central workflow** for the rest
of your Git life: **change → commit → push**.

#### One-time setup vs. everyday workflow

Steps 1–5 are something you do **exactly once per repo**. What you actually do
**every day** is the short 3-command loop from Step 6:

| Phase | Command(s) | Frequency |
|---|---|---|
| **One-time setup** | Create repo on GitHub, `git remote add origin …`, `git push -u origin main` | **Exactly once** per repo |
| **Everyday** | `git add` → `git commit -m "..."` → `git push` | **Hundreds of times** in every project |

#### Commit ≠ push (different rhythms!)

A common beginner misconception: you do **not** have to push every commit
immediately.

| Action | How often? | Why? |
|---|---|---|
| **`commit`** | Often. Several times an hour, whenever you finish a small step. | Commits are local, free, instant, and reversible. |
| **`push`** | Less often. When you need backup, want to share with colleagues, or you're done for the day. | Push is a deliberate decision: "this is the version that goes public now." |

**Realistic example** – a coding session on a Python crawler:

```
09:00  Config module        → git commit -m "Add config module"
09:30  fetcher() done       → git commit -m "Implement fetcher with retry"
09:45  Bug fix              → git commit -m "Fix retry bug"
10:30  Parser               → git commit -m "Add HTML parser"
10:45  Tests added          → git commit -m "Add tests for parser"
11:00  ← coffee break:       → git push    (5 commits go out together)
```

→ **5 commits, 1 push.** The history stays fully traceable, but reviewers/CI
aren't woken up 5 times.

### Step 7 – The other direction: `git pull`

What if you change something on **GitHub** (or a colleague commits) and you
want it locally?

**Try it:**

1. Open your repo in the browser → `notes.md` → ✏️ → add a line → commit
   directly to `main`.
2. Locally:

```powershell
git status                 # says: "Your branch is up to date with 'origin/main'."
                           # → locally we still don't know about the remote commit!
git pull                   # fetches the commit from GitHub
git log --oneline          # the new commit is there
Get-Content notes.md       # the new line from the web editor is in the file
```

> 💡 **`git status` doesn't lie – it just only knows its last known state.**
> Only after `git fetch` or `git pull` does your local Git find out whether
> the remote has moved.

### Step 8 – `git clone` as the counterpart to `git init`

`git clone` is essentially `git init` + `git remote add` + first `pull`
all in one command. Try it by cloning your own repo to a different location:

```powershell
cd $env:TEMP
git clone https://github.com/<YOUR-USERNAME>/my-local-repo.git my-clone
cd my-clone
git log --oneline          # full history immediately available
git remote -v              # origin already configured
```

You land **fully equipped** – no `init`, no `remote add`, no first `pull`.
**This is exactly how you clone other people's repos** (open source, colleagues, templates).

Clean up the helper folder when you're done:

```powershell
cd ..
Remove-Item -Recurse -Force my-clone
```

---

## Cheat sheet – commands from this lab at a glance

| Skill | Command |
|---|---|
| Add a remote | `git remote add origin <url>` |
| Show remotes | `git remote -v` |
| Show branch tracking | `git branch -vv` |
| First push + set upstream | `git push -u origin main` |
| Push (after upstream is set) | `git push` |
| Pull changes from the remote | `git pull` |
| Clone a full repo | `git clone <url>` |
| Change remote URL (e.g. after repo rename) | `git remote set-url origin <new-url>` |

---

## Observations

- **`origin`** is just a **name** – the convention for "the default remote".
  You could also call it `github` or `myserver`.
- After `git push -u`, a bare `git push` is enough – no need to repeat
  `origin main`.
- `git pull` is internally two steps: `git fetch` (get data without merging) +
  `git merge` (merge it in).
- Pushing is **not** like "copying files": Git pushes **commits** (with hash,
  author, parent, etc.) – not the current file state.
- Multiple remotes are allowed (e.g. `origin` = your fork, `upstream` = the original).

## Common pitfalls

| Problem | Solution |
|---|---|
| `error: remote origin already exists` | You ran `remote add` twice. Overwrite the existing one: `git remote set-url origin <url>` |
| `! [rejected] main -> main (fetch first)` when pushing | The remote has commits you don't have locally. Fix: `git pull --rebase` → then push again. |
| `Authentication failed` | The credential manager cached bad credentials. Open Windows Credential Manager → delete `git:https://github.com` → next push will prompt again. |
| Login browser window doesn't open | The terminal shows a code – enter it manually at https://github.com/login/device. |
| Push suddenly wants `--force` | You ran `--amend` on commits that were already pushed. If you're the only user of the repo: `git push --force`. Otherwise: don't do it without coordinating! |

## Cleanup (optional)

We'll keep using the practice repo `my-local-repo` in Lab 4 (Branches).
**Leave it as is.**

If you want to delete it anyway:

- Locally: `Remove-Item -Recurse -Force "$env:USERPROFILE\Documents\my-local-repo"` *(the local folder from Lab 2)*
- On GitHub: Repo → Settings → very bottom → "Delete this repository".

## Further reading / sources

- [Git Docs – Working with Remotes](https://git-scm.com/book/en/v2/Git-Basics-Working-with-Remotes)
- [GitHub Docs – Pushing commits to a remote repository](https://docs.github.com/en/get-started/using-git/pushing-commits-to-a-remote-repository)

---

✅ Lab 3 complete.
