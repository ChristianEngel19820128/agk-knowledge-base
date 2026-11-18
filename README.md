# AGK Knowledge Base

**Practical findings, reproducible experiments, and debugging notes for AppGameKit Studio.**

This independent, community-oriented knowledge base collects lessons learned while building games with [AppGameKit Studio](https://www.thegamecreators.com/product/app-game-kit). It begins with the public development histories of **SonecBot** and **Crystal Columns (Crystals)**.

The aim is to distinguish what code actually does, what developers observed in a particular environment, and what still needs verification. This is a growing field notebook, **not** official AppGameKit documentation or a catalogue of confirmed engine bugs.

> **Living knowledge base:** the initial v0.1 articles are retained, and newer findings are added as evidence becomes available. For the **full inventory of current, draft, deferred and not-yet-verified topics**, use the [TODO and evidence backlog](TODO.md). This is the single source of truth for work and evidence status.

## Browse the knowledge base

This curated list contains **Published** topic articles only. Articles in **Review**, unfinished candidates and the full evidence/status record are linked from the [TODO and evidence backlog](TODO.md); their status is maintained there rather than duplicated in this README.

| Topic | What it covers |
| --- | --- |
| [Timing and frame rates](topics/timing-and-frame-rates.md) | Timer thresholds, real/virtual time, delta updates, integer-vs-float arithmetic and frame-dependent event probabilities |
| [Rendering and pixel scaling](topics/rendering-and-pixel-scaling.md) | VSync requests, Windows OpenGL/Vulkan comparisons, subpixel positions, pixel snapping and sprite edge contrast |
| [Collision and sprite scaling](topics/collision-and-sprite-scaling.md) | Double-scaling pitfalls, animated hit geometry, circle-to-box tests and collision ownership |
| [Testing methodology](topics/testing-methodology.md) | How to report reproducible conditions and label the strength of each claim |
| [Keyboard input and safe rebinding](topics/keyboard-input-and-rebinding.md) | Safe menu navigation, modal capture, duplicate assignments, persistence, defaults and developer-tested Windows/HTML5 behavior |
| [Persistent settings: Windows vs. HTML5](topics/html5-persistence-and-settings.md) | Platform-specific storage, versioned settings, safe defaults, validation, and separately reported Windows/HTML5/browser tests |
| [Browser exit versus desktop quit](topics/browser-exit-vs-desktop-quit.md) | Platform-aware ESC and QUIT handling, with code evidence from SonecBot and a historical Crystals HTML5 fix |

### Reproducible experiments

- [10 ms timer gate vs. every-update movement](experiments/frame-update-comparison.md)
- [Growing flame circle vs. static axis-aligned box](experiments/circle-vs-box-collision.md)

**More articles, including drafts under review:** browse the [TODO and evidence backlog](TODO.md). It links each topic and records what evidence is available or still missing; the README intentionally does **not** duplicate that evolving list.

See [source projects and pinned references](references/source-projects.md) for original code and context.

## Evidence labels

- **Locally tested:** A developer ran the game and reported a specific observation. Include system and test conditions when known. A visual test is not a numerical measurement.
- **Code-reviewed:** Verified from a particular source revision, but not necessarily run in a compiled AGK build.
- **Hypothesis:** Plausible explanation not yet isolated by a decisive test.
- **Design decision / proposal:** Intended or suggested game behavior, not proof of engine behavior or completed implementation.

Different parts of a document can have different evidence labels. A result on one renderer, GPU, refresh rate or game does **not** imply the same result everywhere.

## Important scope limitation

The initial games use custom objects, timers, drawing wrappers and collision mathematics alongside AGK API calls. Bugs in those layers should **not** be attributed to AGK's built-in sprite collision or physics system without a separate minimal reproduction. Windows observations are not HTML5 test results.

## Contributing

Corrections, independent reproductions, new hardware results and smaller test cases are welcome. Read [CONTRIBUTING.md](CONTRIBUTING.md) before proposing an addition. Cite the AGK version, platform, source revision and test method whenever possible.

## License

This repository uses the [Unlicense](LICENSE). The reference projects also currently include Unlicense files; consult each project's license and source revision before copying substantial material. All examples in this knowledge base should be independently explained, attributed and safe to redistribute.

## Reference games

- [SonecBot](https://github.com/ChristianEngel19820128/SonecBot)
- [Crystal Columns / Crystals](https://github.com/ChristianEngel19820128/Crystals)

Other projects can be added after their code and test histories have been examined separately.
