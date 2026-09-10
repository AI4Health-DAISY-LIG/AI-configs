# Aider Configs — Model / Parameter Comparison

This folder contains several ready-to-use Aider configuration files, so you can test
different models and settings by just config different files.

## Files

| File | Model | Use case |
|---|---|---|
| `qwen-precise.yml` | Qwen3-Coder, temperature 0.0 | Reliable code editing, bugfixes, refactoring |
| `qwen-creative.yml` | Qwen3-Coder, temperature 0.7, architect mode | Thinking through complex changes |
| `mistral-default.yml` | Mistral-Small, temperature 0.5 | Comparison / general-purpose use |

## How to launch a given config

```bash
aider -c configs/qwen-precise.yml
aider -c configs/qwen-creative.yml
aider -c configs/mistral-default.yml
```


## Validate a YAML file before using it

```bash
python3 -c "import yaml; print(yaml.safe_load(open('configs/qwen-precise.yml')))"
```
This should print a Python dictionary (never `None`).

## Parameter definitions

| Parameter | Definition | Effect | Typical values |
|---|---|---|---|
| `temperature` | Degree of randomness in choosing the next token | Low = deterministic/predictable; High = creative/varied | `0.0` (precise) → `0.7` (reasoning) → `1.0+` (exploratory) |
| `top_p` | Cumulative probability threshold for considered tokens | Low = narrow choice; High = more diversity | `0.1` (restrictive) → `0.8-0.9` (common) → `1.0` (no restriction) |
| `top_k` | Number of most probable tokens considered | Low = predictable; High = more variety | `1` (greedy) → `20-40` (common) → `100+` (wide diversity) |
| `edit-format` | How the model applies its changes | `diff` = token-efficient; `whole` = returns the entire file | `diff` (Qwen), `whole` (fallback if patches fail) |
| `map-tokens` | Size of the repo-map sent as context | Higher = better overview of the repo, more token cost | `1024` (light) → `2048-4096` (complex repo) |

## Suggested starting point

Start with `qwen-precise.yml` for concrete tasks (adding a function, fixing a bug),
then switch to `qwen-creative.yml` when you want to explore several approaches before deciding