# EXAMPLE-KIT — one real build, packaged start to finish

A real packaging run, with the identifying details removed. The source was a **project folder convention** — a small, opinionated way of running any project so that an AI session can pick it up cold. The recipient was a partner team lead ("Dana") who ran a different toolchain entirely. Read this once before the decision points in `IMPLEMENT.md`; every question there gets easier with a concrete picture.

## 1 — Intake

- **Source:** a project convention that had been in daily use for months — a fixed file set per project folder, plus a session protocol.
- **Audience:** one named person on a partner team, on a mid-tier coding agent. Not the public.
- **Scope:** the whole convention. Small enough to stay one kit.

Three questions, one round. The scope answer mattered most: the source folder also contained a dozen live projects, none of which belonged in a kit.

## 2 — Read and extract

Everything was read first — the convention doc, the templates, the protocol, and two example project folders to see how it behaved in practice. Then the split, written down as two explicit lists:

**Mechanism (becomes the kit):**

| Piece | Why it's portable |
|---|---|
| Four files per project, each with one job | Identity in one file, current state in another, history in a third and fourth. Works in any folder, any tool. |
| "Would an agent loading current state need this paragraph?" | The single test that keeps the state file from rotting into a diary. |
| The session protocol: enter by reading two files, exit by updating state and writing a handoff | The thing that makes a project survive a dead chat. |
| One fact lives in exactly one file; everything else points at it | The rule that prevents three files disagreeing about the same decision. |

**Wiring (gets genericized or becomes a decision):**

| Piece | What happened to it |
|---|---|
| The absolute path to the projects folder | → became a decision: *where do projects live?* |
| Wiki-style `[[links]]` from a specific notes app | → plain markdown links, with the wiki style offered as an option in the same decision |
| Folder names for internal business functions | → cut entirely; they were a taxonomy nobody else shares |
| Two teammates' names in the worked example | → invented neutral roles |
| A specific AI platform's skills directory | → became part of the environment scan, with three install paths |

The temptation was to cut the *examples* too, as business-specific. That would have been the classic over-cut: what survived was one small, invented-but-realistic project walked through the whole protocol, which is the only part a stranger can anchor on.

## 3 — The decisions that fell out

Five, from the two legitimate sources:

| # | Question | Came from |
|---|---|---|
| DP-1 | Where do projects live? | Wiring — a hardcoded path |
| DP-2 | How deep is a project by default (four files, or two)? | A personal choice: ceremony versus completeness |
| DP-3 | Which quality gates run at session exit? | A personal choice: three gates, and not everyone wants three |
| DP-4 | How does one session hand off to the next? | A platform capability: persistent memory, which most tools don't have |
| DP-5 | How much history does a project keep? | A personal choice: storage and clutter versus recoverable context |

DP-4 in full, as it shipped:

```
### DP-4 — How does one session hand off to the next?

**Options:**
- **A. A handoff file** — each session ends by writing the next session's
  opening brief into the project folder. *Trade-off:* one more file to keep
  current; it goes stale the moment someone skips the exit step.
- **B. Platform memory** — the assistant remembers across sessions on its own.
  *Trade-off:* only some platforms have it, it's invisible (you can't audit
  what it kept), and it doesn't transfer to a teammate.

**Recommendation:** A, even if your platform has memory — the author's
reasoning was that a handoff you can read is a handoff you can correct, and a
project that depends on one assistant's memory can't be picked up by anyone
else. Use B on top of A if you have it, never instead. Default if you say
"whatever you recommend": A.
```

Note what DP-4 does: it takes a capability the source leaned on and turns it into a choice with a fallback, so the kit installs on a platform that has no memory at all.

## 4 — The kit that got written

```
project-convention-kit/
├── AGENTS.md          # agent entry: this is an installer, not a codebase
├── CLAUDE.md          # one line, pointing at AGENTS.md
├── IMPLEMENT.md       # the installer: scan → 5 decisions → install → first real project
├── STATUS.md          # scan table, decisions table, phase checkboxes, blockers
├── README.md          # the human's why, three on-ramps, what's in here
├── OVERVIEW.md        # the concepts, for someone deciding whether to care
└── templates/         # the four project files, plus an install matrix README
```

4,027 words across the root markdown files — counted, not estimated. That's the small end; a kit carrying a full procedure as payload runs closer to 8,400. Neither number is a target; they're what the material actually needed.

## 5 — Sanitize

The term list came first: the author's name and handle, the company, two teammates, three customers, the product name, the home-directory username, and the notes-app vault name. One teammate's name was an ordinary English word, so it went into the separate case-sensitive, word-boundary pattern rather than being skipped.

The report, as delivered:

**Removed or genericized** — the projects path (→ a decision); wiki-links (→ markdown, with a wiki option); internal function folder names (→ cut); two teammate names (→ neutral roles); one customer example (→ an invented one with the same shape).

**Deliberately left out** — a review cadence that only makes sense with a specific meeting rhythm; a metrics file tied to a system the recipient doesn't run. One line of reasoning each, and both were offered back for veto.

**Survived as recommendations** — four files rather than two (DP-2); three exit gates (DP-3); a written handoff over platform memory (DP-4). All three carrying the reasoning, none of them carrying the author's authority.

The search gate returned nothing on the second run. The first run found the home-directory username inside a code block in the installer — exactly the kind of hit a read-through misses.

## 6 — The cold test

A fresh agent, no context, cloned the published repo — not the authoring folder — and role-played a mid-tier platform (files and shell, no subagents, no memory) with a terse user. It installed the convention for real and created a project.

**Verdict: 8.5/10.** Four findings, all real, none visible from the inside:

1. **A hidden decision.** The installer used `git` in the install phase but never asked whether the user had it or wanted it. Every capability a kit *uses* has to be choosable somewhere.
2. **An undefined term.** "Workspace" appeared throughout and was never defined; the tester guessed. Fixed by defining it inside the recommendation: *an organized folder or vault, not loose files*.
3. **A referenced checklist that wasn't there.** One option said "check it against the exit checklist," and no checklist shipped. A stronger agent would have invented a plausible one, which is worse than failing.
4. **Instruction files are sometimes workspace-scoped.** The wiring step assumed an account-level instructions file. On the tester's platform it was per-folder, so the wiring landed somewhere the user's AI would never read. Fixed with an explicit scope warning.

All four became permanent authoring rules. That's the pattern: a cold-test finding on one kit is usually a rule for every kit.

## 7 — Retro

Four lessons appended to the lessons file, and two of them changed the skeleton immediately: *no hidden decisions*, and *ship the checklist inline if you reference one*. The kit was registered in the kits index with its verdict and date. Total elapsed: two sittings.

## How other kinds of builds differ

| If your source is… | What changes |
|---|---|
| **A procedure or skill** (research routine, writing process, review protocol) | The payload is a portable `SKILL.md` rather than templates. Add a decision for where the procedure installs, since platforms discover skills differently. If the procedure produces an accumulating output — packs, reports, records — ship a library index template with an install record, or the output scatters. |
| **A pipeline with code** (scraper, ingest, automation) | Never transplant your scripts; their stack isn't yours. Ship **build specs with acceptance checks** and let the recipient's AI generate the code. Make sure the recommended path still builds and tests the safety-critical components — a default that skips the guardrail is worse than no default. |
| **An abstract protocol** (an orchestration pattern, a decision framework) | This is the dangerous one. Stripping the subject matter as wiring leaves something correct and unusable. Put a sanitized version of the subject back as a worked example — like this file — with a translation table for other subjects. |
| **A checklist or SOP** | Often the whole kit. Templates plus a short installer; two or three decisions; lean explainer depth is fine because the recipient already knows the domain. Don't inflate it — a small kit that installs cleanly beats a thorough one nobody finishes. |
| **Something that only works because of who runs it** | Not a kit. Say so. Forwarding it with a conversation is the honest answer. |
