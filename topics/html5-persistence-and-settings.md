# Persistent settings in AppGameKit Studio: Windows vs. HTML5

**Work status:** Review  
**Evidence status:** Code-reviewed SonecBot implementation; developer-reported local Windows and HTML5/Apache tests, plus a hosted itch.io Draft embed check for keyboard bindings  
**Scope:** Application-defined persistence of gameplay preferences and input mappings. This is an example of a project architecture, **not** a guarantee of storage availability in every browser or proof of an AGK engine defect.  
**Pinned code revision:** [SonecBot `2ddb1c93963dc38bbe3c17260e9bb7a6f8b40d8c`](https://github.com/ChristianEngel19820128/SonecBot/tree/2ddb1c93963dc38bbe3c17260e9bb7a6f8b40d8c)  
**Environment:** AGK Studio under Windows; locally exported HTML5 build served by Apache; hosted itch.io Draft embed. Exact AGK Studio/browser versions, browser storage policies and full device matrix: **not recorded**.

## The practical problem

A game can expose the **same setting** to the player on Windows and HTML5 but require a **different storage API** for each target. The settings must also survive restarting the game or reloading the page, and malformed or future-version data must not make the game unusable.

SonecBot handles this by separating **what to save** (small, versioned records) from **where to save it** (a platform-dependent storage path). The two data groups are independent:

| Data group | Version 1 payload | HTML5 shared-variable name | Non-HTML5 file |
| --- | --- | --- | --- |
| `GameOptions` | `1|0` = hover HOLD; `1|1` = hover TOGGLE | `sonec_game_options` | `/sonec_game_options.dat` |
| `KeyBindings` | `1|key0|pad0|...|key14|pad14` (31 tokens) | `sonec_key_bindings` | `/sonec_key_bindings.dat` |

These names and formats belong to **SonecBot's own implementation**, not to AGK itself.

**Sources:** [`input.agc`, options storage, lines 8–65](https://github.com/ChristianEngel19820128/SonecBot/blob/2ddb1c93963dc38bbe3c17260e9bb7a6f8b40d8c/source/sonec/input.agc#L8-L65); [bindings format/storage, lines 120–248](https://github.com/ChristianEngel19820128/SonecBot/blob/2ddb1c93963dc38bbe3c17260e9bb7a6f8b40d8c/source/sonec/input.agc#L120-L248).

## 1. Select the backend, not a different settings format

In the reviewed code, `GetDeviceBaseName() = "html5"` selects `SaveSharedVariable` and `LoadSharedVariable`. All other device names use AGK file operations such as `OpenToWrite`, `WriteLine`, `OpenToRead` and `ReadLine`.

For example, SonecBot's GameOptions save path contains:

~~~agk
Data = Str(Options.Version)+"|"+Str(Options.HoverMode)
if GetDeviceBaseName() = "html5"
    SaveSharedVariable("sonec_game_options",Data)
else
    FileID = OpenToWrite("/sonec_game_options.dat")
    if FileID > 0
        WriteLine(FileID,Data)
        CloseFile(FileID)
    endif
endif
~~~

This is a **real excerpt** from the pinned source, shown without its preceding value-validation checks. Do not copy the excerpt alone and assume invalid values are rejected.

**Source:** [`GameOptionsSave()`, lines 42–65](https://github.com/ChristianEngel19820128/SonecBot/blob/2ddb1c93963dc38bbe3c17260e9bb7a6f8b40d8c/source/sonec/input.agc#L42-L65).

**Design lesson:** Keep one logical data contract per settings group, then choose its persistence backend at the edge. This avoids implementing unrelated rules for Windows and HTML5.

**Boundary:** The project uses the file path for every non-HTML5 device name. This article documents the tested Windows branch; it does **not** establish identical behavior on every other native platform.

## 2. Initialize safe defaults before attempting to load

The startup order matters:

~~~agk
InputSetDefaultBindings(Keys)
KeyBindingsLoad(Keys)

InputSetDefaultGameOptions(Options)
GameOptionsLoad(Options)
~~~

The code establishes a valid in-memory baseline **first**, and only then attempts to replace its values with stored preferences.

**Source:** [`main.agc`, lines 142–152](https://github.com/ChristianEngel19820128/SonecBot/blob/2ddb1c93963dc38bbe3c17260e9bb7a6f8b40d8c/source/sonec/main.agc#L142-L152).

### GameOptions: accept only two exact version-1 values

`InputSetDefaultGameOptions()` sets version 1 and hover mode TOGGLE. `GameOptionsLoad()` changes the hover mode only if the loaded string equals `"1|0"` or `"1|1"`. An empty, malformed or unknown-version string therefore leaves the initialized default in place.

`GameOptionsSave()` checks both the version and the allowed hover values before attempting a write.

**Source:** [`input.agc`, lines 8–65](https://github.com/ChristianEngel19820128/SonecBot/blob/2ddb1c93963dc38bbe3c17260e9bb7a6f8b40d8c/source/sonec/input.agc#L8-L65).

### KeyBindings: validate the entire record before applying it

`KeyBindingsLoad()` uses a temporary `Candidate` object. It checks:

- Expected version and **31-token** structure.
- Numeric token text, permitted 0–255 key/pad ranges and a full serialization round trip.
- A remaining keyboard or gamepad binding for each essential START and BACK action.

Only after validation does the loader copy **all 15 pairs** into the active bindings. Invalid existing records are not partially applied or silently overwritten; safe initialized defaults remain active. If **no record exists**, the defaults are initially saved once.

This is intentionally different from blindly accepting `Val()` results or applying each token as soon as it is parsed.

**Source:** [`input.agc`, lines 175–248](https://github.com/ChristianEngel19820128/SonecBot/blob/2ddb1c93963dc38bbe3c17260e9bb7a6f8b40d8c/source/sonec/input.agc#L175-L248).

For action-level remapping, its modal UI, duplicate-key warnings and keyboard-only RESET, see the already published [Keyboard input and safe rebinding](keyboard-input-and-rebinding.md). Those details are **not duplicated** here.

## 3. Test storage separately on each runtime

A code path that selects the intended storage API is only **code-reviewed** until the game is run. Reusing the same serialization format does not prove that browser storage will always be available.

The following is a **developer-reported acceptance history**, not an independent test performed by the author:

| Stage | Reported observation | Limits |
| --- | --- | --- |
| `GameOptions` on Windows | Hover HOLD/TOGGLE survived full program restarts; **12/12** stage checks including Windows and local HTML5 were accepted | Results apply to the reported development setup |
| `GameOptions` on local HTML5/Apache | Fresh export loaded defaults; HOLD/TOGGLE survived page reload on that origin | Does not automatically prove persistence in hosted embeds |
| `KeyBindings` on Windows | Stored record/format inspected, and **10/10** stage checks eventually passed, including valid edited binding and unknown-version fallback | The earlier 8/10 intermediate report recorded two tests not yet executed, not two code failures |
| `KeyBindings` on local HTML5/Apache | Separate binding value survived reload in the tested setup | Browser/version, storage permissions and expiration behavior were not established as a matrix |
| Updated keyboard-enabled itch.io Draft embed | Developer reported **12/12** smoke checks, including saved keyboard bindings after full reload | This is a specific hosted build/session, not proof for every iframe, browser or privacy configuration |

**Test provenance:** [SonecBot development handoff](https://github.com/ChristianEngel19820128/SonecBot/blob/develop/HANDOFF.md) and [SonecBot project TODO](https://github.com/ChristianEngel19820128/SonecBot/blob/develop/TODO.md). These are **moving historical logs**. The exact binary export SHA for every reported session was not independently recorded; the implementation links in this article refer to the pinned code revision above.

### Reproduction checklist for another developer

1. Record the **actual source revision**, AGK Studio version, OS, browser, origin and tested storage policy.
2. Start without saved preferences. Confirm safe defaults and the initial stored record.
3. Change one **GameOptions** value, fully exit/restart Windows or reload the HTML5 page, and check that the value returns.
4. Test **KeyBindings separately**: change a valid binding, restart/reload and check the gameplay mapping, not just a fixed menu navigation key.
5. With the application stopped and a backup available, test an invalid version or malformed record. Confirm complete fallback without partial changes.
6. Repeat the relevant checks in a **hosted embed** if the game is distributed that way. Record that result independently from local Apache.
7. Restore original test data and note any storage permissions, browser cleanup or privacy-mode restrictions encountered.

This is a **proposed reproducible test sequence**, not a claim that every item has already been run in every environment.

## 4. What this design does not establish

- **Persistence across browser contexts:** Origin, embed behavior, browser privacy settings, storage permissions, expiration and cleanup may affect the observed result. One successful reload is not a universal compatibility guarantee.
- **Guaranteed write success:** The reviewed save routines perform validation and attempt a write, but this review does **not** establish a complete user-facing failure notification or a read-back verification for every write.
- **Automatic schema migration:** Version fields allow format changes to be detected, but this v1 loader **does not migrate** a future format. A new version requires its own explicit parser/migration decision.
- **Fully remappable gamepad input:** Digital pad values are stored with key codes, while analog axes/triggers remain in existing game code. Real Alpha work may later change this design.
- **An AGK engine limitation:** These are observed project storage choices and tests, not evidence that AGK's built-in APIs are defective.

## When to use this / when not to use this

**Useful when** a game shares configurable options between Windows and HTML5, needs safe fallback for invalid data, or wants to test browser persistence without making assumptions from desktop results.

**Do not treat it as** a drop-in cross-platform storage library, a guarantee that shared variables survive every browser setting, or an argument that each individual option should have its own persistent key. SonecBot intentionally stores related values in **two compact versioned groups**.

## Open follow-ups

- Test additional browsers, browser privacy/storage modes, origins and hosted iframe conditions with explicit environment records.
- Decide on handling and reporting failed writes if a project needs stronger storage guarantees.
- When a future settings schema changes, document the migration strategy rather than silently changing v1 parsing.
- Revisit gamepad mapping only after the Real Alpha implementation and its tests provide evidence.
- Review this draft against the existing Keyboard input article and [testing methodology](testing-methodology.md) before changing its work status to Published.

## References

- [SonecBot pinned code revision](https://github.com/ChristianEngel19820128/SonecBot/tree/2ddb1c93963dc38bbe3c17260e9bb7a6f8b40d8c)
- [SonecBot `input.agc` – storage implementation](https://github.com/ChristianEngel19820128/SonecBot/blob/2ddb1c93963dc38bbe3c17260e9bb7a6f8b40d8c/source/sonec/input.agc)
- [SonecBot `main.agc` – initialization order](https://github.com/ChristianEngel19820128/SonecBot/blob/2ddb1c93963dc38bbe3c17260e9bb7a6f8b40d8c/source/sonec/main.agc)
- [SonecBot developer-reported test log](https://github.com/ChristianEngel19820128/SonecBot/blob/develop/HANDOFF.md)
- [Keyboard input and safe rebinding](keyboard-input-and-rebinding.md)
- [Knowledge Base TODO](../TODO.md)
