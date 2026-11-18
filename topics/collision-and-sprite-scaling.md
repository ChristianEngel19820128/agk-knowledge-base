# Collision geometry and sprite scaling

**Work status:** Published  
**Evidence status:** Specific code fixes are code-reviewed. The flame-circle-to-chest experiment was subsequently **locally tested** by the developer. Other updated flame targets need further regression tests.  
**Scope:** Collision math and custom object management in SonecBot. These findings concern **game-defined** collision calculations; they are not evidence of a bug in AGK's built-in sprite collision system.

## 1. Do not multiply an already scaled size by its scale again

SonecBot's chest creation code assigns dimensions from an image width/height multiplied by `SpriteData.Scale`. A subsequent chest-movement routine formerly multiplied those saved dimensions by the scale **again** when calculating right/bottom collision bounds.

For a sprite at 0.2 scale, a second scale would give dimensions equal to only 0.2 of the already scaled dimensions. The result was a collision area smaller than intended.

**Correction:** Use the already-scaled `ObjectData.Size` directly to compute the updated chest bounds.

- [Chest fix commit a4be9ab](https://github.com/ChristianEngel19820128/SonecBot/commit/a4be9ab7a91d30581390e01e99323cca35755972)
- [Current chest source](https://github.com/ChristianEngel19820128/SonecBot/blob/6e10b3dcea542071aa8c9c7b2a35f2a3792d3be0/source/sonec/chest.agc#L247-L265)

A similar double-scaling mistake occurred in **all four corners** of the custom flame AABB after movement. Its initial size was already scaled to 0.15, so multiplying again reduced the checked rectangle to 15% of that initial width and height. This was corrected in [be6c33d](https://github.com/ChristianEngel19820128/SonecBot/commit/be6c33d839a3b8242792ecc8230c7217d05bef39).

## 2. An animated sprite can grow while its stored collision size does not

Each SonecBot flame particle begins at sprite scale 0.15; over its lifecycle the *visible* scale approaches 1.0. Its stored initial object radius does not automatically change with the sprite draw scale.

A collision check that only uses the spawn-time radius can therefore miss visible contact with a larger flame.

The current custom helper derives a radius proportional to the **current visual scale**:

~~~text
currentRadius = radiusAtSpawn * (currentSpriteScale / scaleAtSpawn)
~~~

In this project, `scaleAtSpawn = 0.15`. This formula is local to the implementation and assumes the stored radius represents the starting size. If the particle-generation code changes, update the relationship too.

**Source:** [flame source](https://github.com/ChristianEngel19820128/SonecBot/blob/6e10b3dcea542071aa8c9c7b2a35f2a3792d3be0/source/sonec/flame.agc#L38-L95) and [shared radius helper](https://github.com/ChristianEngel19820128/SonecBot/blob/6e10b3dcea542071aa8c9c7b2a35f2a3792d3be0/source/sonec/flame.agc#L158-L166).

## 3. Circle against rectangle: use the nearest point

For a circular particle centered at `(cx, cy)` and an axis-aligned box `[xmin,xmax] × [ymin,ymax]`:

~~~text
nearX = clamp(cx, xmin, xmax)
nearY = clamp(cy, ymin, ymax)
hit = distance((cx, cy), (nearX, nearY)) <= currentRadius
~~~

The closest point handles side contact, corners and a circle whose center lies inside the rectangle. In SonecBot, the distance reuses the existing `CalcRadius()` function. The chest remains rectangular because its physical geometry is a box; the rotating flame is approximated by a circle independent of its visual angle.

The radius is based on the larger of the stored image dimensions at spawn. This is a **circumscribed circular approximation**, not a pixel-perfect check of a transparent or asymmetric flame image. A larger collision shape may also affect balance.

**Local result for chest hits:** The developer confirmed that small chests and corner/edge contacts were detected; the damage did not look excessively strong at first and the 60/120 FPS comparisons appeared normal.

**Sources:** [experiment](../experiments/circle-vs-box-collision.md), [first circle commit 2879d7c](https://github.com/ChristianEngel19820128/SonecBot/commit/2879d7cc4a1f46d56b3d2b2125a78915cef32798).

## 4. Reuse geometry carefully across target types

The shared flame-radius helper is also used against Orbs, Mines and the currently unused Sonec flame-collision function. Orbs and Mines still use **circle-vs-circle** checks; chest collisions use **circle-vs-box**. The same radius does not mean every target needs the same shape.

After this change the developer reported the flamethrower felt **stronger**. That is a gameplay observation, not yet a controlled damage/range measurement. Future balancing may reduce damage with distance while preserving the planned flamethrower role as a counter to mines. Armor types, weapon types and damage resistances are not yet settled.

**Source:** [shared-radius commit 2b2ec8c](https://github.com/ChristianEngel19820128/SonecBot/commit/2b2ec8c705a080dbb3cf3701e7e9784640a06f3e).

## 5. A missed projectile is not automatically tunneling

The developer saw some bullets fly **above** the smallest chests. That observation does not show a bullet passing *through* a collision region. Earlier destruction may have come from an explosion, but that was not established.

Tunneling—skipping an object between successive discrete checks—remains a separate testable concern for fast-moving bullets; do not classify every visual miss as tunneling.

## Open questions

- Does the unified, growing flame radius hit Orbs and Mines at an acceptable range?
- Should distance, weapon type and armor alter damage independently of geometry?
- Can a minimal isolated AGK sprite-shape test reproduce or improve the geometry without changing the game architecture?
- How should projectile collision ownership and damage types be represented?
