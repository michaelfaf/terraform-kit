# Lessons

Append-only. Every kit you build ends with a dated entry here — two to four lines per lesson. A lesson that should change how kits get built means you edit `kit-skeleton.md` or `SKILL.md` **immediately**, and note here that you did.

The entries below are seeded from the first three kits built with this procedure, before it was packaged. They are here so you inherit the scar tissue instead of earning it. Add yours underneath.

## Seed — what the first three kits taught

- **Extraction is the real work.** Mechanism (file jobs, protocol, tests) versus wiring (names, trees, links) — every other step falls out of that split. Rushing it produces either a leaky kit or an abstract one.
- **Decision points come from exactly two places:** platform capabilities the source leans on, and choices the owner made that others could make differently. Scanning for those two categories found every DP in every kit; scanning for "things that might vary" found noise.
- **Instructions-file wiring is the stickiness step.** The first kit created folders and nothing else. It would have died at the recipient's first new chat. Now it's a hard rule.
- **Kit STATUS needs tables, not just checkboxes.** Scan results and recorded decisions are what make a cold resume work; a bare checklist tells the next session nothing about *why*.
- **Cutting the subject matter as "wiring" broke a kit.** The orchestration protocol without its worked pipeline was correct and incomprehensible. Fix: a sanitized worked example plus a translation table, and build-specs (never transplanted scripts) for anything the recipient's AI has to generate.
- **READMEs were all how-to and no why.** A stranger deciding whether to care had nothing to read. Every README now opens with a step-back section, with the depth in OVERVIEW.md.
- **The cold test earns its cost every single time.** Three kits, six cold tests, verdicts from 7 to 8.5 out of 10 — and every run found real defects that authoring could not: a hidden decision, an unresolved placeholder, a DP that deleted a file the payload required, a contradictory default, a shell tip that could not work. None were visible from the inside.
- **Three cold tests on one kit is the right price when the recipients are people you work with.** Each run found a distinct, real defect.
- **A publish approval that doesn't name the visibility isn't an approval to publish publicly.** Word the gate so the answer contains the words.
- **Numbers in worked examples must be copied from the artifact.** One kit nearly shipped an estimated word count that was wrong by about 9% — in a kit whose central rule was anti-fabrication.

## <YYYY-MM-DD> — Kit: <name>

- <what this run taught>
