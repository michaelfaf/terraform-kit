# Kit skeleton

Every kit ships these files. Deviate only with a reason worth writing into `lessons.md`.

## File set

| File | Job | Written for |
|---|---|---|
| `AGENTS.md` | Agent entry point — auto-read by Codex, Cursor, Copilot, Amp and friends when the repo opens. States: this is an installer, not a codebase; IMPLEMENT.md is your script; STATUS.md is your memory; decisions belong to the user; adapt, don't transplant; confirm before starting. | The AI |
| `CLAUDE.md` | One line: "Read AGENTS.md — it has your instructions for this repo." | Claude Code |
| `IMPLEMENT.md` | The walkthrough (structure below). | The AI (second person) |
| `STATUS.md` | Scan-results table + Decisions table + per-phase checkboxes + Blockers. The header tells the AI to update it as it goes, not at the end — this file is the resume spine. | The AI |
| `OVERVIEW.md` | *(Required at full and standard explainer depth; at lean depth the README's step-back section carries it alone — see the explainer-depth decision in the installer. If you skip it, delete the README's pointer line to it.)* Everything a stranger needs to *understand* the system before installing it: the problem, the core idea, every concept they'll meet (each defined in one to three sentences), how a run actually flows, what gets installed, the human's role versus the AI's, platform needs, what it is not, honest provenance. Roughly 1,200–1,500 words. IMPLEMENT is the *how*; OVERVIEW is the *what and why*. | The human |
| `README.md` | Opens with a **step-back section**: what this is in plain words, the problem it solves, and three or four concrete use cases — written for someone deciding *whether to care*, before any how-to. Keep it near 250 words and point to OVERVIEW.md for depth. Then **three on-ramps**: coding agent ("open this repo with your assistant and say: read IMPLEMENT.md and walk me through it"), chat-only (paste IMPLEMENT.md, keep STATUS yourself), no-AI human (read IMPLEMENT.md by hand). Then the model recommendation (most capable available, high effort), a what's-in-here table, and a what-you-end-up-with list. | The human |
| Worked example | For any kit whose subject matter got stripped as wiring: a concrete narrative of one real run, sanitized, plus a translation table showing how other subjects map onto it. Named for the domain (`EXAMPLE-<THING>.md`). Abstract protocols are unusable without one. | The human |
| Build specs | For any kit where the recipient's AI must *generate code*: specs with acceptance checks, never transplanted scripts. Their stack is not yours. | Both |
| Payload | `templates/` (files the AI installs, with an install-matrix `README.md` mapping each template to the DP that gates it) and/or a portable, spec-compliant `SKILL.md` — the agent-skill format that loads in roughly forty tools. | Both |

## IMPLEMENT.md structure

Open with a blockquote addressed to the AI: this file is your script, STATUS.md is your memory, a human can follow it by hand, present decisions conversationally, and here is the honest time estimate. Then:

1. **"What you are installing"** — the mechanism in one table or tight paragraph. The recipient's AI has to *understand* the thing, not just execute steps.
2. **Phase 0 — Environment scan.** Always first, before any questions. What am I (platform, and: files? shell? subagents? persistent memory? relevant integrations)? Where does the user's work live? What conventions and instructions files already exist (read them — merge, never bulldoze)? Any kit-specific prerequisites? Findings go straight into STATUS's scan table. Include the chat-only fallback.
3. **Phase 1 — Decisions.** DP blocks (format below), presented one at a time, each recorded in STATUS before moving on.
4. **Phase 2 — Install.** Create and adapt the artifacts — adapt *minimally*, renaming only terms that clash with the user's vocabulary. **Always includes wiring into the user's standing instructions file**, with the scope warning: some platforms scope instruction files to a workspace or folder, so place the wiring where the user's AI actually works and tell them where it landed.
5. **Phase 3 — First live run on something real** of the user's, end to end, finishing with a verification test that proves the install (for example: a cold read where a fresh session reconstructs the state from the installed files alone).
6. **Phase 4 — Wrap.** Walk through what's installed and where; tick STATUS fully; leave one line of habit behind; note that the kit repo can now be deleted (after the install record is preserved somewhere permanent).
7. **"If things go wrong"** — the standing fallbacks: missing capability → the nearest degradation path; conventions conflict → keep their names, map the jobs; a step fails twice → don't loop, note it in Blockers, take the fallback, tell the user.

## Decision-point block format

```
### DP-N — <question in plain words>

**Options:**
- **A. <name>** — <what it is>. *Trade-off:* <cost>.
- **B. <name>** — <what it is>. *Trade-off:* <cost>.

**Recommendation:** <the lean, conditioned on the scan where relevant
("A if your platform supports X; otherwise B"), with the reasoning — if it
is the choice the owner made, say why they made it.> Default if the user
says "whatever you recommend": <the same conditional, spelled out>.
```

## Authoring rules learned the hard way

Each of these came from a cold test finding a defect that authoring could not see.

- **Every capability the kit uses must be choosable somewhere.** No hidden decisions. (Found: git was used in Phase 2 but never asked about.)
- **Define fuzzy terms inside the recommendation** — "a workspace means an organized folder or vault, not loose files."
- **If an option says "check it against a checklist," ship the checklist inline.** Weaker agents cannot reconstruct one.
- **Never leave a `<placeholder>` the installer must resolve without a stated default.** (Found: a kit's core safety marker lived in an unnamed "status file" — the safety mechanism rested on a guess.)
- **If a DP option removes or skips a file, search the payload for unconditional references to that file first.** (Found: choosing option B deleted a rules file that every worker prompt still required.)
- **Sweep the *default* templates too, not just the DP-gated ones.** (Found: a "prune the middle layer" option that could never fire, because the default template linked the layer unconditionally.)
- **Any "non-negotiable" rule in the payload needs a stated degraded form** for tiers that cannot honor it. (Found: a model-escalation rule that was impossible on single-model installs.)
- **Design every recommendation for the "whatever you recommend" user** — the default path must still build and test the safety-critical components. (Found: the recommended tier skipped building the safety gate entirely.)
- **The stated default must be conditioned on the scan exactly like the recommendation is.** (Found: the lean said "B if it's a team," the default said "A" — a team user got two answers.)
- **Acceptance checks must not pass vacuously.** Forbid empty-set passes. Where a behavior genuinely cannot be tested at install time, say so and park it as a to-verify item rather than implying coverage.
- **Define jargon at first use and give load-bearing concepts a three-line micro-example.** A semi-technical reader nods past a parenthetical.
- **Numbers in worked examples are copied from the artifact, never estimated.** Count it, list it, paste it.
- **Every shell recipe in a kit is executed once against a real file before publish.** (Found: a search-gate tip that could not work, because line-oriented tools cannot match across a line break — and a literal agent would have drawn the wrong conclusion from it.) Reasoning about a one-liner is not testing it.
- **A kit whose output is a library ships the library index template with an install-record block.** The procedure needs a registered home or the output scatters, and the install record is where later sessions learn the user's policy choices.
- **Name platform-specific mechanisms explicitly.** Which ignore file, which rules directory, which frontmatter flag. A mid-tier agent should not have to already know.

## Degradation tiers

Design every kit to work at all four, and name the tier switches inside the DPs:

1. **Full agent** (files, shell, subagents, persistent memory) — everything on.
2. **Mid agent** (files and shell, no subagents, no memory) — subagent gates become self-review with a stated checklist; memory handoff becomes an on-disk handoff file.
3. **Chat-only AI** — the user pastes IMPLEMENT.md; the AI narrates and the user creates the files; STATUS lives in a note the user pastes back each session.
4. **No AI** — the README's human path; every IMPLEMENT step is doable by hand.

**The cold test always role-plays tier 2** — it exercises the most branches.
