# Rendering, VSync, and pixel scaling

**Scope:** Windows observations from SonecBot, with custom sprite drawing and diagnostics. **Not** a universal ranking of AGK renderers or graphics cards.

**Evidence:** Local developer visual tests, pinned source review, and unresolved explanations.

## 1. A VSync request is not proof of synchronized presentation

In SonecBot, `SetVSync(1)` was requested on Windows in selected tests, while the F2 diagnostics displayed the selected renderer and the application's requested sync call. These values are helpful context, but do not independently prove that every displayed frame was tear-free.

SonecBot's Windows reference build currently contains `#renderer "Basic"`, selecting the OpenGL-oriented AGK renderer. Earlier comparisons also used Vulkan (AGK Advanced/automatic selection). The developer reported that OpenGL looked calmer than Vulkan **on the specific tested system**:

- GPU: AMD Radeon RX 6600 XT
- Monitor: Lenovo T24t-20
- Tested refresh settings: approximately 59.94/60 Hz and 75 Hz
- Observation: OpenGL generally appeared smoother, especially at 75 Hz; Vulkan exhibited more noticeable artifacts in some tests

This is a **locally tested comparison**, not a measured latency benchmark or an official performance claim. The exact current driver, AGK and OS versions were not recorded with every observation.

**References:** [renderer and sync setup](https://github.com/ChristianEngel19820128/SonecBot/blob/6e10b3dcea542071aa8c9c7b2a35f2a3792d3be0/source/sonec/main.agc#L1-L70), [technical findings](https://github.com/ChristianEngel19820128/SonecBot/blob/6e10b3dcea542071aa8c9c7b2a35f2a3792d3be0/TECHNICAL.md).

## 2. Fractional scale factors matter visually

The observed test window was 1350 × 675 for a 900 × 450 virtual resolution, corresponding to **1.5 physical pixels per virtual pixel**. A moving edge can land between physical pixel boundaries, so different raster and sampling paths may look different.

That makes fractional scaling a **candidate contributing factor**, not a proven root cause for all visible jitter or tearing.

SonecBot has an F4 debug comparison:

- **A — FLOAT:** Preserve floating-point positions in its sprite draw calls.
- **B — PIXEL SNAP:** Round a temporary drawing copy to the physical pixel grid for platforms and decorative towers.

Crucially, the experiment does **not** automatically snap chests, walkers, physics or collision coordinates. A platform and its attached chest can therefore be rendered using different raster choices. Do not generalize a platform-only comparison to every object.

**Source:** [sprite positioning and snapping](https://github.com/ChristianEngel19820128/SonecBot/blob/6e10b3dcea542071aa8c9c7b2a35f2a3792d3be0/source/sonec/sprite.agc#L61-L105).

## 3. High-contrast edges can amplify perceived shimmer

A separate graphic change reduced the bright green contrast of platform and chest edges. At 60 FPS with VSync and in a 120 FPS comparison, the developer reported that the scene became substantially calmer; remaining edge shimmer was only obvious on close inspection.

There was a similar earlier observation with particularly bright building edges. This suggests that **asset design and rendering settings must be tested separately**.

These changes did not establish that underlying movement timers, VSync or AGK had been defective. A quieter image can improve perceived stability while motion calculations stay identical.

**Asset revision:** [SonecBot commit 88f5575](https://github.com/ChristianEngel19820128/SonecBot/commit/88f55755bda991e4a7601a1c46c5e2a7e1285982).

## Practical test matrix

Keep other variables fixed as you compare:

| Variable | Example comparison |
| --- | --- |
| Renderer | OpenGL-oriented Basic vs Vulkan-oriented Advanced |
| Sync | VSync requested vs fixed sync-rate request |
| Frame rate / refresh | 60 FPS vs 120 FPS; 60 Hz vs 75 Hz |
| Sprite placement | Float vs pixel-snap draw copy |
| Asset | Original high-contrast edge vs softened edge |
| Camera | Stationary vs scrolling |
| Object | Platform alone vs platform with attached chest |

Log what was **requested** separately from what was **measured**. A screenshot alone does not conclusively show moving tearing, and the absence of obvious shimmer does not establish perfect frame pacing.

## Open tests

Repeat on other GPUs, monitors and browsers; capture actual frame pacing where possible; confirm HTML5 behavior independently.
