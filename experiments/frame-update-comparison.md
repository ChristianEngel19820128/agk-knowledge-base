# Experiment: 10 ms movement gate vs. every-positive-update

**Reference game:** SonecBot, Windows test setup
**Status:** **Locally tested** for visual comparisons, **code-reviewed** for implementation; specific cause of residual jitter remains unproven.
**Original source revision:** [SonecBot 6e10b3d](https://github.com/ChristianEngel19820128/SonecBot/tree/6e10b3dcea542071aa8c9c7b2a35f2a3792d3be0)

## Question

Does moving a platform only when a custom 10 ms timer gate fires produce visibly different motion from advancing it on every update with positive elapsed milliseconds?

## Background

The original SonecBot movement timer uses a strict condition equivalent to `Now > previousStamp + 10`. At around 120 game updates per second, a single frame is commonly shorter than 10 ms; at around 60 updates per second it is ordinarily longer. The gate may therefore skip some 120 FPS updates, potentially creating uneven visible steps.

The experiment isolates platform movement. It does **not** switch every game object to the same update schedule.

## Test conditions and controls

**Reported locally:**

- Windows development build
- Comparisons at approximately **60 FPS** and **120 FPS**
- F2 debug overlay enabled to permit F6 A/B switch
- Platform movement:
  - **A:** original 10 ms gated movement
  - **B:** every positive elapsed update using delta time
- Separate observations at **75 Hz** and other refresh settings, with sync behavior depending on test conditions

**Not consistently recorded:** Exact AGK version, driver version, every frame-time sample and a frame-by-frame captured video with synchronized measurement.

## Implementation checks

- Both modes advance from elapsed time and share the same position/collision update block.
- A can leave the position unchanged in a game frame without a timer event.
- B accepts positive elapsed milliseconds without waiting for the 10 ms threshold.
- The platform default returns to A when the F2 overlay is dismissed; B is for comparison, not yet the global default.

**Source:** [plate movement and gate](https://github.com/ChristianEngel19820128/SonecBot/blob/6e10b3dcea542071aa8c9c7b2a35f2a3792d3be0/source/sonec/plate.agc#L209-L290).

## Observations

1. At around **120 FPS**, mode B looked less jittery than A in the reported comparison.
2. At around **60 FPS**, their perceived behavior was much closer.
3. In a separately reported refresh-rate comparison, **75 Hz** could look especially calm in the particular setup.
4. Reducing contrast of bright platform and chest sprite edges subsequently produced a major visual improvement at 60/120 FPS, independently of this movement switch.

These results indicate that update cadence and sprite presentation can both be worth investigating. They do **not** prove a single source of all apparent jitter or screen tearing.

**Asset evidence:** [softened sprite contrast commit 88f5575](https://github.com/ChristianEngel19820128/SonecBot/commit/88f55755bda991e4a7601a1c46c5e2a7e1285982).

## Reproduction plan for other developers

1. Run one moving platform at a constant speed in a stable scene.
2. Record timer span, actual elapsed ms and position delta for each game update.
3. At the same window size, compare 60 FPS and 120 FPS, with the renderer fixed.
4. Toggle the movement gate without changing acceleration, speed, sprite asset or collision logic.
5. Repeat with high-contrast versus softened sprite edges as a **separate** A/B experiment.
6. Check attached objects, collision bounds and edge reversals before treating B as a replacement.

## Interpretation limits

- The number of game updates is not the same as display refresh.
- `SetVSync(1)` reports an API request, not guaranteed tear-free presentation.
- A custom `TimeGet()` gate is not an AGK engine defect.
- Subjective smoothness needs to be supported by logs or recordings before assigning a root cause.
- The HTML5 result is **not established** by these Windows comparisons.
