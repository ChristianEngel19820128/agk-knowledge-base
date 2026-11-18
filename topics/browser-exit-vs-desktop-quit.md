# Browser exit versus desktop quit in an AppGameKit Studio game

**Work status:** Review  
**Evidence status:** Code-reviewed SonecBot implementation; developer-reported HTML5/browser observation  
**Scope:** The game's own QUIT flag and main-loop termination logic. **Not** proof of an AGK defect or a universal browser keyboard rule.  
**Pinned source revision:** [SonecBot `2ddb1c93963dc38bbe3c17260e9bb7a6f8b40d8c`](https://github.com/ChristianEngel19820128/SonecBot/tree/2ddb1c93963dc38bbe3c17260e9bb7a6f8b40d8c)  
**Environment:** Developer-run Windows and local HTML5/Apache pre-alpha tests; exact AGK Studio and browser versions not recorded for this observation.

## The situation

The same game can run in a desktop application window or inside a browser. A visible **QUIT** menu entry may have a different meaning in each environment: closing a native application is not the same as leaving a page or an embedded game.

SonecBot demonstrates this distinction in its **custom game loop**. The developer reported that ESC/QUIT did not terminate the HTML5 browser build. The implementation explains **part of** that behavior, but the browser-specific outcome of ESC still requires careful wording.

## Source evidence: what actually ends the game loop?

The final line of the SonecBot loop is:

~~~agk
until GetESCPressed() = 1 or (Device$ <> "html5" and Intro.IsQuit = TRUE)
~~~

**Source:** [`main.agc`, lines 275–279](https://github.com/ChristianEngel19820128/SonecBot/blob/2ddb1c93963dc38bbe3c17260e9bb7a6f8b40d8c/source/sonec/main.agc#L275-L279).

`Device$` is defined from `GetDeviceBaseName()` in [`main.agc`, lines 68–70](https://github.com/ChristianEngel19820128/SonecBot/blob/2ddb1c93963dc38bbe3c17260e9bb7a6f8b40d8c/source/sonec/main.agc#L68-L70). Selecting the menu's QUIT entry sets `Intro.IsQuit = TRUE` in [`intro.agc`, lines 790–794](https://github.com/ChristianEngel19820128/SonecBot/blob/2ddb1c93963dc38bbe3c17260e9bb7a6f8b40d8c/source/sonec/intro.agc#L790-L794).

The condition has **two separate paths**:

| Path | Desktop/non-HTML5 | HTML5 |
| --- | --- | --- |
| `Intro.IsQuit = TRUE` after selecting QUIT | Can end the loop | Explicitly **cannot** end the loop, because `Device$ <> "html5"` is false |
| `GetESCPressed() = 1` | Can end the loop | **Still evaluated**; there is no HTML5 guard on this term |

This second row matters. The code **does not disable the ESC test for HTML5**. It is therefore incorrect to explain the developer's observed failure to exit with ESC solely by pointing to the `Device$` condition. Whether `GetESCPressed()` becomes true in a specific browser is a separate input/browser question that this code review does not resolve.

## Observation versus explanation

**Developer observation:** In the tested SonecBot HTML5 build, ESC and the game's QUIT command did not leave the browser game as a native Windows application would.

**Code-reviewed explanation for menu QUIT:** The application's `Intro.IsQuit` flag does not terminate the main loop when `Device$ = "html5"`.

**Unresolved explanation for ESC:** The loop still checks `GetESCPressed()` on HTML5. The test history does not isolate whether the browser consumed the key, the AGK input function returned zero, focus affected input, or some other browser/session behavior was involved.

**Historical references:** [SonecBot technical notes](https://github.com/ChristianEngel19820128/SonecBot/blob/develop/TECHNICAL.md) and [handoff / browser test history](https://github.com/ChristianEngel19820128/SonecBot/blob/develop/HANDOFF.md). These are moving documents. The code is pinned above; exact exported build SHA and complete browser matrix were not recorded for the observation.

## Reproduce and distinguish the paths

A developer who wants to test this in another project can:

1. Record the AGK Studio version, actual source/export revision, browser and OS, and whether the game is embedded or opened as a page.
2. In a desktop build, trigger the application's QUIT menu and record whether it ends the main loop.
3. In the HTML5 build, trigger **that same menu action** and observe whether the game continues.
4. Independently test ESC with the game canvas focused. Record the value returned by `GetESCPressed()`, not only whether the page remains open.
5. Repeat the input test with and without the game canvas focused, and—if relevant—in a hosted iframe. Do not assume different environments behave alike.
6. If a browser-specific exit interaction is desired, design and test it explicitly as a **separate user-experience choice**, rather than assuming that ending a program loop will close a browser tab.

These are **proposed steps**, not an assertion that every test has already been executed.

## When to use this / when not to use this

**Useful when:**

- A Windows game is exported to HTML5 and its QUIT menu seems unresponsive.
- A developer needs to separate a **game-specific termination condition** from AGK keyboard input and browser behavior.
- An embedded game must communicate how the player should leave or return to the hosting page.

**Do not assume:**

- This SonecBot condition is an AGK-recommended cross-platform exit template.
- ESC never works in HTML5 or every browser handles it identically.
- A webpage or iframe can be closed like a normal desktop application window.
- A browser exit issue automatically indicates a faulty AGK engine function.

## Known limitations and follow-up

- The browser's observed ESC behavior was not isolated by a dedicated return-value test.
- Exact browser, AGK Studio version, focused element and export SHA were not captured in the historical report.
- The pinned source does **not** establish a portable alternative UX for leaving an HTML5 game.
- An explicit browser-friendly back/exit design may be useful in a future project, but **no gameplay or menu change is proposed or authorized by this article**.

## References

- [Pinned SonecBot main loop](https://github.com/ChristianEngel19820128/SonecBot/blob/2ddb1c93963dc38bbe3c17260e9bb7a6f8b40d8c/source/sonec/main.agc#L275-L279)
- [Pinned SonecBot QUIT menu handler](https://github.com/ChristianEngel19820128/SonecBot/blob/2ddb1c93963dc38bbe3c17260e9bb7a6f8b40d8c/source/sonec/intro.agc#L790-L794)
- [SonecBot historical HTML5 notes](https://github.com/ChristianEngel19820128/SonecBot/blob/develop/TECHNICAL.md)
- [Testing methodology](testing-methodology.md)
- [Knowledge Base TODO](../TODO.md)
