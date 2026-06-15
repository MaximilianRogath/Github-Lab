# Lab 2 – Your first local Git repo

## What you'll learn

- How to use **Git locally** on your machine (still without GitHub).
- The difference between **Working Directory**, **Staging Area**, and **Repository**.
- The core commands `git init`, `git status`, `git add`, `git commit`, `git log`.
- How a commit is built (author, hash, message).

---

## Why do you need this? (The aha moment)

In Lab 1 you worked in the browser on github.com. In reality, you'll spend
roughly 95 % of your time developing **locally on your machine** – with your
editor (e.g. VS Code).
Git is the tool that records every change long before anything is uploaded to GitHub.

**The big aha moment in this lab:**

| | Classic "Save" | Git commit |
|---|---|---|
| What gets saved? | Only the current version | **Snapshot of the entire project** |
| How many versions? | Usually just 1 | **As many as you want, all reachable** |
| Comment attached? | No | **Yes – commit message** |
| Where? | Overwrites the file | **Separate storage (`.git/` folder)** |

→ A commit is like a **save point in a video game**: you can jump back to any
point in time.

### Concrete benefits at a glance

| Benefit | What that means in practice |
|---|---|
| **Works offline** | No internet needed to commit. |
| **Complete history** | Every change with author, date, comment. |
| **Roll back to any state** | "It worked last week" → no problem. |
| **Compare** | `git diff` shows you exactly what changed. |
| **Preparation for GitHub** | A local repo can later be sent to GitHub with `git push` (Lab 3). |

---

## Prerequisites

- Lab 1 completed.
- **Git for Windows** installed (https://git-scm.com/download/win) – accept all defaults during install.
- VS Code (recommended).

### One-time Git configuration

Git needs to know who you are (this ends up in every commit), so you need to
configure Git **once per machine**.

> ⚠️ **Before running the config command:** decide **which email address**
> you want to use. This address goes into **every commit** and, once you push
> to a public repo, becomes **publicly visible forever** and scrapeable by bots.

#### Option A – Real email (simple, but public)

```powershell
git config --global user.name "Max Mustermann"
git config --global user.email "max.mustermann@example.com"
git config --global init.defaultBranch main
```

Use the same email as your GitHub account – that way GitHub recognises your
commits and shows your avatar. Downside: anyone can scrape your email from any
`.patch` endpoint of your public repos (spam risk).

#### Option B – Noreply address (recommended for public repos)

GitHub provides every user with a special address that hides your real email
but still links commits to your profile.

**One-time setup on GitHub:**

1. Go to https://github.com/settings/emails.
2. Tick **"Keep my email addresses private"**.
3. Right below, your personal noreply address is shown, e.g.
   `12345678+yourusername@users.noreply.github.com` → **copy** this address.
4. Also tick **"Block command line pushes that expose my email"** –
   GitHub will then automatically reject pushes that would expose your real
   email (extra safety net).

**Configure locally** – with the address you just copied:

```powershell
git config --global user.name "Max Mustermann"
git config --global user.email "12345678+yourusername@users.noreply.github.com"
git config --global init.defaultBranch main
```

**Verify:**

```powershell
git config --global --list
```

You should see `user.name=...`, `user.email=...@users.noreply.github.com`, and
`init.defaultBranch=main`.

> 🆘 **Already committed and pushed with your real email?** The address is now
> permanently in your history. You can rewrite it after the fact using
> [`git filter-repo`](https://github.com/newren/git-filter-repo) (one script
> run per repo, followed by a force push) – tedious, but doable.

## Files in this lab

```
lab2-first-local-repo/
└── README.md       ← this document
```

> The actual practice repo (`my-local-repo`) is something you'll create
> **yourself** in the hands-on section – outside the lab folder, so it doesn't
> mix with the teaching material.

---

## Hands-on

### Step 1 – Create the project folder

```powershell
cd "$env:USERPROFILE\Documents"
mkdir my-local-repo
cd my-local-repo
```

### Step 2 – Initialise the repository

```powershell
git init
```

**Expected output:**

```
Initialized empty Git repository in C:/Users/.../my-local-repo/.git/
```

There's now a hidden subfolder **`.git/`** inside your project. It contains
the entire history. **Never touch or delete it manually.**

```powershell
# Show hidden files
Get-ChildItem -Force
```

### Step 3 – Check status (will become your best friend)

```powershell
git status
```

Tells you: current branch (`main`), no commits yet, no files tracked.

### Step 4 – Create a file

Open the folder in VS Code:

```powershell
code .
```

Create a new file `notes.md` with this content:

```markdown
# My notes

This is my first local Git project.
```

Save (`Ctrl`+`S`), back to PowerShell:

```powershell
git status
```

Git now says: **`notes.md`** is *untracked* (in red). Git sees the file but
doesn't yet know whether it's supposed to care about it.

### Step 5 – Add the file to the staging area

```powershell
git add notes.md
git status
```

`notes.md` is now **green** under "Changes to be committed" – it sits in the
**Staging Area** (a holding zone for the next commit).

> 💡 **Working Directory → `git add` → Staging Area → `git commit` → Repository**

### Step 6 – Make your first commit

```powershell
git commit -m "Add first notes"
```

**Expected output (shortened):**

```
[main (root-commit) a1b2c3d] Add first notes
 1 file changed, 3 insertions(+)
 create mode 100644 notes.md
```

The hash `a1b2c3d` is the **unique ID** of your commit.

### Step 7 – Look at the history

```powershell
git log
```

Shows your commit with author, date, and message.

Shorter variant:

```powershell
git log --oneline
```

> 💡 If `git log` hangs and doesn't respond to input: press **`q`**.
> Git opens a pager for longer output (same as `less` on Linux); `q` closes it.

### Step 8 – Change again, commit again

Add a second line to `notes.md`, save, then:

```powershell
git status                       # shows: notes.md is "modified"
git diff                         # shows: which line changed
git add notes.md
git commit -m "Add second note"
git log --oneline                # now shows two commits
```

#### The three diff views

`git diff` has different modes – depending on which **two** states you want
to compare:

| Command | Compares | When useful? |
|---|---|---|
| `git diff` | Working Directory ↔ Staging Area | "What did I change but **haven't** staged yet?" |
| `git diff --staged` *(or `--cached`)* | Staging Area ↔ last commit | "What would go into the next commit?" |
| `git diff HEAD` | Working Directory ↔ last commit | "Everything that changed since the last commit (staged + unstaged)." |

> 💡 Rule of thumb: **before every commit** quickly run `git diff --staged` –
> prevents "I committed the wrong state" mistakes.

### Step 9 – Fix the last commit (`--amend`)

Typo in the commit message? Forgot to stage a file? Used the wrong author
email? As long as the commit **hasn't been pushed yet**, you can edit it
after the fact:

```powershell
# Change the message
git commit --amend -m "Better message"

# Stage the forgotten file AND add it to the previous commit
git add forgotten-file.txt
git commit --amend --no-edit   # --no-edit = keep the message unchanged

# Apply current config identity (e.g. after fixing git config)
git commit --amend --reset-author --no-edit
```

| Flag | Effect |
|---|---|
| `--amend` | "Edit the last commit" instead of making a new one |
| `--no-edit` | Keep the commit message unchanged (otherwise an editor opens) |
| `--reset-author` | Apply the current `user.name`/`user.email` from config (otherwise amend keeps the old author) |

> ⚠️ **Important rule of life:** `--amend` changes the **commit hash**. If the
> commit has already been pushed, you'll need `--force` to push – and that can
> wreck other people's history.
> **Rule of thumb: only amend commits you haven't pushed yet.**

### Step 10 – A shortcut for later

Instead of `git add` + `git commit`, for **already tracked** files you can do
it in one go:

```powershell
git commit -am "Quick change"
```

`-a` automatically stages all modified files (but **not new** ones).

---

## Cheat sheet – commands from this lab at a glance

| Skill | Command |
|---|---|
| Initialise a repo | `git init` |
| Check status | `git status` |
| Stage a file for the next commit | `git add <file>` / `git add .` |
| See changes (Working ↔ Staging) | `git diff` |
| See changes (Staging ↔ last commit) | `git diff --staged` |
| All changes since the last commit | `git diff HEAD` |
| Commit | `git commit -m "..."` |
| Stage + commit (for tracked files) | `git commit -am "..."` |
| Fix the last commit | `git commit --amend` |
| Show history | `git log` / `git log --oneline` |
| Rename branch (e.g. master → main) | `git branch -m main` |

---

## Observations

- **Without a commit, nothing is saved** – only `.git/` counts as history.
- **`git status`** is your most important command. When in doubt: run `git status` first.
- A commit **always** needs a message (`-m "…"`). Keep it short and in
  imperative form: `"Update README"` instead of `"I updated the README"`.
- The **commit hash** (e.g. `a1b2c3d`) is globally unique.
- Nothing is on **GitHub yet**. Everything lives locally. We'll change that in Lab 3.

## Common pitfalls

| Problem | Solution |
|---|---|
| `git: command not found` (Bash) or `git : The term 'git' is not recognized` (PowerShell) | Git isn't installed, or you need to open a fresh PowerShell window. |
| `Please tell me who you are` on your first commit | Set `git config --global user.name`/`user.email` as shown above. |
| `nothing to commit, working tree clean` | File hasn't been changed yet, or wasn't saved. |
| `.git/` deleted by accident | History is gone. Only the current file state remains. |
| `git status` shows `On branch master` instead of `main` | You ran `git init` before setting `git config --global init.defaultBranch main`. Fix without re-initialising: `git branch -m main`. |

## Cleanup (optional)

```powershell
# Delete the entire practice repo (including history)
cd ..
Remove-Item -Recurse -Force my-local-repo
```

> ⚠️ Better to leave the repo where it is – we'll use it in **Lab 3** to
> connect it to GitHub.

## Further reading / sources

- [Pro Git Book (free)](https://git-scm.com/book/en/v2)
- [Git Cheat Sheet (GitHub)](https://education.github.com/git-cheat-sheet-education.pdf)

---

✅ Lab 2 complete.
