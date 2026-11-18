# Experiment: growing flame circle vs. static collision box

**Work status:** Published  
**Evidence status:** **Locally tested** for the first flame-to-chest implementation; broader shared-radius behavior **code-reviewed**, with continued gameplay regression testing needed.  
**Reference game:** SonecBot
**Source:** [SonecBot at 6e10b3d](https://github.com/ChristianEngel19820128/SonecBot/tree/6e10b3dcea542071aa8c9c7b2a35f2a3792d3be0)

## Problem

The SonecBot flame particles are randomly rotated when created and visually grow over their lifetime. Earlier custom collision bounds had two separate problems:

1. The flame AABB was scaled twice during movement, so its calculated width/height became smaller than the size established at spawn.
2. Even after fixing that mistake, the fixed size saved at spawn did not reflect the later growth of the visible flame.

As a result, an apparently overlapping flame and chest could fail the custom rectangle-against-rectangle hit test.

These are game-code issues, **not** verified faults in AGK's native sprite shape system.

## Target geometry

- **Chest:** Axis-aligned rectangle with previously corrected bounds.
- **Flame:** Center and circular approximation; the radius follows the current visual scale.
- **Distance:** Existing custom `CalcRadius()` function for the distance from the circle center to the nearest point on the chest rectangle.
- **Hit:** The distance is less than or equal to the current flame radius.

Schematic pseudocode (an explanation, not AGK-ready drop-in source):

~~~text
r = initialRadius * currentScale / initialScale
nearest = (
    clamp(flame.x, chest.left, chest.right),
    clamp(flame.y, chest.top, chest.bottom)
)
hit = distance(flame.position, nearest) <= r
~~~

The original flame starts at scale **0.15**, later growing toward **1.0**; that initial value is part of the current custom radius relationship.

**Implementation history:**
- [Flame AABB double-scaling fix be6c33d](https://github.com/ChristianEngel19820128/SonecBot/commit/be6c33d839a3b8242792ecc8230c7217d05bef39)
- [Flame circle-to-chest test 2879d7c](https://github.com/ChristianEngel19820128/SonecBot/commit/2879d7cc4a1f46d56b3d2b2125a78915cef32798)
- [Radius helper applied to other flame checks 2b2ec8c](https://github.com/ChristianEngel19820128/SonecBot/commit/2b2ec8c705a080dbb3cf3701e7e9784640a06f3e)

## Local test report

The developer tested the first circle-to-chest version and reported:

| Check | Observation | Status |
| --- | --- | --- |
| Small chests | Flame hits were detected | Locally tested |
| Chest sides and corners | Contacts behaved as expected visually | Locally tested |
| Medium/large chests | Damage did not appear excessively strong | Preliminary visual judgment |
| 60 FPS VSync / 120 FPS | Behavior looked normal in those comparisons | Preliminary local test |

After the same growing radius was applied to additional flame checks, the developer reported that the **flamethrower felt stronger**. The extent and target-specific contribution are **not quantified**.

## What stayed unchanged

The test did not deliberately alter projectile movement, the lifecycle growth formula, the nominal per-hit damage or the existing particle deletion rules. The hit geometry itself **does** affect the number of overlaps and therefore the resulting damage in practice.

## Special considerations

- A circular approximation may cover transparent corners of a rotated flame sprite. It is not pixel-perfect.
- A larger radius can cause earlier hits against Mines or Orbs. These paths need their own local tests.
- The flamethrower is planned as a **counter to mines**. A future distance-based damage falloff must be evaluated against that role.
- Weapon types and armor are planned gameplay dimensions, not yet established here.
- Native AGK sprite shape/collision APIs are a potential future comparison, but this code uses a custom sprite wrapper and reuses prototype sprite IDs; swapping systems is not a one-line change.

## Suggested follow-up tests

1. Compare hit distance at young, middle-aged and fully grown flames.
2. Repeat on small/large chests and both circular target types (Mines, Orbs).
3. Inspect cases where one flame overlaps multiple objects in one update.
4. Separate changes in collision *reach* from changes in weapon *damage*.
5. Only afterward compare with isolated native AGK sprite collision shapes.
