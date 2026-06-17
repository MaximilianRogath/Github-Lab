# Quickstart – Get your project on GitHub in 15 minutes

> For everyone who already built something (vibe-coded scripts, quick hacks,
> small apps) and just wants to **share it cleanly, fast**. No concept-heavy
> intro, just the necessary steps.
> If you want to learn Git/GitHub from scratch → [Lab series](../lab1-hello-github/).

---

## Checklist – what needs to be in your repo before you share?

| ✅ | File | Why? |
|---|---|---|
| ☐ | **`.gitignore`** | Prevents junk (caches, virtual envs, build artefacts) and especially **secrets** from being committed. |
| ☐ | **`README.md`** | What does the project do? How do you run it? Nobody uses what they don't understand. |
| ☐ | **`requirements.txt`** (Python) or **`package.json`** (Node) | Says: "These are the packages you need." Without it, nobody else can run your project. |
| ☐ | **`.env.example`** | Shows **which** environment variables your project needs (without real values). |
| ☐ | **`LICENSE`** | **Without a license, strictly speaking nobody is allowed to use your project.** MIT for permissive, Apache-2.0 if you care about patents. |
| ☐ | Secrets removed | API keys, passwords, tokens, private URLs – **none of it** in commits. Put them in `.env` (local) and document them in `.env.example` (in the repo). |

---

## ⚠️ Things you must NEVER commit

- **A real `.env` with values** – only `.env.example` with empty placeholders.
- **API keys, tokens, passwords** – not even "just for a quick test".
- **Model files > 100 MB** – GitHub rejects large files. For ML models: [Git LFS](https://git-lfs.com/).
- **`__pycache__/`, `node_modules/`, `.venv/`** – all of this belongs in `.gitignore`.
- **Personal data** (email lists, customer exports, etc.).

---

## Templates (to copy into your project)

In the [`templates/`](./templates/) folder you'll find ready-to-use templates:

| File | What it is | Drop into your project as |
|---|---|---|
| [`templates/.gitignore`](./templates/.gitignore) | Python defaults | `.gitignore` |
| [`templates/.gitignore-node`](./templates/.gitignore-node) | Node.js defaults | `.gitignore` *(rename!)* |
| [`templates/README-template.md`](./templates/README-template.md) | Minimal README with What/Install/Usage | `README.md` |
| [`templates/requirements-example.txt`](./templates/requirements-example.txt) | Example with version pinning | `requirements.txt` |
| [`templates/.env.example`](./templates/.env.example) | Secrets declaration without values | `.env.example` *(keep as is)* |
| [`templates/LICENSE`](./templates/LICENSE) | MIT license with placeholders | `LICENSE` *(fill in year + name)* |

### Copy one template into your project (PowerShell)

Example – pull the Python `.gitignore` into your current project folder:

```powershell
cd C:\path\to\your-project
Copy-Item "C:\Users\<YOU>\…\Github-Lab\_quickstart\templates\.gitignore" .
```

> 💡 For Node projects: copy `.gitignore-node` and **rename it locally** to `.gitignore`.

---

## Push workflow – the 7 commands

Once the checklist is complete:

```powershell
# 1. Go into your project folder
cd C:\path\to\your-project

# 2. Initialise the local Git repo
git init

# 3. Stage all files (make sure .gitignore is in place first!)
git add .

# 4. First commit
git commit -m "Initial commit"

# 5. Create an empty repo on github.com (NO README/gitignore/License checkbox)
#    → copy the URL

# 6. Connect the remote
git remote add origin https://github.com/<YOUR-USERNAME>/<REPO-NAME>.git

# 7. Push + set upstream
git branch -M main
git push -u origin main
```

**Quick explanation per step:**

| Command | What it does |
|---|---|
| `git init` | Turns the folder into a Git repository (creates `.git/`). |
| `git add .` | Records all files (except `.gitignore` exclusions) for the next commit. |
| `git commit -m "..."` | Freezes the current state with a message in the local history. |
| `git remote add origin <url>` | Connects your local repo to GitHub. |
| `git branch -M main` | Makes sure your branch is called `main` (idempotent). |
| `git push -u origin main` | Sends everything to GitHub. `-u` remembers the link – from then on, plain `git push` is enough. |

> 📚 **Deeper understanding** of each command:
> [Lab 2 – Use Git locally](../lab2-first-local-repo/) (commit, staging, history) and
> [Lab 3 – Connect a remote](../lab3-connect-remote/) (push, pull, remote concepts).

---

## Clone & run – get someone else's project running locally

Someone sent you a GitHub link and you want to run it on your machine?
This is the counterpart to the push workflow above.

### Step 1 – Clone the repo

```powershell
git clone https://github.com/<USERNAME>/<REPO-NAME>.git
cd <REPO-NAME>
```

`git clone` downloads the full repo (history included) and configures `origin`
automatically – no `git init` or `git remote add` needed.

### Step 2 – Install dependencies (Python)

```powershell
# Create and activate a virtual environment first (keeps your system clean)
python -m venv .venv
.venv\Scripts\activate          # Windows
# source .venv/bin/activate     # macOS/Linux

pip install -r requirements.txt
```

No `requirements.txt`? Ask the author to add one, or run `pip install <package>`
for each import you see at the top of the code.

### Step 3 – Set up secrets / environment variables

```powershell
# Copy the example file and fill in real values
copy .env.example .env
notepad .env                    # or open in VS Code: code .env
```

No `.env.example`? Ask the author – or look in the README / code for any
`os.getenv(...)` calls to see which variables are expected.

### Step 4 – Run it

```powershell
python main.py                  # or whatever the entry point is
```

Check the README for the exact run command if unsure.

### Stay up to date

When the author pushes new changes, pull them:

```powershell
git pull
```

If new packages were added:

```powershell
pip install -r requirements.txt
```

### If it doesn't work after cloning – checklist

| Symptom | Likely cause | Fix |
|---|---|---|
| `ModuleNotFoundError` | Virtual env not activated or `pip install` not run | Activate `.venv`, then `pip install -r requirements.txt` |
| `KeyError` / `None` for an env var | `.env` not created or missing a value | Check `.env.example`, fill in `.env` |
| `python: command not found` | Python not installed or wrong version | Install from python.org or use `python3` |
| Code runs but crashes with a path error | Hardcoded absolute paths in the code | Open an issue or PR on the repo (see Lab 9) |

> 📚 **Deeper understanding:** [Lab 3 – Connect a remote](../lab3-connect-remote/) explains `clone`, `pull`, and remotes in detail.

---

## Vibe-code specifics (ChatGPT / Copilot / Claude generated code)

If your code came from an LLM, there are a few typical traps to check **before
your first push**:

| Risk | What to do |
|---|---|
| **Hardcoded API keys in code** | LLMs often suggest `openai.api_key = "sk-..."` directly in code. Remove immediately → put it in `.env`, read it in code with `os.getenv("OPENAI_API_KEY")`. |
| **Example paths from the prompt** | `C:\Users\maxmustermann\Documents\...` → replace with relative paths or env vars. |
| **Invented libraries** | LLMs sometimes hallucinate package names. Before `pip install`, check that the package really exists on [PyPI](https://pypi.org/). |
| **Outdated code patterns** | LLMs train on old code. For security-relevant things (auth, SQL), **always** cross-check against the official docs. |
| **Personal data from the prompt** | Did you paste real data into the prompt? It might show up verbatim in the code → before committing, grep the code for email addresses, names, internal URLs. |
| **No license, no author** | LLM code has no license info by default – set one yourself (see the `LICENSE` template). |

> 🔍 **Quick self-check before pushing:**
> ```powershell
> # Searches all files for anything suspicious
> Select-String -Path *.py,*.js,*.ts,*.env -Pattern "sk-|api_key|password|secret|token" -CaseSensitive:$false
> ```
> If anything shows up → fix it before committing.

---

## (Optional) Bonus: Dockerize your project

If your project is more than a 50-line throwaway script, a Dockerfile helps
against "works on my machine" problems. There's a dedicated lab series for it:

→ [**Docker-Lab series**](https://github.com/MaximilianRogath/Docker-Lab)
*(link active once the repo is published – 404 means it isn't live yet)*

---

## When something goes wrong

| Problem | Where to look |
|---|---|
| Push is rejected | [Lab 3 → Common pitfalls](../lab3-connect-remote/#common-pitfalls) |
| Wrong email in commits | [Lab 2 → Privacy setup](../lab2-first-local-repo/#one-time-git-configuration) |
| Typo in commit message | [Lab 2 → `--amend`](../lab2-first-local-repo/#step-9--fix-the-last-commit-amend) |
| Branch is called `master` instead of `main` | [Lab 2 → Common pitfalls](../lab2-first-local-repo/#common-pitfalls) |

---

✅ Done. Your repo is on GitHub. Share the link.
