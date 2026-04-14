# Cursor agent harness

A **cloneable starter** for coding projects where **agents and humans share the same contract**: canonical documentation under `/docs` and Cursor rules under `.cursor/rules`. Application code can live anywhere you want; this repo defines **how context is organized**, not how your app is laid out.

## Intention

- **Agent-forward:** Rules tell the AI to read `/docs` first, avoid guessing on empty specs, use Plan mode to fill gaps, and sync docs when you explicitly “wrap up” a task.
- **Human-friendly:** The same files are the source of truth for product intent, architecture, stack, and feature plans—useful for onboarding and reviews, not only for automation.

## Layout

```text
.cursor/rules/          # Cursor rule files (.mdc)
  01-system-instructions.mdc   # Global behavior: /docs routing, doc readiness, deps
  02-definition-of-done.mdc  # Hold /docs during iteration; wrap-up + anti-drift
  03-docs-architecture.mdc     # How to write docs/architecture.md (globs that file)
  04-python.mdc               # Python conventions (globs *.py)

scratch/                # Optional working notes (session context, backlog—not canonical)
  README.md             # What belongs here vs /docs

docs/                   # Canonical doc tree (paths stay stable across clones)
  spec.md                 # Product scope and requirements
  architecture.md         # How the system/repo is shaped (flexible depth)
  tech_stack.md           # Languages, deps, versions
  ui_resources.md         # UI tokens/assets (trim if no UI)
  decisions.md            # Short ADRs / pivots
  features/
    example/              # Copy to features/<name>/ for real work
      feature-spec.md
      implementation-plan.md
```

## Typical flow

1. **Bootstrap:** Fill or refine `/docs` (often starting with `spec.md` and `architecture.md`). Rules nudge Plan mode when docs are still stubs.
2. **Build:** Implement in your normal code layout; during iteration, rules defer bulk `/docs` edits until you signal completion.
3. **Wrap-up:** Say you’re done (e.g. “wrap it up”); the agent reconciles code with docs and reports what changed.

## Adding this harness to an existing project

Your project’s root **`README.md` should stay yours.** This guide lives here so you can merge **only** what you need:

- Copy or merge **`.cursor/rules/`**, **`docs/`**, and optionally **`scratch/`** (see `scratch/README.md`).
- **Do not** copy the harness repo’s root `README.md` into the project root, or you will overwrite the project readme. The harness keeps a tiny root `README.md` so **GitHub** can show an entry point; full narrative is in this file.

Resolve conflicts if you already have files under `docs/` or `.cursor/rules/` (merge by hand or rename feature example folder).

## README on GitHub vs in your checkout

Git cannot “skip” a tracked file during `git clone`—the object is still fetched. You have three practical options:

1. **Sparse checkout (no `README.md` in the working tree)** — The file remains in the repo (GitHub still renders it), but you only check out harness pieces:

   ```bash
   git clone --filter=blob:none --sparse <url-for-this-repo>
   cd cursor-agent-harness
   git sparse-checkout set --cone .cursor docs scratch
   ```

   Your tree has `.cursor/` and `docs/` only; root `README.md` is not checked out. Add paths later with `git sparse-checkout add <path>` if needed.

2. **Download ZIP from GitHub** — This repo’s `.gitattributes` marks `README.md` with `export-ignore`, so the **Code → Download ZIP** archive usually **omits** `README.md` while the file stays on the web UI. Handy for unpacking into another project without a stray readme.

3. **Normal clone** — You get `README.md` on disk; delete it locally or ignore it if you do not need it (it is only a short pointer to this doc).

## Greenfield use

If this repo **is** the project root, fill stub content in `/docs` and add code wherever you like. Add language-specific rules alongside `04-python.mdc` if you need them (same pattern: one `.mdc` per language area, scoped with `globs`).
