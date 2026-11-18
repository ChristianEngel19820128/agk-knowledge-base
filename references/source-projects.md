# Source projects and revision provenance

This document preserves the **source revisions used when drafting Knowledge Base v0.1** and records later source reviews in separate dated sections. The source projects are not dependencies, and their current branches may change after each snapshot.

## SonecBot

- Project: [ChristianEngel19820128/SonecBot](https://github.com/ChristianEngel19820128/SonecBot)
- Examined branch: `develop`
- **Pinned documentation baseline:** [`6e10b3dcea542071aa8c9c7b2a35f2a3792d3be0`](https://github.com/ChristianEngel19820128/SonecBot/tree/6e10b3dcea542071aa8c9c7b2a35f2a3792d3be0)
- Relevant documentation: [PROJECT.md](https://github.com/ChristianEngel19820128/SonecBot/blob/6e10b3dcea542071aa8c9c7b2a35f2a3792d3be0/PROJECT.md), [TECHNICAL.md](https://github.com/ChristianEngel19820128/SonecBot/blob/6e10b3dcea542071aa8c9c7b2a35f2a3792d3be0/TECHNICAL.md), [TODO.md](https://github.com/ChristianEngel19820128/SonecBot/blob/6e10b3dcea542071aa8c9c7b2a35f2a3792d3be0/TODO.md)
- Source files reviewed: `timer.agc`, `plate.agc`, `tower.agc`, `sprite.agc`, `flame.agc`, `chest.agc`, `collision.agc`, `game.agc`, `main.agc`
- Licensing reference: [SonecBot LICENSE](https://github.com/ChristianEngel19820128/SonecBot/blob/6e10b3dcea542071aa8c9c7b2a35f2a3792d3be0/LICENSE) (Unlicense)

### Important historical change points

| Reference | Meaning |
| --- | --- |
| [`d536043`](https://github.com/ChristianEngel19820128/SonecBot/commit/d536043) | Use floating-point literal in the custom timer multiplier |
| [`a4be9ab`](https://github.com/ChristianEngel19820128/SonecBot/commit/a4be9ab7a91d30581390e01e99323cca35755972) | Correct double-scaled chest collision bounds |
| [`be6c33d`](https://github.com/ChristianEngel19820128/SonecBot/commit/be6c33d839a3b8242792ecc8230c7217d05bef39) | Correct double-scaled flame collision bounds |
| [`2879d7c`](https://github.com/ChristianEngel19820128/SonecBot/commit/2879d7cc4a1f46d56b3d2b2125a78915cef32798) | Trial of growing circular flames against chest rectangles |
| [`2b2ec8c`](https://github.com/ChristianEngel19820128/SonecBot/commit/2b2ec8c705a080dbb3cf3701e7e9784640a06f3e) | Shared growing radius for the four flame collision paths |
| [`88f5575`](https://github.com/ChristianEngel19820128/SonecBot/commit/88f55755bda991e4a7601a1c46c5e2a7e1285982) | Reduced platform/chest sprite-edge contrast |

**Evidence boundary:** Most compiled/visual observations were supplied by the developer on a Windows system. The Knowledge Base authoring pass is a **source review** and does not independently compile or run the AGK project. Local test results in the articles must not be upgraded to controlled lab measurements.

### Later SonecBot review: keyboard input and rebinding (18 November 2026)

- **Pinned reviewed code revision:** [`2ddb1c93963dc38bbe3c17260e9bb7a6f8b40d8c`](https://github.com/ChristianEngel19820128/SonecBot/tree/2ddb1c93963dc38bbe3c17260e9bb7a6f8b40d8c). This is a subsequent reference, **not** a replacement for the v0.1 baseline `6e10b3d`.
- **Reviewed implementation files:** [`source/sonec/intro.agc`](https://github.com/ChristianEngel19820128/SonecBot/blob/2ddb1c93963dc38bbe3c17260e9bb7a6f8b40d8c/source/sonec/intro.agc), [`input.agc`](https://github.com/ChristianEngel19820128/SonecBot/blob/2ddb1c93963dc38bbe3c17260e9bb7a6f8b40d8c/source/sonec/input.agc), [`def_input.agc`](https://github.com/ChristianEngel19820128/SonecBot/blob/2ddb1c93963dc38bbe3c17260e9bb7a6f8b40d8c/source/sonec/def_input.agc).
- **Knowledge Base draft:** [Keyboard input and safe rebinding](../topics/keyboard-input-and-rebinding.md), currently undergoing review. The article covers a custom menu-navigation regression, modal key capture, duplicate warnings, versioned input persistence and keyboard-only reset.
- **Test provenance:** SonecBot developer reported Windows and local HTML5/Apache acceptance, followed by a **12/12 hosted itch.io Draft embed smoke test** for the keyboard-enabled build. The [project handoff](https://github.com/ChristianEngel19820128/SonecBot/blob/develop/HANDOFF.md) records the reports but is a **moving documentation reference**; it is not itself a pinned test-log revision.
- **Evidence limits:** source implementation was code-reviewed, but the Knowledge Base editor did not independently run AGK, Windows, HTML5 or itch.io. Gamepad mapping beyond the present implementation is deferred until future Real Alpha development and testing.

## Crystal Columns (Crystals)

- Project: [ChristianEngel19820128/Crystals](https://github.com/ChristianEngel19820128/Crystals)
- Examined branch: `main`
- **Pinned reference revision:** [`2d4ac7e61133e6be17fe26e565116223f76c9134`](https://github.com/ChristianEngel19820128/Crystals/tree/2d4ac7e61133e6be17fe26e565116223f76c9134)
- Primary reviewed files: [timer.agc](https://github.com/ChristianEngel19820128/Crystals/blob/2d4ac7e61133e6be17fe26e565116223f76c9134/source/crystals/timer.agc), [main.agc](https://github.com/ChristianEngel19820128/Crystals/blob/2d4ac7e61133e6be17fe26e565116223f76c9134/source/crystals/main.agc), [README.md](https://github.com/ChristianEngel19820128/Crystals/blob/2d4ac7e61133e6be17fe26e565116223f76c9134/README.md)
- Licensing reference: [Crystals LICENSE](https://github.com/ChristianEngel19820128/Crystals/blob/2d4ac7e61133e6be17fe26e565116223f76c9134/LICENSE) (Unlicense)

**Code-reviewed comparison:** Crystal Columns also uses a custom timer with `1000.0/Span`. Its platform sync configuration is project-specific and its grid-game movement model is not interchangeable with SonecBot's scrolling platformer.

## Later sources

Other games mentioned for possible future review (including Subspace, Blizz and Clone) are **not covered by v0.1**. Do not create source-specific findings for them until their actual files, licenses and tested behavior have been independently checked.

## Keeping citations accurate

Prefer links pinned to a specific commit for reproducibility. A moving `main` or `develop` URL may show a newer implementation. When adding fresh experiments, date the result, state its own source SHA and leave older verified observations intact unless corrected by better evidence.

## License of this knowledge base

The Knowledge Base itself uses the [Unlicense](../LICENSE), as provided when the repository was created. This document does not modify the licenses of any reference project.
