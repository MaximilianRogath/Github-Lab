# Lab 5 – Pull Requests

## What you'll learn

- What a **Pull Request (PR)** is and why teams use it instead of merging directly.
- How to open, describe, and review a PR on GitHub.
- How to merge a PR via the GitHub UI.
- How to sync your local repo after a merge and clean up the branch.
- What **branch protection rules** are and why they exist.

---

## Why do you need this? (The aha moment)

In Lab 4 you merged branches **directly** with `git merge`. That works fine
when you're working alone. But in a team, direct merges into `main` are
almost always a bad idea:

| | Direct merge into `main` | Pull Request |
|---|---|---|
| Anyone can review the change first? | ❌ it's already in | ✅ before merging |
| Change is visible and discussable? | ❌ only in `git log` | ✅ dedicated discussion thread |
| Easy to roll back if broken? | 🟡 possible but awkward | ✅ just close the PR |
| Audit trail ("who approved what")? | ❌ | ✅ built in |
| Works with branch protection? | ❌ blocked by rules | ✅ the intended path |

→ A Pull Request is not just a merge – it's a **conversation** around a
proposed change. Code review, discussion, and approval all happen before a
single line lands in `main`.

### Concrete benefits at a glance

| Benefit | What that means in practice |
|---|---|
| **Catch bugs early** | A second pair of eyes spots what you missed. |
| **Knowledge sharing** | Reviewers learn about the change; authors learn from feedback. |
| **Clear history** | Every merge in `main` has a PR number, title, and discussion thread. |
| **Safe experimentation** | Work on a branch freely; `main` stays stable until the PR is approved. |
| **Automation triggers** | CI/CD pipelines (tests, linters, deployments) typically run on PR events. |

---

## Prerequisites

- Lab 4 completed.
- `feature/about-section` branch pushed to GitHub (done at the end of Lab 4).
- You're signed in to github.com.

## Files in this lab

```
lab5-pull-requests/
└── README.md       ← this document
```

> The PR workflow happens entirely in the **GitHub browser UI** for Steps 1–6.
> Step 7 is back in the terminal.

---

## Glossary

| Term | Meaning |
|---|---|
| Pull Request (PR) | A request to merge changes from one branch into another, with a built-in review process. |
| Base branch | The branch you want to merge **into** (almost always `main`). |
| Compare branch | The branch that contains your changes (e.g. `feature/about-section`). |
| Reviewer | A person asked to look at the code and approve or request changes. |
| Merge commit | The commit that is created when a PR is merged (similar to `git merge`). |
| Branch protection rule | A GitHub setting that prevents direct pushes to a branch and requires PRs (and optionally approvals) before merging. |

---

## Hands-on

### Step 1 – Open the Pull Request

1. Go to your repo on github.com.
2. GitHub likely shows a **yellow banner** at the top:
   *"feature/about-section had recent pushes – Compare & pull request"* → click it.
3. If the banner is gone (it expires after ~1 hour):
   - Click the **branch dropdown** (top left, says `main`).
   - Select `feature/about-section`.
   - Click **"Contribute"** → **"Open pull request"**.

You land on the **"Open a pull request"** form.

### Step 2 – Fill in the PR form

| Field | What to put |
|---|---|
| **Title** | `Add About section to notes` |
| **Description** | A brief explanation of what you changed and why. Think of the reviewer: what do they need to know? |
| **Reviewers** *(optional)* | Leave empty for now – you're working solo. In a team you'd tag a colleague here. |
| **Base branch** | `main` ← where the changes should land |
| **Compare branch** | `feature/about-section` ← your changes |

**Good PR description example:**

```
## What

Added an About section to notes.md.

## Why

Makes the file more readable by providing context at the top.

## How to review

Check notes.md – the new lines should appear at the end.
```

Click **"Create pull request"**.

### Step 3 – Explore the PR page

Spend 30 seconds on the PR page and find:

| Tab/Section | What it shows |
|---|---|
| **Conversation** | The description + any comments – this is where discussion happens. |
| **Commits** | All commits on the branch that are part of this PR. |
| **Files changed** | The diff – green = added, red = removed. This is what reviewers look at. |

Click **"Files changed"** and look at the diff. You'll see:
- The exact lines that will be added to `notes.md`.
- A `+` sign next to every added line.

> 💡 In real teams, reviewers go through "Files changed" line by line.
> You can click the **`+` icon** next to any line to leave an inline comment.

### Step 4 – Leave a review comment (solo simulation)

Even though you're the author and reviewer here, practice the flow:

1. In the **"Files changed"** tab, hover over one of the added lines.
2. Click the **blue `+` icon** that appears on the left.
3. Write a comment, e.g. `Looks good! 👍`
4. Click **"Comment"** to post it immediately as a single inline comment.

> 💡 You'll also see a **"Start a review"** button. That's for batching multiple
> comments before submitting them all at once – useful in real reviews so the
> author doesn't get pinged for every single line. For now, **"Comment"** is fine.

You'll see the comment appear in the **Conversation** tab. In a real team,
reviewers submit their batch via **"Start a review"** and mark the whole thing
as "Approved", "Comment", or "Request changes" at the end.

### Step 5 – Merge the PR

1. Go back to the **Conversation** tab.
2. Scroll to the bottom → green **"Merge pull request"** button.
3. Click it → **"Confirm merge"**.

**What just happened:**
- GitHub ran a three-way merge under the hood (same as `git merge`).
- A new **merge commit** was created in `main`.
- The PR is now marked as **"Merged"** (purple badge).

You should see: *"Pull request successfully merged and closed."*

GitHub also offers: **"Delete branch"** → click it. The remote
`feature/about-section` branch is now gone from GitHub.

### Step 6 – Check the result on GitHub

Click on **"Code"** → you're back on the repo home page.
- The `main` branch now contains the About section.
- In the **commit history**, you'll see the merge commit with the PR number
  (e.g. `Merge pull request #1 from MaximilianRogath/feature/about-section`).
- The PR number is a **permanent link**: `github.com/<user>/<repo>/pull/1`.

### Step 7 – Sync your local repo

The merge happened on GitHub. Your local `main` doesn't know about it yet.

```powershell
cd "$env:USERPROFILE\Documents\my-local-repo"

# Switch to main
git switch main

# Pull the merge commit from GitHub
git pull

# Verify: merge commit is now in your local history
git log --oneline --graph

# Clean up the local branch (it was already deleted on GitHub in Step 5)
git branch -d feature/about-section
```

> 💡 If `git branch -d` says "not fully merged" – use `-D` (force). This
> can happen when the merge was done on GitHub and your local branch doesn't
> know about it. The merge is already in `main`, so it's safe to force-delete.

### Step 8 – The full PR workflow in one picture

```
 local machine                          github.com
 ─────────────                          ──────────
 git switch -c feature/x                
 (work, commit, commit)                 
 git push -u origin feature/x  ──────► branch appears on GitHub
                                        Open Pull Request
                                        Review → Approve
                                        Merge PR (GitHub UI)
 git switch main               ◄──────
 git pull                       ◄────── merge commit arrives locally
 git branch -d feature/x
```

---

## Cheat sheet – PR workflow at a glance

| Step | Where | What |
|---|---|---|
| Create branch + work | Local | `git switch -c feature/x` → commit |
| Push branch | Local | `git push -u origin feature/x` |
| Open PR | GitHub UI | "Compare & pull request" |
| Review + Approve | GitHub UI | "Files changed" → inline comments → "Approve" |
| Merge | GitHub UI | "Merge pull request" → "Confirm merge" |
| Sync locally | Local | `git switch main` → `git pull` |
| Delete local branch | Local | `git branch -d feature/x` |
| Delete remote branch | GitHub UI | "Delete branch" after merge (or `git push origin --delete feature/x`) |

---

## Observations

- A PR is **not a Git concept** – it's a GitHub (and GitLab/Bitbucket) feature
  built on top of Git branches. `git` itself has no `git pull-request` command.
- The PR page's **"Files changed"** tab is the single most important view for
  code review. Keep your PRs small so reviewers can actually read it.
- **PR size matters:** a 5-file, 50-line PR gets reviewed thoroughly. A
  500-line PR gets a "LGTM 👍" and rubber-stamped.
- **Commit messages** matter twice: once for `git log`, and once as the PR
  title suggestion when you open the PR.
- Merging on GitHub and then `git pull` locally is **the normal daily workflow**
  in most professional teams.

## Common pitfalls

| Problem | Solution |
|---|---|
| Yellow "Compare & pull request" banner is gone | Click the branch dropdown → select the branch → "Contribute" → "Open pull request". |
| "Merge pull request" button is grey / blocked | Branch protection is active and requires at least one approval. In a solo repo you can disable this in Settings → Branches. |
| Local `main` still shows old state after merge | Run `git pull` – your local repo doesn't update automatically. |
| `git branch -d feature/x` says "not fully merged" | The merge happened on GitHub. Use `-D` to force-delete – the content is safe in `main`. |
| PR shows merge conflicts | Both `main` and your branch edited the same lines. Resolve locally: `git switch feature/x` → `git merge main` → fix conflicts → commit → push → the PR updates automatically. |

## Branch protection rules (a look ahead)

In real teams, `main` is almost always **protected**. That means:

- Direct `git push` to `main` is **blocked** by GitHub.
- Every change must go through a PR.
- The PR typically requires **at least one approval** from a teammate.
- Optionally: **CI checks** (automated tests) must pass before merging.

You can set this up yourself: repo → **Settings → Branches → Add rule** →
branch name `main` → tick "Require a pull request before merging".

For a solo learning repo it's optional, but it's worth knowing this is how
most professional repos are configured.

## Further reading / sources

- [GitHub Docs – About pull requests](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/about-pull-requests)
- [GitHub Docs – Reviewing changes in pull requests](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/reviewing-changes-in-pull-requests)

---

✅ Lab 5 complete.
