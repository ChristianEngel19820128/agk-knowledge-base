# Testing methodology for AGK investigations

The central principle is to **separate source-code behavior, local observations, hypotheses and design decisions**. A successful fix in one game does not prove an AGK engine defect or a universal workaround.

## Evidence levels

| Label | Meaning | What it does not prove |
| --- | --- | --- |
| **Locally tested** | A developer ran a specific test and reported a result | Universal behavior across devices or browsers |
| **Code-reviewed** | A result follows from the code at a known revision | That the compiled game behaves correctly under every condition |
| **Hypothesis** | A plausible explanation or potential mechanism | That the explanation caused the symptom |
| **Design decision / proposal** | Chosen or proposed intended gameplay | That the feature is implemented or balanced |

When possible, split a finding into multiple statements with separate labels.

## Minimum reproduction details

Record:

- Game/repository, branch and exact commit SHA
- AGK Studio version, or **not recorded**
- Platform (Windows, HTML5/browser, etc.)
- Renderer actually selected, if known
- Sync API requested; measured frame rate and actual display refresh separately
- Virtual resolution and window/device dimensions
- Relevant user settings, debug switches and game state
- Source changes and asset changes between comparisons
- How the result was measured or observed; sample size
- Remaining unknowns and next discriminating test

The game developer can perform AGK builds and visual checks; a static GitHub code inspection alone does not count as an AGK runtime test.

## Good A/B procedure

1. Establish a baseline commit and keep its configuration reproducible.
2. Change **one variable at a time**: timer gate, sprite snapping, renderer, contrast or collision geometry.
3. Retain a way to switch back, ideally while exposing the selected mode in a debug overlay.
4. Sample frame timing and per-object movement, including frames without an update.
5. Compare the same scene or object type, at the same scale and refresh rate.
6. Report **which observation** changed and avoid assuming a cause.
7. Only after the comparison, choose whether a permanent code or asset change is justified.

For the SonecBot movement work, F2 exposed movement steps, elapsed milliseconds and a rolling 60-frame history for selected platforms/towers. F4 tested float/pixel-snap rendering; F5 tested tower movement; F6 tested platform movement. These toggles affect **different systems** and should not be treated as interchangeable.

**Reference:** [SonecBot plate diagnostics](https://github.com/ChristianEngel19820128/SonecBot/blob/6e10b3dcea542071aa8c9c7b2a35f2a3792d3be0/source/sonec/plate.agc#L209-L290), [main debug keys](https://github.com/ChristianEngel19820128/SonecBot/blob/6e10b3dcea542071aa8c9c7b2a35f2a3792d3be0/source/sonec/main.agc#L165-L215).

## Example: distinguishing similar-looking symptoms

**Observation:** A high-contrast chest edge appears to flicker while scrolling.

Possible explanations include fractional pixel positioning, intermittent movement updates, contrast amplification or frame presentation. A screenshot or even a change in perceived smoothness alone cannot distinguish all of them.

Useful comparisons:

- The same sprite asset under two update modes
- The same update mode with old/new edge contrast
- Float draw position vs snapped draw-copy position
- A measured movement-step log vs a visual recording

**Result from SonecBot:** Softer chest/platform edges substantially reduced perceived shimmer locally. The evidence supports an **asset-level improvement**; it does not establish that rendering, AGK timing or VSync was the original sole cause.

## Reuse and publication checklist

Before turning an investigation into a general article:

- Are the source links pinned to tested/reviewed commits?
- Could this be a **custom wrapper** problem rather than an AGK built-in issue?
- Are version, platform and renderer assumptions explicit?
- Is a developer's visual judgment distinguished from an instrumented metric?
- Is the claim reproduced beyond one project/hardware setup?
- Are proposed fixes labelled as proposals until tested?
- Does the article state what remains uncertain?

For details on submitting results, see [CONTRIBUTING.md](../CONTRIBUTING.md).
