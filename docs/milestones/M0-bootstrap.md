# M0: Bootstrap

| | |
| --- | --- |
| Status | Done (2026-11-27) |
| Branch | `first-branch` (merged in PR #1) |
| Time | About 1 hour |

## Goal

A clean Python project that installs with one command and never commits the API key.

## Concepts

- **Virtual environment (`.venv`):** project-only dependencies, like a Maven project's own libraries. Activate it in every new terminal: `source .venv/bin/activate`. The prompt then starts with `(.venv)`.
- **`.env` vs `.env.example`:** `.env` holds the real key and is gitignored. `.env.example` is the template with the same variable names and no values, committed so others know what to set.
- **Model name as config:** `GEMINI_MODEL` lives in `.env`, not in code, so switching models needs no code change.

## Tasks

- [x] Python 3.12 installed (the Mac's built-in 3.9 is too old; do not remove it)
- [x] Free Gemini API key from Google AI Studio, personal account
- [x] Public repo `MarwahZain/rag-chatbot-llm-eval` created and cloned
- [x] `python3.12 -m venv .venv` and activated
- [x] `requirements.txt`: `google-genai`, `chromadb`, `python-dotenv`, installed with `pip install -r requirements.txt`
- [x] `.env` with `GEMINI_API_KEY` and `GEMINI_MODEL`; `.env.example` without the key
- [x] `.gitignore`: `.venv/`, `.env`, `__pycache__/`, `.deepeval/`, `.pytest_cache/`, `.DS_Store`, `.vscode/`
- [x] Folders: `data/faqs/`, `src/`, `tests/` (with `.gitkeep`), `docs/`
- [x] VS Code: Python extension (Microsoft) installed, interpreter set to `./.venv/bin/python`

## Definition of done

- `python --version` inside the venv prints 3.12.x
- `git check-ignore .env .venv/` prints both names
- `git log --all -- .env` prints nothing (the key was never committed)

## Notes and findings

- `zsh: command not found: python` means the venv is not active. `python3` outside the venv runs the Mac's 3.9, which does not have the libraries.
- Appending to `.gitignore` with `echo >>` glued two entries together because the file had no final newline. Check with `tail -c1 .gitignore` before appending.
