# Browser exit versus desktop quit in an AppGameKit Studio game

**Work status:** Review  
**Evidence status:** Code-reviewed implementations in SonecBot and Crystals; developer-reported HTML5 behavior and historical correction  
**Scope:** Custom exit handling in two AGK projects. **Not** an independently isolated or universal AGK engine defect.  
**Pinned source revisions:** [SonecBot `2ddb1c93963dc38bbe3c17260e9bb7a6f8b40d8c`](https://github.com/ChristianEngel19820128/SonecBot/tree/2ddb1c93963dc38bbe3c17260e9bb7a6f8b40d8c); [Crystals ESC fix `f2471121b5d7fc65e232d77cb236ebd6b4d71b1c`](https://github.com/ChristianEngel19820128/Crystals/commit/f2471121b5d7fc65e232d77cb236ebd6b4d71b1c)  
**Environment:** Developer-reported Windows and HTML5/browser work. Exact browser/AGK versions, error message and affected export hashes: **not recorded** for the historical incident.

## The situation

Closing a desktop game is not the same thing as leaving a browser page or an embedded HTML5 game. Reusing an existing ESC-to-quit shortcut without checking the platform caused problems during the developer's early Crystals HTML5 work. The developer recalls an error/exception-like result when the program attempted to terminate in the browser; the exact message and triggering runtime path have **not been independently recovered**.

Two related game projects now use a small **custom wrapper** to avoid treating ESC as a program-exit request on HTML5. SonecBot additionally prevents its menu QUIT flag from terminating the HTML5 main loop.

## 1. Check the actual ESC wrapper before interpreting the main loop

SonecBot defines:

~~~agk
Function GetESCPressed()
    Local result as Integer
    If Device$ = "windows" and GetKeyPressed(Key_Escape) then result = 1
Endfunction result
~~~

**Source:** [SonecBot `controls.agc`, lines 65–68](https://github.com/ChristianEngel19820128/SonecBot/blob/2ddb1c93963dc38bbe3c17260e9bb7a6f8b40d8c/source/sonec/controls.agc#L65-L68).

This is **not** AGK's built-in `GetESCPressed()` function: it is the **project's own named wrapper**. On HTML5, its `Device$ = "windows"` condition is false, so it returns its default value (0) instead of triggering an application-exit request.

The SonecBot main loop uses this wrapper:

~~~agk
until GetESCPressed() = 1 or (Device$ <> "html5" and Intro.IsQuit = TRUE)
~~~

**Source:** [SonecBot `main.agc`, lines 275–279](https://github.com/ChristianEngel19820128/SonecBot/blob/2ddb1c93963dc38bbe3c17260e9bb7a6f8b40d8c/source/sonec/main.agc#L275-L279).

The two loop-exit paths therefore behave as follows:

| Exit condition | Windows | HTML5 |
| --- | --- | --- |
| Custom `GetESCPressed() = 1` | May end the loop after ESC | Returns 0: the wrapper ignores ESC for program exit |
| `Intro.IsQuit = TRUE` via the game's QUIT menu | May end the loop | Explicitly ignored by the `Device$ <> "html5"` condition |

Selecting QUIT sets `Intro.IsQuit = TRUE` in [SonecBot `intro.agc`, lines 790–794](https://github.com/ChristianEngel19820128/SonecBot/blob/2ddb1c93963dc38bbe3c17260e9bb7a6f8b40d8c/source/sonec/intro.agc#L790-L794).

**Important distinction:** The `GetESCPressed()` **call is present** in the HTML5 loop condition, but its **implementation blocks the ESC result** on that platform. Reading only the loop line without inspecting the wrapper gives the wrong explanation.

## 2. Historical comparison: the Crystals HTML5 correction

The earlier Crystal Columns (Crystals) project contains essentially the same wrapper:

~~~agk
Function GetESCPressed()
    Local result as Integer
    If Device$ = "windows" and GetKeyPressed(Key_Escape) then result = 1
Endfunction result
~~~

**Source:** [Crystals `controls.agc` at the historical fix, lines 51–54](https://github.com/ChristianEngel19820128/Crystals/blob/f2471121b5d7fc65e232d77cb236ebd6b4d71b1c/source/crystals/controls.agc#L51-L54).

The documented [Crystals commit `f247112`](https://github.com/ChristianEngel19820128/Crystals/commit/f2471121b5d7fc65e232d77cb236ebd6b4d71b1c) introduced the `controls.agc` include and explicitly noted: **ESC now checks the Windows device because HTML5 would otherwise break**. The game's main loop calls the wrapper in its [exit condition](https://github.com/ChristianEngel19820128/Crystals/blob/f2471121b5d7fc65e232d77cb236ebd6b4d71b1c/source/crystals/main.agc), with its own additional game-state check.

This is concrete **historical code and commit evidence** that the Windows-only ESC guard was an intentional fix in Crystals. The developer additionally recalls an error or exception-like behavior when exiting the program inside a browser. The commit itself does not establish a precise exception class, browser console message or AGK-internal root cause, so this article does not invent one.

Crystals supplies the historical correction; SonecBot shows the same pattern being reused in a later project. That makes the lesson more transferable, while still requiring tests for other environments.

## 3. Reproduce the relevant behavior

For another developer investigating a desktop-to-HTML5 port:

1. Record the AGK version, browser/OS, deployment (standalone page or embed), source revision and build revision.
2. Identify whether an exit condition uses **raw ESC input**, a custom wrapper or a menu flag. Read the called helper implementation; names alone are not evidence.
3. On Windows, check whether ESC and the intended QUIT action exit the native application.
4. On HTML5, check the corresponding game actions **without attempting to close the browser tab**. If a failing exit path is already known, reproduce only in a disposable test build and record the actual browser error/console output.
5. Compare the guarded and unguarded paths if safe and relevant; record the affected AGK/browser versions rather than assuming universal behavior.
6. If players need an HTML5 exit/back action, design a browser-appropriate interface separately and test it in the actual hosting environment.

These are **recommended future checks**; the complete A/B browser/error matrix is not contained in the historical evidence.

## When to use this / when not to use this

**Useful when:**

- An AGK game runs on Windows but attempts to exit incorrectly after an HTML5 export.
- A developer sees a loop-level `GetESCPressed()` call and needs to know whether a **custom wrapper** already restricts it by platform.
- The game should distinguish a native quit command from leaving a browser page or an iframe.
- A project wants to reuse a historically documented correction from Crystals without blindly copying assumptions.

**Do not assume:**

- ESC itself is invalid in HTML5; the concern here is **using ESC to terminate the application**.
- Ending an HTML5 game loop reliably closes the browser tab or iframe.
- These two game implementations prove a specific universal AGK engine exception.
- A Windows-only ESC wrapper is automatically suitable for all non-HTML5 native platforms or all games.
- Browser navigation, a return-to-title action and application termination are interchangeable.

## Known limitations and follow-up

- The developer recalls an error/exception-like response to program exit in the earlier browser build; its exact message, stack trace, browser, AGK version and test revision have not been recovered.
- The historical Crystals commit demonstrates that the Windows-only ESC check was an intentional fix, **not** which low-level browser or AGK exit mechanism failed.
- HTML5 may still need a deliberately designed **back, restart or return-to-host** UX; this article neither implements nor approves one.
- No new Windows/HTML5 build was compiled or run for this Knowledge Base review.

## References

- [SonecBot pinned ESC wrapper](https://github.com/ChristianEngel19820128/SonecBot/blob/2ddb1c93963dc38bbe3c17260e9bb7a6f8b40d8c/source/sonec/controls.agc#L65-L68)
- [SonecBot pinned main-loop exit condition](https://github.com/ChristianEngel19820128/SonecBot/blob/2ddb1c93963dc38bbe3c17260e9bb7a6f8b40d8c/source/sonec/main.agc#L275-L279)
- [SonecBot pinned QUIT handler](https://github.com/ChristianEngel19820128/SonecBot/blob/2ddb1c93963dc38bbe3c17260e9bb7a6f8b40d8c/source/sonec/intro.agc#L790-L794)
- [Crystals historical fix commit `f247112`](https://github.com/ChristianEngel19820128/Crystals/commit/f2471121b5d7fc65e232d77cb236ebd6b4d71b1c)
- [Crystals `controls.agc` at the fix](https://github.com/ChristianEngel19820128/Crystals/blob/f2471121b5d7fc65e232d77cb236ebd6b4d71b1c/source/crystals/controls.agc#L51-L54)
- [Testing methodology](testing-methodology.md)
- [Knowledge Base TODO](../TODO.md)
