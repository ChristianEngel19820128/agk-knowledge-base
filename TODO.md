# AGK Knowledge Base – TODO and evidence backlog

**Updated:** 18 November 2026  
**Repository:** `ChristianEngel19820128/agk-knowledge-base`  
**Branch:** `main`  
**Purpose:** A durable inventory of completed, unfinished, uncertain and deliberately deferred research. **An entry does not need to be finished to belong here.**

## Current focus

**Latest accepted article:** [Keyboard input and safe rebinding](topics/keyboard-input-and-rebinding.md), created from a pinned SonecBot source revision. **Work status: Published** (owner reviewed and approved). The article explains the original menu regression, fixed menu navigation, 41-key modal capture, visual duplicate warnings, versioned Windows/HTML5 persistence, keyboard-only reset and developer-reported acceptance tests. It explicitly defers gamepad remapping until supported by Real Alpha implementation/tests.

**Consistency check completed (read-only):** Compared the keyboard article with existing topics/experiments, [AGENTS.md](AGENTS.md), [CONTRIBUTING.md](CONTRIBUTING.md), [README.md](README.md) and [source provenance](references/source-projects.md). No fundamental technical contradictions were identified. Three documentation improvements were approved and applied: README links to this TODO instead of duplicating the dynamic topic list; the new pinned SonecBot revision was added to source provenance without replacing the v0.1 baseline; article/contributor metadata now separate work from evidence status.

**Single next step:** select the next Knowledge Base topic from the backlog (HTML5 persistence is a candidate) and investigate its source/test evidence before agreeing on article scope. Gamepad remapping remains deferred until Real Alpha development provides evidence.

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
| HTML5 export, browser input and saved key bindings | Backlog | Locally tested in the SonecBot setup; broader browser behavior not established | [SonecBot README.md](https://github.com/ChristianEngel19820128/SonecBot/blob/develop/README.md), [HANDOFF.md](https://github.com/ChristianEngel19820128/SonecBot/blob/develop/HANDOFF.md) | Isolate Windows vs Apache vs itch.io Embed evidence; verify actual save implementation and portability assumptions |
| Multi-part enemy / Alpha-Walker movement, turret and collision ownership | Backlog | Locally tested in SonecBot; reusable guidance not yet reviewed | [SonecBot TECHNICAL.md](https://github.com/ChristianEngel19820128/SonecBot/blob/develop/TECHNICAL.md), [historical accepted implementation](https://github.com/ChristianEngel19820128/SonecBot/tree/18f92a965810763b552bd96649607c112f966238) | Separate project-specific behavior from general patterns; select narrowly reproducible examples |
| Timer gate vs per-update movement | Published | Code-reviewed; selected local visual A/B tests | [Timing and frame rates](topics/timing-and-frame-rates.md), [frame-update experiment](experiments/frame-update-comparison.md) | Optional further instrumentation and HTML5 reproduction, not prerequisite to retain the article |
| Renderer, VSync, pixel scaling and edge shimmer | Published | Mixed: locally tested visual observations, code review and hypotheses | [Rendering and pixel scaling](topics/rendering-and-pixel-scaling.md) | Instrument frame pacing; verify on other hardware/browsers before broad claims |
| Collision size, growing flame circle and geometric tests | Published | Code-reviewed and selected locally tested fixes; some open targets | [Collision and sprite scaling](topics/collision-and-sprite-scaling.md), [circle/box experiment](experiments/circle-vs-box-collision.md) | Separate target-specific regression tests and any balancing from geometric correctness |
| Update-rate-dependent random spawn attempts | Deferred | Code-reviewed candidate / Hypothesis (actual difficulty effect not measured) | [Timing and frame rates](topics/timing-and-frame-rates.md), [SonecBot game loop](https://github.com/ChristianEngel19820128/SonecBot/blob/develop/source/sonec/game.agc) | Compare attempted events and successful spawns at controlled update rates; no gameplay changes authorized |
| Additional AGK edge cases and lessons from Crystals | Backlog | Evidence missing for a consolidated new article | [Crystals](https://github.com/ChristianEngel19820128/Crystals), [source references](references/source-projects.md) | Review repository documentation and identify concrete transferable findings before scoping work |
| Other historical projects (Subspace, Blizz, Clone) | Deferred | Evidence missing | [Source project references](references/source-projects.md) | Verify accessible repository, licensing, source revisions and actual test history before adding project-specific claims |

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
- **Source context:** SonecBot keyboard update completed in four steps; developer reported Windows and local HTML5 tests followed by **12/12 itch.io Draft embed smoke-test passes**. These are project observations and not universal AGK guarantees. The itch.io page remains Draft, not publicly published.
