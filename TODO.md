# AGK Knowledge Base – TODO and evidence backlog

**Updated:** 18 November 2026  
**Repository:** `ChristianEngel19820128/agk-knowledge-base`  
**Branch:** `main`  
**Purpose:** A durable inventory of completed, unfinished, uncertain and deliberately deferred research. **An entry does not need to be finished to belong here.**

## Current focus

**Workflow foundation – first approved step:** incorporate the SonecBot collaboration and test-gate lessons into [AGENTS.md](AGENTS.md) and establish this two-axis backlog. These two documentation tasks are the scope of the initial change; no game code, source project repositories, other Knowledge Base articles or releases are in scope.

**Single next step after this foundation:** inspect the pinned SonecBot keyboard-input code and developer test history, check for any overlap with current topics, then agree on the exact scope of a new keyboard/rebinding article. **No article implementation has been approved yet.**

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
| Keyboard input, capture, rebinding, duplicate detection and fixed menu controls | Backlog | Locally tested (SonecBot developer: Windows, local HTML5/Apache, hosted itch.io Draft); article not drafted | [SonecBot HANDOFF.md](https://github.com/ChristianEngel19820128/SonecBot/blob/develop/HANDOFF.md), [intro.agc](https://github.com/ChristianEngel19820128/SonecBot/blob/develop/source/sonec/intro.agc), [input.agc](https://github.com/ChristianEngel19820128/SonecBot/blob/develop/source/sonec/input.agc) | Recover exact code/test revision links, distinguish menu workaround from AGK behavior, draft a small reusable article |
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

- **Last scoped change:** Establish workflow in AGENTS.md and new TODO.md, as approved by the owner. Both are documentation-only.
- **Out of scope:** modifications to SonecBot or Crystals, gameplay, new topic articles, repository releases or the itch.io Draft.
- **Next discussion:** prioritize and scope the keyboard input/rebinding knowledge entry using pinned code and test evidence. A separate approval is required before creating that article.
- **Source context:** SonecBot keyboard update completed in four steps; developer reported Windows and local HTML5 tests followed by **12/12 itch.io Draft embed smoke-test passes**. These are project observations and not universal AGK guarantees. The itch.io page remains Draft, not publicly published.
