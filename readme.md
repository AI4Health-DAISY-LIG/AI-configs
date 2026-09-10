This document explains how to install, configure, and use **Aider** with the **Albert API** models (DINUM/Etalab) for this project, taking into account the fact that some of the data handled is **confidential**.

The folder contains the files: Gitignore/env/3 .yml files in the config folder. This folder contains several ready-to-use Aider configuration files giving the possibility to use different models and settings by just configuring different files.

## Files

| File | Model | Use case |
|---|---|---|
| `qwen-precise.yml` | Qwen3-Coder, temperature 0.0 | Reliable code editing, bugfixes, refactoring |
| `qwen-creative.yml` | Qwen3-Coder, temperature 0.7, architect mode | Thinking through complex changes |
| `mistral-default.yml` | Mistral-Small, temperature 0.5 | Comparison / general-purpose use |

---

## 1. Aider Configs

### Introduction to Albert API

Albert API is the sovereign AI infrastructure made available by DINUM (Direction interministérielle du numérique). It is favored here because:

- It is hosted in a sovereign environment (SecNumCloud certified via Outscale).
- No data sent is reused for training, nor retained beyond the processing of the request.
- Suited to sensitive data.

### Installing Aider

```
python -m pip install aider-install
aider-install
```

This command installs Aider in an isolated environment (`~/.local/bin`) via `uv`, independently of any project venv.

Check:
```
which aider
aider --version
```

### Getting an Albert API key

**In order to be able to use an Albert model you need an API key that you can get following the next steps:**

1. Request access at: `https://albert.sites.beta.gouv.fr/access/` (reserved for French public agents). Approval delay: a few hours, up to 24h.
2. Log into the Playground: `https://albert.playground.etalab.gouv.fr/`
3. Generate/copy your personal API key from the Playground.

⚠️ This key is **personal and confidential** — never share it, commit it, or display it in a file tracked by Git.

### Configuring the `.env` file

The `.env` file contains the real API key and **must never be pushed to GitHub**.

Create `.env` at the project root:
```
# .env — NEVER COMMIT THIS FILE
OPENAI_API_BASE=https://albert.api.etalab.gouv.fr/v1
OPENAI_API_KEY=your_albert_key_here
```

### Making sure `.env` is properly ignored by Git

```
echo ".env" >> .gitignore
git status   # .env must NOT appear among the files to be committed
```
If `.env` is already tracked by mistake:
```
git rm --cached .env
```

### Configuring Aider

Explicit launch (if you need to force the model):
```
aider --model openai/Qwen/Qwen3-Coder-30B-A3B-Instruct
```

Check that the key works before launching Aider:
```
curl https://albert.api.etalab.gouv.fr/v1/models -H "Authorization: Bearer $OPENAI_API_KEY"
```
A JSON response listing the models confirms everything is working well.

### Everyday usage

1. Launch Aider normally — the `.aider.conf.yml` config applies automatically
```bash
aider
```
2. Check that architect mode is indeed active
At startup you should see something like:
```
Main model: openai/Qwen/Qwen3-Coder-30B-A3B-Instruct with architect edit format
Editor model: openai/Qwen/Qwen3-Coder-30B-A3B-Instruct
```
3. Make a change request
With `architect: true`, the flow becomes two-step:
```
> add a function
```
- The architect model first answers in natural language: explains its plan, without touching the code.
- Since `auto-accept-architect: false`, Aider asks for confirmation:
```
Edit the files? (Y)es/(N)o
```
- If you accept, the editor model generates the actual diff and applies it.

You can see the reasoning before anything is modified.

4. Temporarily switch models during the session
Without changing your permanent config:
```
/model openai/mistralai/Mistral-Small-3.2-24B-Instruct-2506
```

### How to launch a given config

```bash
aider -c configs/qwen-precise.yml
aider -c configs/qwen-creative.yml
aider -c configs/mistral-default.yml
```

- Aider looks for things starting with `/`, like `/add`, `/drop`, `/model`. If you want to run an actual shell command from inside Aider, prefix it with `!`:
```
> !aider --version
> /tokens
```
- Fully exit Aider: Press Ctrl+C twice in a row, quickly

- Set the Albert env vars again (in this same terminal, before launching Aider)
```bash
export OPENAI_API_BASE="https://albert.api.etalab.gouv.fr/v1"
export OPENAI_API_KEY="your_albert_key"
```

### Auto-loaded conventions

```yaml
read:
  - .aider.convention.md
```
means that at every launch, Aider automatically injects the content of this file into the context — useful for enforcing a code style without having to repeat it in every prompt.

### Daily usage

```bash
aider
```

Useful commands in the Aider REPL:

| Command | Effect |
|---|---|
| `/add <file>` | Adds a file to the chat context |
| `/drop <file>` | Removes a file from the context |
| `/tokens` | Shows current token usage (run it first, on a clean prompt) |
| `/model <name>` | Switches models during the session |
| `/chat-mode architect` | "Architect" mode for complex changes |
| `!<command>` | Runs a shell command from inside Aider |

### Troubleshooting (common errors)

| Error | Likely cause | Solution |
|---|---|---|
| `command not found: aider` | `~/.local/bin` missing from PATH in the current session | `source ~/.zshenv` or a new terminal |
| `429 - rate limit exceeded` | Context too large (e.g. notebook with outputs) | Clean the notebook, reduce `--map-tokens`, check `/tokens` |
| `403 - Invalid API key` / `Not authenticated` | Key missing, wrongly exported, or a local `.env` file overriding the shell variable | Check `echo $OPENAI_API_KEY`, test with `curl`, check the `.env` file |
| Aider uses gpt-4o instead of the Albert model | `OPENAI_API_BASE` not defined in the session, or no model specified | Re-export both variables, relaunch with `--model openai/Qwen/...` |
| Invalid command in the Aider REPL | Shell command typed without the `!` prefix | Use `!<command>` inside Aider |

### Handling large files (notebooks, tokens)

Jupyter notebooks (`.ipynb`) often embed cell outputs (plots, results), which can blow up the number of tokens sent and trigger a `429 rate limit` error.

**Solution — clean the outputs before adding the notebook to Aider:**
```bash
jupyter nbconvert --clear-output --to notebook \
  --output workflows/my_notebook_clean.ipynb \
  workflows/my_notebook.ipynb
```
The original file stays intact; only the lightened version is added to Aider:
```
/add workflows/my_notebook_clean.ipynb
```

### Confidential data

- Never `/add` a file containing raw or identifying patient/subject data.
- Always check `/tokens` and the context content before sending a request.
- The `.env` stays **local only**.
- The GitHub repo stays **private** as long as the project isn't meant for publication.
- Make sure no API key appears in the files.

---

## 2. Model / Parameter Comparison

### The "agents" in Aider = model roles

Aider distinguishes several roles, each of which can use a different model:

| Role | What it does |
|---|---|
| **Main model** | Understands the request, thinks about the solution |
| **Architect** (if enabled) | Thinks about the change strategy without writing the code directly |
| **Editor** | Translates the architect's reasoning into the diff/code actually applied to the file |

| Parameter | Definition | Concrete effect | Typical values |
|---|---|---|---|
| **temperature** | Controls the degree of randomness in choosing the next word/token | Low = deterministic, predictable, repetitive responses. High = more varied, creative responses, sometimes less reliable. | `0.0` → deterministic (ideal for precisely editing code). `0.7` → balanced (good for reasoning/explaining). `1.0+` → very creative, risky for code. |
| **top_p** (nucleus sampling) | Only considers tokens whose cumulative probability reaches this threshold | Low = very restricted choice, limited to the most probable options. High = more vocabulary/structure diversity. | `0.1` → very restrictive, near-deterministic. `0.8-0.9` → moderate diversity, common usage. `1.0` → no restriction. |
| **top_k** | Only considers the K most probable tokens at each step | Low = limited choice, more predictable responses. High = more possible variety. | `1` → equivalent to greedy decoding (always the most probable token). `20-40` → good common compromise. `100+` → wide diversity. |
| **max_tokens** *(optional, not in your file)* | Limits the number of tokens generated in the output | Avoids truncated or, conversely, overly long/costly responses. | `4096`, `8192` depending on the task. |
| **reasoning_effort** *(for models with reasoning, e.g. o1/gpt-oss)* | Controls the depth of internal reasoning before answering | Higher = better quality but slower/more costly. | `low`, `medium`, `high` depending on the model. |

### Edit format (diff vs whole)

- **Diff edit format**: the model sends only the changed lines/hunks — much more token-efficient, especially on large files.
- **Whole edit format**: the model re-sends the **entire file content** for every edit — which will burn through your token budget fast.
```bash
aider --model openai/Qwen/Qwen3-Coder-30B-A3B-Instruct --edit-format diff
```

- `"architect: true"` separates the reasoning (model) from the code writing (editor-model). The model explains its plan before touching the code.
- `"auto-accept-architect: false"` = Aider asks for confirmation before applying the changes proposed by the architect. Set to `"true"` to apply automatically (not recommended with confidential data / sensitive code).

### Typical combinations by use case

| Usage | temperature | top_p | top_k |
|---|---|---|---|
| **Precise code editing** (editor) | `0.0` | `0.1` | `1` |
| **Reasoning/architecture** (controlled creativity) | `0.5–0.7` | `0.8` | `20–40` |
| **Brainstorming/exploration** | `0.9–1.0` | `0.95` | `50+` |

### How to easily test different models/parameters

**Option 1: Separate config files for experimentation**
```bash
aider -c configs/qwen-creative.yml
aider -c configs/qwen-precise.yml
aider -c configs/mistral-default.yml
```

**Option 2: Quick command-line override** (without touching the file, for a one-off test)
```bash
aider --model openai/mistralai/Mistral-Small-3.2-24B-Instruct-2506
```

### Validate the YAML before each test

```bash
python3 -c "import yaml; print(yaml.safe_load(open('.aider.conf.yml')))"
```

- More about API: `https://aws.amazon.com/what-is/api/`
