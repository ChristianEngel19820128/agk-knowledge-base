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


## Collaboration workflow learned from SonecBot (November 2026)

Use this workflow for Knowledge Base research, edits, and cross-project comparisons. It complements the read-only default above; **a request to collect ideas or investigate does not authorize a commit**.

1. **Check the real baseline.** Read the current repository, branch, source files and existing articles. Confirm the actual HEAD and avoid relying only on chat recollections or stale handoffs. Prefer source links pinned to a commit.
2. **Scope one outcome.** State the concrete question, the repository/files in scope, expected behavior and observed behavior. Determine whether this is an AGK API issue, custom game logic, a platform-specific interaction, or an open question.
3. **Investigate before proposing changes.** Trace relevant code and test history. Present competing explanations, confidence and missing evidence; do not classify a symptom as an engine bug without an isolated reproduction.
4. **Obtain explicit scoped approval.** Agree on the exact files, content and branch before writing. No unrelated code edits, game balancing, asset changes, release operations or writes to the source projects.
5. **Make the smallest reviewable change.** Keep the existing historical record, use focused commits and cite tested/reviewed source revisions. Do not expand a documentation update into a new investigation automatically.
6. **Test the claims at the right level.** Static source inspection is **code-reviewed**, not an AGK compile or runtime test. Only record local Windows/AGK Studio, HTML5/Apache, browser persistence or hosted itch.io-embed tests when actually performed and reported by the developer. These are distinct environments; passing one does not imply passing another. Report test cases, pass/fail counts, limitations and environment details when known.
7. **Close the step and hand it off.** Update the affected documentation and [TODO.md](TODO.md) with what changed, supporting commit, actual evidence, unresolved questions and **one next active focus**. Finish or deliberately defer the current step before beginning another. Never mark unfinished items complete just to make a milestone look tidy.

### Handling regressions and uncertain findings

- A newly discovered regression reopens the relevant test gate. Reproduce the specific behavior, identify the actual code path, obtain approval for a focused fix and rerun the affected regression tests. Preserve the failed result as history rather than presenting the final pass as if no issue had occurred.
- Mark a suspected mechanism as a **Hypothesis** until a discriminating test or reliable source analysis supports it; an observed improvement does not necessarily prove its root cause.
- Use a minimal reproducible example when attributing behavior to AGK itself. Custom input mappings, timers, sprite transforms and collision geometry can explain symptoms without an engine defect.
- If a test was not run, label it **not tested**, even if adjacent checks passed. Never claim that an assistant executed desktop builds, hardware tests, browser sessions or a hosted release when only the developer did.
- Do not upgrade one developer's visual result to an instrumented measurement or to a universal rule for all browsers, GPUs and AGK versions.

### Knowledge backlog and evidence maturity

[TODO.md](TODO.md) is the durable inventory of candidates, including **incomplete, untested and deferred topics**. It is acceptable—and often preferable—to record a topic before its supporting evidence is complete. Add its source pointer and the precise missing test or fact rather than inventing certainty or opening a full article prematurely.

Track two independent axes:

- **Work status:** `Backlog` (captured), `Research` (sources under review), `Draft` (article being written), `Review` (evidence and Markdown being checked), `Published` (article exists in the Knowledge Base), `Deferred` (parked intentionally).
- **Evidence status:** `Locally tested`, `Code-reviewed`, `Hypothesis`, `Design decision / proposal`, or `Evidence missing`. The first four match the established article-level terminology; **Evidence missing is a TODO-only triage label**, not a claim suitable for a published finding. A topic can contain mixed evidence: specify which claim has which status.

`Published` means documented, **not** proven on every platform; a carefully labelled open hypothesis may remain in a published investigation. Multiple tests in different environments should retain their own provenance. Do not require every backlog entry to reach Published, and never erase open candidates solely to make a list appear finished.

### Where each kind of information belongs

- `AGENTS.md`: stable rules, permissions and workflow; avoid transient task lists here.
- `TODO.md`: current focus, candidate inventory, status, missing evidence, ownership/source pointers and next actions.
- `CONTRIBUTING.md`: evidence and Markdown expectations for individual contributions and articles.
- `topics/` and `experiments/`: technical findings, reproducible comparisons, results and caveats.
- `references/`: tested or reviewed source revisions and provenance.

The Knowledge Base is public and source-oriented. Internal handoffs, unreviewed speculative snippets, and private project/user information should not be copied into public articles. For new sources or extensive code, verify licensing and attribution before publication.
