# Timing and frame rates in AGK projects

**Work status:** Published  
**Evidence status:** Code-reviewed at pinned revisions; selected visual comparisons locally tested by the SonecBot developer; causality for the original perceived jitter remains a hypothesis.  
**Scope:** Custom timer and update code used by SonecBot and Crystal Columns; not a claim that AGK itself has a timing defect.

## 1. Timer gates are not the same as per-frame movement

SonecBot's custom `TimeGet()` returns an elapsed interval only when `Now > Stamp + Span` (strictly greater). Its `TimebasedCalc()` derives the movement factor from actual elapsed milliseconds; `TimeReset()` then advances the stamp.

For a **10 ms gate**, an update loop running at approximately 120 FPS has an average frame interval of about 8.3 ms. Some frames can therefore pass without a movement step. At approximately 60 FPS (16.7 ms/frame), the gate will ordinarily be satisfied every frame. This is a deduction from the custom code, not a measured guarantee about every frame.

**Sources:** [SonecBot timer at pinned revision](https://github.com/ChristianEngel19820128/SonecBot/blob/6e10b3dcea542071aa8c9c7b2a35f2a3792d3be0/source/sonec/timer.agc#L33-L85) and [plate movement](https://github.com/ChristianEngel19820128/SonecBot/blob/6e10b3dcea542071aa8c9c7b2a35f2a3792d3be0/source/sonec/plate.agc#L209-L285).

## 2. Isolate the gate from the movement formula

SonecBot's platform experiment compares:

- **A — original 10 ms gate:** `TimeGet()` controls when `PlateMove()` advances.
- **B — every positive elapsed update:** `Now - MoveTimer.Stamp > 0` allows an update, using an elapsed-time factor.
- Both variants use the same subsequent platform position, bounds and collision-update logic.

The switch is available as **F6 while the F2 debug view is active**; the default platform mode returns to A when the debug view is closed. This is a targeted experiment, not a recommendation to change every game object globally.

**Local report:** At about 120 FPS, the developer noticed less frequent/smaller platform jitter with B; at about 60 FPS the modes were much closer. A separate reduction in bright sprite-edge contrast also made motion look noticeably calmer. Neither comparison alone proves a single root cause.

See [frame-update comparison](../experiments/frame-update-comparison.md).

## 3. Keep calculations floating-point when fractions matter

The reference projects use a multiplier equivalent to:

~~~text
multiplier = 1000.0 / spanMilliseconds
movementFactor = elapsedMilliseconds * 0.001 * range * multiplier
~~~

In an earlier SonecBot revision, the multiplier used `1000 / Span`, which could discard a fractional component in integer arithmetic. Commit [d536043](https://github.com/ChristianEngel19820128/SonecBot/commit/d536043) changed it to `1000.0 / Span` and the developer reported no regression in the 60/120 FPS comparison.

**Important:** At `Span = 10`, both calculations yield 100. The change is relevant for other spans with nonintegral quotients. The original jitter was **not** demonstrated to result from this division.

Cross-check: [Crystal Columns timer](https://github.com/ChristianEngel19820128/Crystals/blob/2d4ac7e61133e6be17fe26e565116223f76c9134/source/crystals/timer.agc#L34-L85).

## 4. Random spawning can depend on update rate

If an event is attempted **once per game-update call**, using a probability `p`, then its expected number of attempts per second rises with the number of calls per second. A separate cooldown on successful creation can cap actual spawns without making the probability of attempts time-based.

SonecBot's `GameDo()` has such random spawn-attempt checks for several object classes. This is a **code-reviewed candidate for further work**; this knowledge base does not claim that spawn rates have been measured or normalized.

A possible experiment is to use a fixed attempt cadence or a probability derived from elapsed time, then compare distribution and gameplay at different frame rates. Preserve the game's intended difficulty before replacing the rule.

**Source:** [SonecBot game loop](https://github.com/ChristianEngel19820128/SonecBot/blob/6e10b3dcea542071aa8c9c7b2a35f2a3792d3be0/source/sonec/game.agc#L169-L232).

## Open tests

- Compare gated/every-update movement with the **same assets**, refresh rate and render mode.
- Record skipped steps, actual elapsed time and movement deltas alongside video observations.
- Test whether per-update spawn probabilities change effective difficulty.
- Recheck behavior in HTML5; Windows results are not HTML5 evidence.
