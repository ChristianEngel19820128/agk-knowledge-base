# Keyboard input and safe rebinding in AppGameKit Studio

**Status:** Draft for review (Knowledge Base work status: `Review`)  
**Evidence:** Code-reviewed implementation; developer-reported Windows, local HTML5/Apache and itch.io Draft embedded tests  
**Scope:** Custom SonecBot input mapping, menu navigation, persistence and UI; **not a demonstrated defect or universal behavior of the AGK engine**  
**Pinned source revision:** [SonecBot code at `2ddb1c93963dc38bbe3c17260e9bb7a6f8b40d8c`](https://github.com/ChristianEngel19820128/SonecBot/tree/2ddb1c93963dc38bbe3c17260e9bb7a6f8b40d8c)  
**Test environment:** Developer-run AppGameKit Studio on Windows; HTML5 exported and run through a local Apache server; subsequent real itch.io Draft embed. Exact AGK Studio version, browser versions and complete hardware test matrix: **not recorded** here.

## The problem: rebinding can break the menu used to repair it

SonecBot introduced customizable gameplay keyboard bindings while retaining keyboard and gamepad menu support. In an intermediate Windows test, assigning the **same key to MOVE UP and MOVE DOWN** exposed a regression: the menu listened to the freely remapped movement actions. A single key press could therefore trigger both directions, canceling out the selection change and leaving the user unable to navigate as intended.

This is a **custom input-design regression**, not evidence that `GetKeyPressed()` or another AGK command is broken.

**Expected:** Users may assign duplicate gameplay keys, see the conflict, and still navigate to repair or reset their bindings.

**Observed in the intermediate build:** When both vertical movement actions shared `UP`, vertical menu navigation could cancel itself. The developer reported the failure during testing and subsequently verified the focused correction. See the dated test history in [SonecBot HANDOFF.md](https://github.com/ChristianEngel19820128/SonecBot/blob/develop/HANDOFF.md); the final working implementation is pinned below.

### Reproduce the regression conceptually

1. Implement a menu whose up/down commands both call remappable gameplay movement actions.
2. Bind the same physical key to both movement directions (for example `UP`).
3. Press that key while the menu is open.
4. Depending on the two sequential handlers, the selection may move up and immediately down.

**Important:** These are reproduction steps for the **pre-fix design**. The pinned revision already contains the fix; it is not expected to reproduce the failure unchanged.

## Solution: separate recovery navigation from gameplay mapping

SonecBot changed both main-menu and OPTIONS navigation to dedicated, non-remappable inputs:

- Menu up: `W` or up arrow; gamepad D-pad up or upward analog step.
- Menu down: `S` or down arrow; gamepad D-pad down or downward analog step.
- Menu selection: Enter or gamepad A.
- Menu back: Backspace or supported gamepad back controls.
- Gameplay actions remain individually configurable and can legitimately share a keyboard key.

This provides a **recovery path** even if gameplay input is duplicated or unexpectedly remapped. Dedicated navigation is a project design choice, not a requirement imposed by AGK.

### Small illustrative AGK-style example

The following is **pseudocode adapted to AGK-style syntax**, not a drop-in snippet: it omits page-specific bounds, analog handling and selection wraparound.

~~~agk
// Dedicated menu controls: do not query the remappable MOVE_UP/DOWN actions.
if GetKeyPressed(KEY_W) = 1 or GetKeyPressed(KEY_UP) = 1
    // Select the previous menu row.
endif

if GetKeyPressed(KEY_S) = 1 or GetKeyPressed(KEY_DOWN) = 1
    // Select the next menu row.
endif
~~~

**Actual implementation:** [`intro.agc`, OPTIONS navigation, lines 439–491](https://github.com/ChristianEngel19820128/SonecBot/blob/2ddb1c93963dc38bbe3c17260e9bb7a6f8b40d8c/source/sonec/intro.agc#L439-L491) and [main-menu navigation, lines 716–731](https://github.com/ChristianEngel19820128/SonecBot/blob/2ddb1c93963dc38bbe3c17260e9bb7a6f8b40d8c/source/sonec/intro.agc#L716-L731).

**Extra nuance:** The real menu handles each direction in its own `if` statement. A system receiving *both* dedicated up/down inputs in the same frame may still execute both branches; the proven fix addresses accidental overlap caused by **remappable gameplay assignments**, not every possible simultaneous physical input.

## Safely capture a new keyboard key

SonecBot uses a small modal state machine rather than treating every pressed key as a new binding immediately.

1. Enter the keyboard category (`MOVEMENT` or `ACTIONS`) and select an action.
2. Open a modal capture prompt with Enter or gamepad A.
3. Keep capture **unarmed** until the opening Enter/A input is released; otherwise the same confirm press could be consumed by the new state.
4. While capture is active, **Backspace cancels** without changing the binding.
5. After arming, scan only the approved key set; ignore Ctrl/Alt/OS-modified shortcuts.
6. On a permitted press, change that action's keyboard key, save bindings and close the prompt.
7. Do not process ordinary menu navigation in the capture frame.

The project allows exactly **41 newly captured keys**: 26 letters A–Z, 10 digits 0–9, Space and four arrow keys. This is a **deliberate project-level whitelist** rather than a list of all valid AGK keyboard inputs.

~~~agk
// Representative excerpt of SonecBot's whitelist.
if KeyCode = KEY_SPACE then Allowed = 1
if KeyCode >= KEY_LEFT and KeyCode <= KEY_DOWN then Allowed = 1
if KeyCode >= KEY_0 and KeyCode <= KEY_9 then Allowed = 1
if KeyCode >= KEY_A and KeyCode <= KEY_Z then Allowed = 1
~~~

The capture code also checks Ctrl, Alt and operating-system keys before scanning. The existing default Start/Back bindings (Enter/Backspace) are retained in the saved data even though these keys are **not offered for new capture**. Do not mistake the capture whitelist for a complete validation rule for legacy saved bindings.

**Sources:** [whitelist, modifier scan and key scan, lines 202–231](https://github.com/ChristianEngel19820128/SonecBot/blob/2ddb1c93963dc38bbe3c17260e9bb7a6f8b40d8c/source/sonec/intro.agc#L202-L231); [modal capture and release gate, lines 413–437](https://github.com/ChristianEngel19820128/SonecBot/blob/2ddb1c93963dc38bbe3c17260e9bb7a6f8b40d8c/source/sonec/intro.agc#L413-L437); [action identifiers and binding structure](https://github.com/ChristianEngel19820128/SonecBot/blob/2ddb1c93963dc38bbe3c17260e9bb7a6f8b40d8c/source/sonec/def_input.agc).

## Make duplicate bindings visible, without silently changing them

A duplicate is detected when another one of the 15 actions contains the same **nonzero keyboard code**. SonecBot colors the duplicate **key values red on both keyboard pages** while keeping the selected action label cyan.

Importantly, the implementation **does not reject or automatically swap** duplicate assignments. Showing a warning and enforcing uniqueness are different product decisions. The menu must remain navigable even when the warning is present.

**Sources:** [duplicate detection, lines 252–271](https://github.com/ChristianEngel19820128/SonecBot/blob/2ddb1c93963dc38bbe3c17260e9bb7a6f8b40d8c/source/sonec/intro.agc#L252-L271); [key-value warning in the table, lines 396–400](https://github.com/ChristianEngel19820128/SonecBot/blob/2ddb1c93963dc38bbe3c17260e9bb7a6f8b40d8c/source/sonec/intro.agc#L396-L400).

## Persist validated bindings as one versioned record

SonecBot defines **15 actions** in a `TKeys` record. Each action stores a keyboard key and a digital gamepad button; analog axes and triggers remain separate in the existing game code.

The serialized v1 payload is a *single record*:

~~~text
version|key0|pad0|key1|pad1|...|key14|pad14
~~~

That is **31 tokens** in total, rather than a separate browser value for every action.

The save path is platform-specific in this code:

| Runtime reported by AGK | SonecBot storage path |
| --- | --- |
| `GetDeviceBaseName() = "html5"` | `SaveSharedVariable("sonec_key_bindings", Data)` / `LoadSharedVariable(...)` |
| Other runtime, including the tested Windows path | `OpenToWrite("/sonec_key_bindings.dat")` / `OpenToRead(...)` |

The loader first starts with safe defaults, then parses into a **temporary candidate**, requiring the expected version/token count, numeric tokens, allowed 0–255 ranges and a complete serialization round trip. It also requires at least one nonzero keyboard/gamepad input for each essential `START` and `BACK` action. **Only then** does it copy all bindings into the active record. An empty record causes initial defaults to be saved; malformed or future-version records are not silently overwritten.

The v1 loader's range validation is intentionally broader than the 41-key capture whitelist. This distinction matters when explaining compatibility and fallback behavior.

**Source:** [`input.agc` defaults and versioned persistence, lines 53–248](https://github.com/ChristianEngel19820128/SonecBot/blob/2ddb1c93963dc38bbe3c17260e9bb7a6f8b40d8c/source/sonec/input.agc#L53-L248).

### Restore keyboard defaults without changing gamepad assignments

SonecBot's `RESTORE DEFAULTS` confirmation opens with **CANCEL selected**. Choosing RESET constructs defaults in a temporary `TKeys` record, copies **only** the `.Key` member for the 15 actions, then calls `KeyBindingsSave(Keys)`. It deliberately preserves current `.PadButton` values and separate gameplay options such as hover mode.

~~~agk
InputSetDefaultBindings(DefaultKeys)
for BindingIndex = 0 to ACTION_LAST
    Keys.Binding[BindingIndex].Key = DefaultKeys.Binding[BindingIndex].Key
next BindingIndex
KeyBindingsSave(Keys)
~~~

This is a real code excerpt, presented without surrounding menu branches.

**Source:** [`intro.agc`, RESET/CANCEL handler, lines 593–605](https://github.com/ChristianEngel19820128/SonecBot/blob/2ddb1c93963dc38bbe3c17260e9bb7a6f8b40d8c/source/sonec/intro.agc#L593-L605).

## A one-frame menu highlight regression

After implementing RESTORE DEFAULTS, the developer noticed a brief flash of an old menu highlight when entering OPTIONS from the main menu. The source review suggested that OPTIONS was activated before its text colors were initialized for the first rendered frame.

The focused change initializes the OPTIONS title, labels and GAMEPLAY highlight **in the same transition frame**. The developer later reported that the flicker was absent in the final local HTML5 and hosted itch.io tests. This supports the **observed regression fix in this build**; it does not independently prove that stale text colors were the only possible mechanism.

**Source:** [`intro.agc`, initial OPTIONS visuals, lines 762–789](https://github.com/ChristianEngel19820128/SonecBot/blob/2ddb1c93963dc38bbe3c17260e9bb7a6f8b40d8c/source/sonec/intro.agc#L762-L789).

## Developer-reported test evidence

These were **developer-run checks**, not independent AGK builds or browser sessions performed by the Knowledge Base author:

| Stage | Reported result | Scope |
| --- | --- | --- |
| Keyboard table/navigation | Windows **12/12** | Two categories, navigation and presentation |
| Rebinding/capture | Windows **10/10**, local HTML5 **12/12** | Capture, cancellation, allowed input and persistence |
| Duplicate detection/menu recovery | Final Windows regression **8/8**, local HTML5 **11/11** | Both-page duplicate warnings and fixed menu navigation |
| RESTORE DEFAULTS | Windows **12/12** before the final visual entry fix | Reset/cancel, preservation of pad input, restart and UI |
| Final local HTML5/Apache | **13/13** | Keyboard defaults, browser reload, menu flicker regression, gamepad/hover regression |
| Updated itch.io Draft embed | **12/12** | Keyboard movement/weapons, rebinding, duplicates, defaults, reload, gamepad, Alpha-Walker, death/restart |

The intermediate duplicate/menu-navigation Windows test initially exposed a failure. **Do not erase that finding from the history** just because the final regression check passed.

**Test log and caveats:** [SonecBot HANDOFF.md (development handoff, moving reference)](https://github.com/ChristianEngel19820128/SonecBot/blob/develop/HANDOFF.md) and [SonecBot TODO.md](https://github.com/ChristianEngel19820128/SonecBot/blob/develop/TODO.md). Code links above are pinned to the working implementation; the test summary is a developer-reported history rather than an independently measured compatibility matrix.

## When to use this / when not to use this

**Useful when:**

- A game permits runtime keyboard remapping but must guarantee an accessible options menu.
- A capture prompt risks consuming the key that opened it.
- Input conflicts should be communicated instead of silently rewritten.
- Saved assignments need versioning, validation and non-destructive fallback.
- Windows and HTML5 paths must be tested as distinct targets.

**Do not assume:**

- These 41 keys are a universal AGK or browser requirement.
- The chosen fixed W/S menu keys are correct for every game, accessibility need or keyboard layout.
- `SaveSharedVariable` persistence behaves identically across all browsers, origins, storage settings or private modes.
- The `PadButton` fields implement full gamepad remapping, axes or triggers.
- A project-level input regression is evidence of an AGK engine defect.

## Open questions / Real Alpha follow-up

- **Gamepad controls and rebinding:** the existing game owns some analog/trigger input separately. SonecBot's current keyboard-oriented reset does **not** reset or remap gamepad assignments. Revisit this architecture when Real Alpha gamepad work is designed and tested; do not invent a mapping in advance.
- **Broader compatibility:** different browsers, keyboard layouts, gamepads, accessibility needs and save-storage policies remain to be tested independently.
- **Error reporting:** the present save procedure validates data, but a separate user-facing storage-failure message is not established by this review.
- **Existing-article consistency:** compare this draft with [testing methodology](testing-methodology.md), [timing and frame rates](timing-and-frame-rates.md) and related articles in a **separate review step**. Record contradictions or improvements first; do not silently rewrite them.

## References

- [SonecBot pinned implementation (November 18, 2026)](https://github.com/ChristianEngel19820128/SonecBot/tree/2ddb1c93963dc38bbe3c17260e9bb7a6f8b40d8c)
- [SonecBot developer-reported acceptance and embed smoke test](https://github.com/ChristianEngel19820128/SonecBot/blob/develop/HANDOFF.md)
- [AGK Knowledge Base evidence policy](../CONTRIBUTING.md)
- [Knowledge Base TODO and open follow-ups](../TODO.md)
