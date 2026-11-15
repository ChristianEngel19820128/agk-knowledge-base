# AGK Knowledge Base – Repository Instructions

## Purpose

Maintain a public, evidence-based AppGameKit Studio knowledge base. The goal is reproducible lessons, not unsupported claims about the engine or any reference game's behavior.

## Default permissions

**Read-only by default.** Reviewing an issue or suggesting a correction does not authorize writing. Make repository changes only within the scope explicitly approved by the repository owner.

- Work on `main` only when approved for this repository.
- Do not modify SonecBot, Crystals or other source repositories as part of a Knowledge Base edit.
- Do not alter `LICENSE` without explicit instruction.
- Keep commits focused and do not rewrite history without approval.
- Check existing files and current branch HEAD before writing.

## Evidence and provenance

Every technical finding needs a concrete source and a status:

- **Locally tested:** developer-run test, with available conditions and limitations.
- **Code-reviewed:** verified against pinned code, runtime behavior not necessarily confirmed.
- **Hypothesis:** plausible but unproven.
- **Design decision / proposal:** intended future behavior, not a verified implementation.

Separate AGK built-in features from custom game wrappers, collision helpers, sprite management, timers and physics. Never generalize single-machine behavior to all devices. Do not claim to have run AGK/HTML5 builds unless actually performed.

Prefer a pinned commit and line-linked source. Verify the exact file content before quoting or editing it. Keep original source projects and their licenses distinct.

## Writing

- Main language: English.
- Keep examples short, original or appropriately sourced.
- Clearly identify open questions and follow-up experiments.
- If data is missing, state that it was not recorded.
- Review links and Markdown structure after edits.

## Initial sources

- SonecBot: `ChristianEngel19820128/SonecBot`, active development on `develop`.
- Crystal Columns: `ChristianEngel19820128/Crystals`, reference branch `main`.

See [references/source-projects.md](references/source-projects.md). Further source projects require their own review.

## Grow with the source projects

Regularly review new implementations, debugging results and test feedback from the source projects for reusable Knowledge Base material. The repository is also an implementation reference for later project work. Check existing articles and experiments first; record promising candidates in the source project's documentation with their evidence status and unresolved tests. A candidate is not a verified finding. Before adding an article, verify the actual source revision, relevant licensing, reproducibility, test conditions and limitations, and distinguish custom game code from AGK engine behavior. Do not upgrade an uncompiled or visually untested experiment to a proven solution. Keep important documentation current in each authorized commit; update the affected files rather than every file routinely.
