# AGK Knowledge Base – TODO and evidence backlog

**Updated:** 18 November 2026  
**Repository:** `ChristianEngel19820128/agk-knowledge-base`  
**Branch:** `main`  
**Purpose:** A durable inventory of completed, unfinished, uncertain and deliberately deferred research. **An entry does not need to be finished to belong here.**

## Current focus

**Latest accepted article:** [Keyboard input and safe rebinding](topics/keyboard-input-and-rebinding.md), created from a pinned SonecBot source revision. **Work status: Published** (owner reviewed and approved). The article explains the original menu regression, fixed menu navigation, 41-key modal capture, visual duplicate warnings, versioned Windows/HTML5 persistence, keyboard-only reset and developer-reported acceptance tests. It explicitly defers gamepad remapping until supported by Real Alpha implementation/tests.

**Consistency check completed (read-only):** Compared the keyboard article with existing topics/experiments, [AGENTS.md](AGENTS.md), [CONTRIBUTING.md](CONTRIBUTING.md), [README.md](README.md) and [source provenance](references/source-projects.md). No fundamental technical contradictions were identified. Three documentation improvements were approved and applied: README links to this TODO instead of duplicating the dynamic topic list; the new pinned SonecBot revision was added to source provenance without replacing the v0.1 baseline; article/contributor metadata now separate work from evidence status.

**Latest accepted article:** [Persistent settings in AppGameKit Studio: Windows vs. HTML5](topics/html5-persistence-and-settings.md) has **Work status: Published** following owner readability approval and the completed cross-article consistency review. It compares the separately versioned `GameOptions` and `KeyBindings` formats, Windows files vs HTML5 shared variables, defaults/validation and developer-reported tests. It links to (rather than duplicates) the published keyboard article.

**Current focus:** Review the new short [Browser exit versus desktop quit](topics/browser-exit-vs-desktop-quit.md) article. This is a narrow **code-reviewed** finding supported by a developer-reported browser observation, not a universal AGK/ESC claim. In particular, the QUIT flag is gated on the non-HTML5 branch but `GetESCPressed()` is still evaluated in HTML5. No new runtime tests were performed.

**Single next step:** owner reviews the small article for clarity and accuracy. If it is not useful or needs new browser tests, leave it in Review and return focus to SonecBot Real Alpha in a separate chat; otherwise complete consistency review and request explicit publication approval. Gamepad remapping remains deferred. Keep `When to use this / when not to use this` in future articles.

**Metadata-only status review (18 November 2026):** The four original topic articles and both existing experiments were checked against this inventory. Their document headers now explicitly distinguish **Work status: Published** from their original evidence descriptions. No technical explanations, results, or test claims were changed. The README's curated *Browse* list includes Published topics only; Review candidates remain discoverable here. The new keyboard article was subsequently reviewed and approved by the owner and is now **Published**.

## Status model

**Work status**
- `Backlog`: recorded but not started
- `Research`: verifying source, history and test conditions
- `Draft`: an article is being written
- `Review`: article and citations under review
- `Published`: article already present, may still contain open questions
- `Deferred`: intentionally paused

**Evidence status**
- `Locally tested`: developer executed a specified test; state platform and limits
- `Code-reviewed`: behavior supported by actual source revision, no runtime guarantee
- `Hypothesis`: possible explanation or mechanism needs verification
- `Design decision / proposal`: intended design, not observed behavior
- `Evidence missing`: TODO-only label; not yet enough sourced information to classify the claim

Work status and evidence status are **independent**. Use mixed evidence with per-claim details where needed. `Published` does not imply universally verified. Do not mark a candidate complete merely because a project feature was completed.

## Initial inventory

| Topic | Work status | Evidence status | Source / existing article | Missing work or next action |
| --- | --- | --- | --- | --- |
| Knowledge Base collaboration and release-safe workflow | Published | Code-reviewed / Design decision | [AGENTS.md](AGENTS.md), [SonecBot AGENTS.md](https://github.com/ChristianEngel19820128/SonecBot/blob/develop/AGENTS.md) | Review workflow after using it in future KB contributions; no application code tests required |
| Technical contribution and test methodology | Published | Code-reviewed / Design decision | [CONTRIBUTING.md](CONTRIBUTING.md), [testing methodology](topics/testing-methodology.md) | Improve only when a concrete gap or contradiction appears |
| Keyboard input, capture, rebinding, duplicate detection and fixed menu controls | Published | Code-reviewed at [pinned SonecBot revision](https://github.com/ChristianEngel19820128/SonecBot/tree/2ddb1c93963dc38bbe3c17260e9bb7a6f8b40d8c); developer-reported locally tested (Windows, HTML5/Apache, itch.io Draft) | [New article](topics/keyboard-input-and-rebinding.md), [SonecBot HANDOFF.md](https://github.com/ChristianEngel19820128/SonecBot/blob/develop/HANDOFF.md) | Owner approved final article; status Published, listed in README; gamepad remapping deferred to Real Alpha, broader browser/device compatibility remains open |
| HTML5 export, browser input and saved key bindings | Published (persistence article); other HTML5 topics remain Backlog | Code-reviewed [pinned source](https://github.com/ChristianEngel19820128/SonecBot/tree/2ddb1c93963dc38bbe3c17260e9bb7a6f8b40d8c); developer-reported locally tested (Windows, local HTML5/Apache, itch.io Draft); wider browser compatibility not established | [Published persistence article](topics/html5-persistence-and-settings.md), [SonecBot HANDOFF.md](https://github.com/ChristianEngel19820128/SonecBot/blob/develop/HANDOFF.md) | Owner accepted readability; consistency review passed and article listed in README; HTML5 browser input and general export topics remain separate candidates |
| Multi-part enemy / Alpha-Walker movement, turret and collision ownership | Backlog | Locally tested in SonecBot; reusable guidance not yet reviewed | [SonecBot TECHNICAL.md](https://github.com/ChristianEngel19820128/SonecBot/blob/develop/TECHNICAL.md), [historical accepted implementation](https://github.com/ChristianEngel19820128/SonecBot/tree/18f92a965810763b552bd96649607c112f966238) | Separate project-specific behavior from general patterns; select narrowly reproducible examples |
| Timer gate vs per-update movement | Published | Code-reviewed; selected local visual A/B tests | [Timing and frame rates](topics/timing-and-frame-rates.md), [frame-update experiment](experiments/frame-update-comparison.md) | Optional further instrumentation and HTML5 reproduction, not prerequisite to retain the article |
| Renderer, VSync, pixel scaling and edge shimmer | Published | Mixed: locally tested visual observations, code review and hypotheses | [Rendering and pixel scaling](topics/rendering-and-pixel-scaling.md) | Instrument frame pacing; verify on other hardware/browsers before broad claims |
| Collision size, growing flame circle and geometric tests | Published | Code-reviewed and selected locally tested fixes; some open targets | [Collision and sprite scaling](topics/collision-and-sprite-scaling.md), [circle/box experiment](experiments/circle-vs-box-collision.md) | Separate target-specific regression tests and any balancing from geometric correctness |
| Update-rate-dependent random spawn attempts | Deferred | Code-reviewed candidate / Hypothesis (actual difficulty effect not measured) | [Timing and frame rates](topics/timing-and-frame-rates.md), [SonecBot game loop](https://github.com/ChristianEngel19820128/SonecBot/blob/develop/source/sonec/game.agc) | Compare attempted events and successful spawns at controlled update rates; no gameplay changes authorized |
| Additional AGK edge cases and lessons from Crystals | Backlog | Evidence missing for a consolidated new article | [Crystals](https://github.com/ChristianEngel19820128/Crystals), [source references](references/source-projects.md) | Review repository documentation and identify concrete transferable findings before scoping work |
| Other historical projects (Subspace, Blizz, Clone) | Deferred | Evidence missing | [Source project references](references/source-projects.md) | Verify accessible repository, licensing, source revisions and actual test history before adding project-specific claims |

## Candidate intake from SonecBot historical notes (18 November 2026)

These are **Knowledge Base research candidates**, recovered from [SonecBot TODO.md](https://github.com/ChristianEngel19820128/SonecBot/blob/develop/TODO.md), [HANDOFF.md](https://github.com/ChristianEngel19820128/SonecBot/blob/develop/HANDOFF.md) and [TECHNICAL.md](https://github.com/ChristianEngel19820128/SonecBot/blob/develop/TECHNICAL.md). **None is a newly verified AGK engine defect or an approved game-code change.** Historical source documents are moving references; pin precise revisions and test conditions when investigating individual candidates.

| Candidate / category | Work status | Evidence status | What is currently known / what must be checked |
| --- | --- | --- | --- |
| **Art & Sound – HTML5 sound instance overlap, clipping and volume management** | Backlog | Locally observed (developer); cause **Hypothesis** | Developer heard occasional crackling with overlapping SonecBot HTML5 sound effects. Possible causes include summed volume/clipping, simultaneous instances or browser audio; no root cause established. Record reproducible sound combinations, browser/hardware, sample versus summing levels, compare Windows, inspect `sound.agc` / `sonec.agc` and AGK instance APIs. Ducking/limiting instances is a **proposal, not a tested fix**. |
| **Art & Sound – Soundloop creation and implementation** | Backlog | Developer experience; transferable rule **Evidence missing** | Developer reports a time-consuming loop-production issue: aligning MIDI notes/events at the **start of the loop**, with **one silent bar at the end**, helped their intended loop. Do **not** present this as universally required: identify DAW/MIDI tool, tempo/meter, export, actual loop boundaries, whether the pause is encoded, audio tails and AGK playback settings; seek a controlled before/after listening test. |
| **HTML5 – browser gamepad index and activation delay** | Backlog | Locally tested in one SonecBot setup; portability **Evidence missing** | Developer reported an HTML5 index-1 workaround, initial controller interaction and approximately 2–3 s startup latency. Verify exact AGK/browser/device combination, mapping/index semantics and repeatability. A one-system workaround is not a universal AGK rule. |
| **HTML5 – browser exit versus desktop ESC/QUIT** | Review | Code-reviewed (pinned SonecBot source); developer-reported locally observed | [Short review article](topics/browser-exit-vs-desktop-quit.md): menu QUIT flag is excluded from HTML5 loop exit, but `GetESCPressed()` is still evaluated there. ESC behavior needs independent browser/focus testing; do not call it an AGK defect or automatically change quit UX. |
| **HTML5 – XAMPP/Apache browser caching during iteration** | Backlog | Developer observation; controlled reproduction **Evidence missing** | Developer reports old HTML5 scripts/assets may survive file replacement until a proper reload. Record cache conditions, origin, DevTools Disable cache / hard reload, browser version and a repeatable old/new-build experiment. |
| **HTML5 – function/debug keys versus browser shortcuts** | Backlog | Locally observed; browser/device scope not recorded | Developer observed function keys activating browser actions instead of in-game Windows diagnostics. Inventory affected keys, browser shortcuts and alternatives without promising cross-browser interception. |
| **AGK language/math – integer versus float division and nested constants** | Backlog | Mixed: code-reviewed project example; other claims **Evidence missing** | Existing [timing article](topics/timing-and-frame-rates.md) already covers `1000.0/Span`. Avoid duplication; investigate only distinct pitfalls, expression evaluation and nested constants against a minimal sample. |
| **AGK math – negative random bounds and robust centering** | Backlog | **Hypothesis / Evidence missing** | Historical SonecBot notes mention negative random ranges and 0-degree centering, but multiple variables were changed during a successful test; this does **not** prove a random-function engine bug. Recover exact code/reproduction and isolate one variable. |
| **Graphics – turret swivel projection without additional sprite** | Backlog | Evidence missing | Mentioned in historical handoff. Identify actual implementation, projection math, source and test case before extracting a reusable technique. |
| **Graphics – sky/cloud cylinder technique from another project** | Deferred | Evidence missing | Historical candidate references an unspecified other project. Verify accessible source, permission/license and actual implementation before claiming a technique. |
| **Sound architecture – event ownership and duplicate playback** | Backlog | Design decision / proposal | SonecBot notes propose triggering reusable sounds in the owning shared event/function rather than only actor-specific code. First inventory sound triggers and prevent duplicate playback; no implementation/test approval. Separate from HTML5 crackling. |

**Triage boundary:** project-specific reports about player movement feel, damage balancing, HUD polish, roadmap features or external tester feedback remain in the **SonecBot project workflow**, not automatic Knowledge Base findings. The TODO is an intake register: capture ideas first, verify exact revision and evidence later. Existing timing/spawn and keyboard/gamepad topics are not duplicated as new articles merely because they appear in source handoffs.

## Intake checklist for every new idea

1. Record a **short, precise candidate**, even when it is currently just a question.
2. Add its source repository, file/commit pointer or write `not yet located`; never fabricate a revision.
3. Choose **work status** and **evidence status** separately; specify mixed evidence when appropriate.
4. Record platform, AGK version, renderer or browser only if known; otherwise `not recorded`.
5. Specify the **missing evidence / next discriminating test** and whether an article already covers part of it.
6. Keep one primary current focus; do not attempt to complete every backlog item in parallel.
7. When authorized, update the relevant articles and then the TODO with actual outcomes. Retain historical failed tests and deferred questions.

## Handoff / resumption notes

- **Last scoped change:** Owner approved a new English Keyboard Input & Rebinding article with pinned code references, accessible explanation, test provenance and explicit gamepad deferral. Created [topics/keyboard-input-and-rebinding.md](topics/keyboard-input-and-rebinding.md) and advanced its status to **Review**. No source project/game/release modifications.
- **Out of scope:** SonecBot or Crystals edits, gamepad remapping implementation, new releases, other article rewrites.
- **Consistency review:** finished read-only and found no fundamental technical conflict, but recommended a single README-to-TODO backlog link, an additional pinned provenance snapshot, and two-axis metadata guidance. The owner explicitly approved these changes; they are implemented in the current documentation update.
- **Final approval:** owner reviewed relevant pages and spot-checked an older article; confirmed keyboard article content and approved Published. Added it to the curated README listing and updated TODO status; code, test claims and gamepad limitations unchanged.
- **Next discussion:** select the next backlog candidate and investigate evidence first; keep Real Alpha gamepad control mapping out of scope.
- **Short browser exit draft (18 November 2026):** Created [topics/browser-exit-vs-desktop-quit.md](topics/browser-exit-vs-desktop-quit.md) with status Review, explaining the exact custom `Intro.IsQuit`/`GetESCPressed()` loop distinction. No browser input instrumentation, AGK compilation or SonecBot code edit was performed. Await owner review; after that, SonecBot Real Alpha roadmap can become the priority in a new chat.
- **Persistence article published (18 November 2026):** The owner confirmed the article is understandable and useful. Final source/readability consistency comparison with the published keyboard article, testing methodology and contributing rules found no blocking contradiction; moved [topics/html5-persistence-and-settings.md](topics/html5-persistence-and-settings.md) from Review to Published and added it to README. Pinned SonecBot source and Windows/Apache/itch.io test boundaries remain; no new AGK/browser testing performed.
- **Scope note:** The user reported itch.io screenshots have not yet been uploaded; this is a SonecBot presentation task, not a prerequisite to the KB persistence article. Roadmap and feedback remain separate chat scopes.
- **New candidate intake (18 November 2026):** Moved previously scattered Knowledge Base candidates from SonecBot TODO/HANDOFF/TECHNICAL into this backlog, including two `Art & Sound` candidates, HTML5/browser workflow questions, language/math questions and graphics techniques. No candidate is treated as a confirmed engine defect and no technical articles were created in this intake step.
- **Follow-up housekeeping:** The inventory item `Knowledge Base collaboration and release-safe workflow` is a **published repository policy in AGENTS.md**, not a standalone topic article. Clarify its **Type: Repository policy** in a later metadata-only step; do not create a redundant article. 
- **Chat separation agreed:** Continue Knowledge Base here/its own chat as needed; discuss SonecBot Real Alpha roadmap and feedback in separate chats, using each repository's up-to-date TODO/handoff and source revisions.
- **Source context:** SonecBot keyboard update completed in four steps; developer reported Windows and local HTML5 tests followed by **12/12 itch.io Draft embed smoke-test passes**. These are project observations and not universal AGK guarantees. The itch.io page remains Draft, not publicly published.
