# Lab 4 – Branches & Merges

## What you'll learn

- What a **branch** is and why it exists.
- How to create, switch, and delete branches with `git switch` and `git branch`.
- The difference between a **fast-forward merge** and a **three-way merge**.
- How to visualise branches with `git log --graph`.
- How to push a branch to GitHub.

---

## Why do you need this? (The aha moment)

Until now, your repo had one single line of commits. In real projects you'll
constantly want to:

- Try out an idea **without breaking the working main version**.
- Work on a **new feature** in parallel with bug fixes.
- Let other people review your changes **before** they land in `main`.

The classic non-Git way of doing this is **duplicating folders** (`project-v2`,
`project-experiment`, `project-actually-final`). That gets unmaintainable fast.

**Branches solve this elegantly:**

| | Folder duplication | Git branches |
|---|---|---|
| Storage | Full copy per attempt | Only the differences are stored |
| Switching | Open another folder | One command (`git switch`) |
| Merging back | Manually compare and copy files | One command (`git merge`) |
| History | Lost when folder is deleted | Preserved in `.git/` |
| Parallel ideas | Increasingly chaotic | Clean, separate "lanes" |

→ A branch is essentially **a movable label** that points to a commit. Creating
one is cheap, switching is cheap, throwing one away is cheap.

### Concrete benefits at a glance

| Benefit | What that means in practice |
|---|---|
| **Safe experiments** | Try a wild refactor on a branch – if it fails, throw the branch away, `main` is untouched. |
| **Parallel work** | One branch for the new feature, another for an urgent bug fix. Switch in seconds. |
| **Foundation for Pull Requests** | Pull Requests (Lab 5) are entirely built around branches. |
| **Clean history** | Each feature/fix has its own line of commits before merging back. |

---

## Prerequisites

- Lab 3 completed.
- `my-local-repo` exists locally with at least one commit and is connected to GitHub.

### Recommended one-time setup before this lab

If you've never set Git's default editor, do this now to avoid getting stuck
in Vim during merges:

```powershell
git config --global core.editor notepad
```

(Lab 2 covered the other one-time configs; this one is specifically useful
from Lab 4 onwards because merges open an editor for the commit message.)

## Files in this lab

```
lab4-branches-and-merges/
└── README.md       ← this document
```

> You work in **your existing `my-local-repo`** from Lab 2/3. No extra files
> are needed inside the lab folder.

---

## Glossary (cheat sheet)

| Term | Meaning |
|---|---|
| Branch | A movable pointer to a commit. The default branch is `main`. |
| HEAD | Marker for "where you currently are" – usually points to the tip of your active branch. |
| Fast-forward merge | The simple case: `main` hasn't moved while you worked on the branch → Git just moves the `main` pointer forward. |
| Three-way merge | Both branches advanced separately → Git combines the two and creates a **merge commit**. |
| Merge conflict | Both branches changed the same line → Git can't decide automatically. Subject of Lab 6. |

---

## Hands-on

### Step 1 – See your current branch

In PowerShell:

```powershell
cd "$env:USERPROFILE\Documents\my-local-repo"
git branch
```

**Expected output:**

```
* main
```

The `*` shows your current branch. So far we only have one – `main`.

### Step 2 – Create a new branch and switch to it

We'll build a new "greeting" feature on a separate branch.

```powershell
git switch -c feature/greeting
```

`-c` means **create** (and switch in the same step). Convention: prefix
branches with their purpose – `feature/`, `bugfix/`, `experiment/`.

**Expected output:**

```
Switched to a new branch 'feature/greeting'
```

Verify:

```powershell
git branch
```

```
* feature/greeting
  main
```

### Step 3 – Make commits on the branch

Change `notes.md`:

```powershell
"Welcome to my notes! 👋" | Add-Content notes.md
git add notes.md
git commit -m "Add greeting line"
```

Add another commit:

```powershell
"Have a great day." | Add-Content notes.md
git add notes.md
git commit -m "Add closing line"
```

### Step 4 – Switch back to `main` and see the magic

```powershell
git switch main
Get-Content notes.md
```

**Surprise:** the two new lines are **gone**. Your `notes.md` looks exactly
like it did before you created the branch.

That's the whole point of branches: each branch has its own state of every
file. Switching = Git silently updates the files in your Working Directory to
match the branch you switched to.

Switch back to see your changes again:

```powershell
git switch feature/greeting
Get-Content notes.md
```

→ The lines are back.

### Step 5 – Visualise the branch graph

```powershell
git log --oneline --graph --all --decorate
```

`--graph` draws ASCII branch lines, `--all` shows all branches, `--decorate`
shows branch/tag labels. Typical output:

```
* abc1234 (HEAD -> feature/greeting) Add closing line
* def5678 Add greeting line
* 3202e33 (origin/main, origin/HEAD, main) Add entry made directly on GitHub
* db1d89b Live-Test
...
```

Read top-down: newest commit first. Both branches share the older commits at
the bottom and diverge at the top.

### Step 6 – Merge the branch back into `main` (fast-forward)

```powershell
git switch main
git merge feature/greeting
```

**Expected output:**

```
Updating 3202e33..abc1234
Fast-forward
 notes.md | 2 ++
 1 file changed, 2 insertions(+)
```

🎯 **`Fast-forward`** – the simple, conflict-free case. Since `main` didn't
move while you worked on the branch, Git just advanced the `main` pointer to
the same commit as `feature/greeting`. No merge commit needed.

Verify:

```powershell
Get-Content notes.md           # both lines are now in main
git log --oneline --graph --all
```

### Step 7 – Delete the merged branch

A merged branch is just clutter – clean it up:

```powershell
git branch -d feature/greeting
git branch                     # → only main remains
```

`-d` (lowercase) only deletes branches that are **fully merged**. To force-delete
an unmerged branch, use `-D` (uppercase) – but only after you're sure you
don't need it.

### Step 8 – Three-way merge (when branches diverge)

This time we'll **simultaneously** change both `main` and a branch, so Git
can't just fast-forward.

> 💡 To stay **conflict-free** (conflicts are Lab 6's topic), we deliberately
> let the branches modify **different files**: the branch creates a new file
> `footer.md`, while `main` keeps appending to `notes.md`. Same-file edits
> on both sides would conflict – which you'll learn how to resolve in Lab 6.

```powershell
# 1. Make a commit directly on main (modifies notes.md)
"Direct change on main." | Add-Content notes.md
git add notes.md
git commit -m "Edit main directly"

# 2. Create a branch from this point and make a commit – in a NEW file
git switch -c feature/footer
"This is a footer file." | Set-Content footer.md
git add footer.md
git commit -m "Add footer file"

# 3. Meanwhile, make another commit on main – still notes.md
git switch main
"Another change on main." | Add-Content notes.md
git add notes.md
git commit -m "Add another main change"
```

Now `main` and `feature/footer` have **diverged** – both moved forward
independently, but in **different files**. Look at it:

```powershell
git log --oneline --graph --all
```

You'll see two diverging lines.

Merge:

```powershell
git merge feature/footer
```

**Expected output (different from fast-forward!):**

```
Merge made by the 'ort' strategy.
 footer.md | 1 +
 1 file changed, 1 insertion(+)
```

🎯 Notice: **"Merge made by …"**, not "Fast-forward". Git created a new
**merge commit** with **two parents** (one from each branch).

> 💡 **Editor may open** for the merge commit message. If notepad pops up:
> save and close. If you see `~` characters down the left side, it's Vim –
> press **`Esc`** first (you might be in insert mode), then type **`:wq`** and
> Enter to save and quit. To avoid Vim in the future:
> `git config --global core.editor notepad`.

Inspect:

```powershell
git log --oneline --graph --all
```

You see a diamond shape: the two lines come back together via a merge commit.

Clean up:

```powershell
git branch -d feature/footer
```

### Step 9 – Push a branch to GitHub

So far everything stayed local. Branches really shine when you push them so
others can see (and review) them.

```powershell
# Create a fresh branch
git switch -c feature/about-section
"## About`n`nThis is my notes repo." | Add-Content notes.md
git add notes.md
git commit -m "Add About section"

# Push it – -u sets the upstream for this branch
git push -u origin feature/about-section
```

**Expected output:**

```
...
To https://github.com/.../my-local-repo.git
 * [new branch]      feature/about-section -> feature/about-section
branch 'feature/about-section' set up to track 'origin/feature/about-section'.
```

Now check GitHub in your browser:
- Repo page → branch dropdown (top left, says `main`) → you'll see `feature/about-section`.
- GitHub will also show a yellow banner: **"feature/about-section had recent pushes – Compare & pull request"**. That's the entry point for Lab 5.

For now: don't open a PR yet, we'll do that in Lab 5. Just admire that your
branch is on the internet.

---

## Cheat sheet – commands from this lab at a glance

| Skill | Command |
|---|---|
| List local branches | `git branch` |
| List all branches (incl. remote) | `git branch -a` |
| Create and switch to new branch | `git switch -c <name>` |
| Switch to existing branch | `git switch <name>` |
| Merge a branch into the current one | `git merge <name>` |
| Delete a merged branch | `git branch -d <name>` |
| Force-delete a branch | `git branch -D <name>` |
| Visualise branches | `git log --oneline --graph --all --decorate` |
| Push a new branch with upstream | `git push -u origin <name>` |
| Delete a remote branch | `git push origin --delete <name>` |

---

## Observations

- A branch is **cheap** – creating one is essentially "set a label on this commit".
- **HEAD** always points to your current location. After `git switch X`, HEAD points to the tip of X.
- A **fast-forward merge** doesn't create a merge commit; it just moves the pointer.
- A **three-way merge** creates a merge commit with **two parents**.
- Switching branches is forbidden when you have **uncommitted changes that would be overwritten** – Git protects you. Commit or stash first.
- The branch convention `feature/x`, `bugfix/y`, `experiment/z` makes the purpose visible at a glance.

## Common pitfalls

| Problem | Solution |
|---|---|
| `error: Your local changes to the following files would be overwritten by checkout` | Commit your changes, or stash them with `git stash`, then switch. |
| `error: The branch 'feature/x' is not fully merged` when deleting | The branch has commits not in `main`. Use `-D` (force) if you really want to discard, or merge it first. |
| `git switch` not found | Your Git is older than 2.23 (2019). Update Git for Windows, or use the old equivalent `git checkout <branch>` / `git checkout -b <newbranch>`. |
| Editor pops up during merge and won't close | It's likely Vim. Press **`Esc`** first (in case you typed text into the file by accident), then `:wq!` + Enter to force-save and quit. To avoid Vim entirely: `git config --global core.editor notepad`. |
| Merge unexpectedly says `CONFLICT (content)` | Both branches edited the same area of the same file. That's a merge conflict – covered in Lab 6. To bail out: `git merge --abort`. |
| Pushed a branch but GitHub doesn't show "Compare & pull request" | Refresh the page – the banner appears for ~1 hour after a push. Or open the branch manually and click "Contribute" → "Open pull request". |

## Cleanup (optional)

If you pushed `feature/about-section` and want to remove it again from GitHub:

```powershell
# Delete the remote branch
git push origin --delete feature/about-section

# Delete the local branch too (use -D since it's not merged into main)
git branch -D feature/about-section
```

We'll continue in **Lab 5** with Pull Requests – there you'll see the proper
workflow for merging branches into `main`. So feel free to keep your
`my-local-repo` as is.

## Looking ahead: Pull Requests instead of direct merges

In this lab we merged branches **directly into `main`** via `git merge`. In
real teams that's almost never how it's done. Instead:

1. Work on a feature branch (just like here).
2. Push the branch to GitHub.
3. Open a **Pull Request**.
4. A colleague reviews the changes, comments, requests changes if needed.
5. Only after approval, the PR is merged via the GitHub UI (which under the
   hood does `git merge` for you).

That's the entire content of Lab 5. The branch work you just learned is the
foundation for it.

## Further reading / sources

- [Git Docs – Branching](https://git-scm.com/book/en/v2/Git-Branching-Branches-in-a-Nutshell)
- [GitHub Docs – About branches](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/about-branches)

---

✅ Lab 4 complete.
