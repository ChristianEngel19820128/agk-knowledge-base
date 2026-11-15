# Contributing to AGK Knowledge Base

Thanks for helping make practical AppGameKit Studio knowledge easier to verify and reuse.

## What makes a useful contribution?

Prefer a small, testable finding over a broad claim. Show the actual behavior, where it came from, and how another developer could check it.

A good entry records:

1. **Question / symptom:** What was being investigated?
2. **Scope:** An AGK command, a custom wrapper, a particular game system, or an interaction among them.
3. **Environment:** AGK Studio version (if known), platform, renderer, GPU and display refresh rate when relevant.
4. **Reproduction:** Steps, minimal example or A/B comparison, plus important settings.
5. **Expected and observed results:** Keep observations separate from explanations.
6. **Evidence status:** Locally tested, code-reviewed, hypothesis, or design decision/proposal.
7. **Source references:** Repository, branch, commit SHA and relevant file/lines; link to screenshots or test logs when possible.
8. **Limitations:** Other renderers, refresh rates, browsers, devices or game states not covered.

Do not guess missing values. Write **not recorded** or **not tested**.

## Evidence quality

- **Code-reviewed** means the implementation was read; it does not claim a runtime fix.
- **Locally tested** means a developer actually ran it. State whether the result was visual, measured or instrumented.
- **Hypothesis** means an explanation is plausible but still unverified.
- **Design decision / proposal** is a gameplay or architectural choice, not an engine finding.

Avoid declaring an AGK engine defect when the behavior originates from custom game code. If a claim concerns an AGK built-in function, test it separately in a minimal project where practical.

## Recommended Markdown entry skeleton

~~~markdown
# Finding title

**Status:** Code-reviewed / Locally tested / Hypothesis / Design proposal
**Scope:** AGK API / custom game code / mixed
**Source revision:** link to a pinned commit
**Environment:** platform, AGK version, renderer, refresh rate (or "not recorded")

## Observation

## Reproduction steps

## Source evidence

## Explanation and alternatives

## Known limitations

## Follow-up tests
~~~

## Content and licensing

The repository uses the [Unlicense](LICENSE). Submit only text, screenshots and code you have the right to share on those terms. Attribute references and prefer small original illustrative snippets rather than copying unrelated third-party source files.

For existing topics, improve the evidence or add a clearly dated comparison rather than overwriting an older observation as though it never happened.

## Maintenance

Update links when a source revision changes. Keep historical results pinned to their tested or reviewed commit; a moving branch link can supplement a pin but must not replace it.
