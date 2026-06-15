# Lab 1 – Hello GitHub

## What are Git and GitHub?

**Git** is a **version control system**: a tool that records every change to
your files, so you can jump back in time, compare versions, or work on multiple
versions in parallel. Git runs **entirely locally** on your machine.

**GitHub** is an **online platform** where Git projects are stored, shared, and
worked on collaboratively. On top of that, it offers features like Issues
(tasks), Pull Requests (code reviews), and Actions (automation).

> In short: **Git = the tool**, **GitHub = the place where the world writes code together.**

**Typical workflow:**
1. Create a repository (= project folder) on GitHub.
2. Clone it locally, make changes, commit them.
3. Upload the changes to GitHub (`push`).
4. Others can view, copy, and improve the project.

> Source: [GitHub Docs – Hello World](https://docs.github.com/en/get-started/quickstart/hello-world) (retrieved June 12, 2026)

---

## Why do I need this? (The aha moment)

> 💬 *"I overwrote that file last week and now I need the old version back..."*

**Without Git/GitHub** (classic file-based workflow):

```
👩‍💻 You're working on a project. You constantly save "v1", "v2", "final", "final_new".
📁 12 copies of the same document in one folder, nobody knows which is the current one.
🤝 Collaboration via email → everyone ends up with a different version.
🔥 Hard drive dies → everything is gone.
```

**With Git and GitHub:**

```
👩‍💻 Every "save point" (commit) is traceable – with a comment.
🕒 Any old version can be restored at any time.
☁️  Code lives safely on GitHub – broken laptop = no data loss.
🤝 Colleagues work in parallel without overwriting each other.
```

### Concrete benefits at a glance

| Benefit | What that means in practice |
|---|---|
| **Full history** | Every change recorded – with author, date, and comment. |
| **Backup in the cloud** | Repository lives on GitHub → broken laptop = no data loss. |
| **Collaboration** | Multiple people work in parallel; conflicts surface explicitly. |
| **Code review** | Pull Requests let changes be reviewed before they're merged. |
| **Portfolio** | Public repos show recruiters or clients what you can do. |
| **Automation** | GitHub Actions builds, tests, and deploys your project automatically. |

### Typical use cases for beginners

- **Save and version your own projects** safely.
- **Collaborate with colleagues** on a shared codebase.
- **Try out, fork, and contribute to** open-source projects.
- **Version-control documentation and notes** (like this lab itself).
- **Build a portfolio** for job applications.

### Where Git/GitHub is NOT the right choice

- Pure binary files (large videos, huge image libraries) → use Git LFS or cloud storage instead.
- Confidential secrets (passwords, API keys) **do NOT** belong in a repo.
- One-off throwaway scripts – Git is overkill.

---

**Goal of this lab:** Create your first repository on GitHub, edit a README via
the web editor, and understand your first commit.

---

## Glossary (cheat sheet)

| Term | Meaning | Analogy |
|---|---|---|
| Repository (repo) | Project folder with full history | Filing cabinet with version log |
| Commit | Saved snapshot with a comment | Save point in a video game |
| Branch | Parallel development line | Fork in a road |
| README | Front-page document of a repo (Markdown) | Cover page of a folder |
| Markdown | Lightweight markup language (`.md`) | Word-style formatting with the keyboard |

---

## Prerequisites

- You have a free GitHub account at https://github.com/.
- You're signed in.

## Hands-on

### Step 1 – Create a new repository

1. Top right, click the **`+`** icon → **"New repository"**.
2. **Repository name:** `my-first-repo`
3. **Description (optional):** `My first test repository`
4. Select **Public** (it's public – no big deal, it's just practice).
5. Check **"Add a README file"**.
6. Click **"Create repository"** at the bottom.

Done: you'll see your new repo with a `README.md`.

### Step 2 – Edit the README in the web editor

1. Click on the **`README.md`** file.
2. Top right, click the **pencil icon** ("Edit this file").
3. Add a sentence, for example: `Hi, I'm Max Mustermann and this is my first repo on GitHub! 🚀`
4. Top right, click **"Commit changes…"**.
5. **Commit message:** `Add greeting to README`
6. Confirm with **"Commit changes"**.

### Step 3 – Look at the commit history

1. On the repo home page, click **"Commits"** (the clock icon or the commit counter).
2. You'll now see **two commits**:
   - `Initial commit` – created automatically when you set up the repo.
   - `Add greeting to README` – your own commit.

Click your commit → GitHub shows you **exactly which lines changed**
(green = added, red = removed). This view is called a **diff**.

### Step 4 – Learn the Markdown building blocks

Edit the README once more. Here's a small overview – try each block in the
editor and switch to the **Preview** tab to see what happens.

#### Cheat sheet

| Markdown syntax | Result |
|---|---|
| `# Heading 1` | Largest heading |
| `## Heading 2` | One level smaller |
| `**bold**` | **bold** |
| `*italic*` | *italic* |
| `- Item` | Bullet list |
| `1. Item` | Numbered list |
| `[Text](https://example.com)` | Linked text |
| `` `code` `` | `inline code` |
| ` ```language ` … ` ``` ` | Code block (with syntax highlighting) |
| `> Quote` | Indented quote block |
| `---` | Horizontal divider |

#### Example to copy-paste

```markdown
# My first repo

Hi, I'm **Max Mustermann** and this is my first repo on GitHub! 🚀

## What am I learning right now?

- How to create a repository
- How to write Markdown
- How a commit works

## Useful links

- [GitHub Docs](https://docs.github.com)
- [Markdown Cheat Sheet](https://www.markdownguide.org/cheat-sheet/)

> Status: 🟢 Learning the basics.
```

> 💡 **Most important Markdown rule:** Always put a **blank line** between a
> paragraph and a list or heading – otherwise GitHub won't render them correctly.

Save with a meaningful commit message, e.g.
`Expand README with learning goals and links`.

---

## Observations

- Every commit has an **author, timestamp, and message** – a complete audit trail.
- Even a single changed line is a full commit.
- The README is **automatically rendered** on the repo home page (Markdown → HTML).
- Your repo has its own URL: `https://github.com/<your-name>/my-first-repo` – you can share it.
- You haven't written **a single line locally**. Everything happened in the browser.

## Common pitfalls

| Problem | Solution |
|---|---|
| "Commit changes" button is greyed out | Nothing was changed – type something in the file. |
| Markdown looks weird in Preview | Leave a blank line between paragraphs; lists need `- ` (dash + space). |
| You wanted "Private" – everything is now public | Settings → "Change repository visibility" → Private. |

## Cleanup (optional)

If you ever want to get rid of the practice repo:

1. Repo → **Settings** → scroll all the way down.
2. **"Delete this repository"** → type the name → confirm.

> ⚠️ Deletion is **permanent**. `my-first-repo` was a standalone lab and is not
> used again in later labs – you can delete it or keep it as a small memento.
> Lab 2 and Lab 3 work with a completely new local repo.

## Further reading / sources

- [GitHub Docs – Hello World](https://docs.github.com/en/get-started/quickstart/hello-world)
- [Markdown Cheat Sheet](https://www.markdownguide.org/cheat-sheet/)

---

✅ Lab 1 complete.
