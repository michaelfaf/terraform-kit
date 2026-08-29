# terraform — implementation kit

> **Not that Terraform.** This has nothing to do with HashiCorp Terraform or infrastructure-as-code. The name is literal: it *terraforms someone else's environment* so a system you built can live there. If that collision would confuse your team, the installer's first decision offers you a rename (`implementation-kit`) and tells you exactly what to change.

A generator: it turns any system you've built into an **implementation kit** — a small repo that the *recipient's* AI reads and executes, scanning their stack, walking them through a handful of decisions, installing an adapted version, and tracking progress in a file that survives a dead chat. This repo is the generator itself, packaged as one of its own kits. Extracted from a working procedure that has produced three shipped kits and survived six cold tests.

## Read this first — what this is and why

**The problem.** You built something that works — a way of running projects, a research routine, a report your team can't live without — and now three people want it. So you explain it. Then you explain it again, slightly differently, to someone whose setup is nothing like yours. Handing over your actual files doesn't work: they're full of your paths, your names, your tools, your assumptions. Writing documentation doesn't work either, because documentation describes the system and nobody has time to *install* it from a description. The knowledge stays trapped in the one person who built it.

**What this installs.** A procedure your AI runs on any build you own: it reads the whole thing, separates what makes it work *anywhere* from what only works *for you*, turns your personal choices into decisions the recipient gets to make (with your reasoning attached as the recommendation), and writes a kit whose instructions are addressed to *their* AI. Then it strips every name, path, and business detail and proves it with a search gate, and it hands the finished kit to a fresh agent that installs it for real and scores it out of 10. Under 8, you fix it and re-run.

**Use it if:** you're the bottleneck on something you built · you want to give your team AI skills that work on *their* setup, not just yours · you've handed someone a folder of your files and watched it not take · you're sharing a system with a partner or client and can't send them your internal paths · you want the thing you built to outlive your explanation of it.

**Concrete use cases:** give every employee your project-management convention, installed into whatever tool each of them actually uses · hand a partner agency your reporting routine without exposing customer data · publish a system you're proud of as a repo strangers can actually run · onboard a contractor onto your workflow in an hour with no meeting.

**It is not** documentation, a template repo, or a package manager. Templates give people files and hope; this gives their AI a script, a set of choices, and a progress file — and it's tested by someone who has never seen your work.

**→ Want the full picture before deciding?** Read [OVERVIEW.md](OVERVIEW.md) — every concept, how a packaging run flows, what gets installed, your role versus your AI's — then [EXAMPLE-KIT.md](EXAMPLE-KIT.md), one real build packaged start to finish. Ten minutes for both.

## How to use it — three on-ramps

1. **You have a coding agent** (Claude Code, Cursor, Codex, Copilot Workspace, Amp…): open this repo with it and say **"Read IMPLEMENT.md and walk me through it."** It scans your stack, presents six decisions, installs the procedure, and then packages one of your real builds with you, cold test included.
2. **You have a chat-only AI:** paste `IMPLEMENT.md` into the chat, follow along, create the files yourself, and keep `STATUS.md` as a note you paste back each session. The sanitize search becomes find-in-files in your editor; the cold test becomes a second chat window with no context. The procedure is unchanged.
3. **No AI at all:** read `IMPLEMENT.md` yourself — every step is doable by hand, and a kit is just markdown files.

**What you'll need:** nothing mandatory. `git` and the GitHub CLI (`gh`) make distribution and updates much easier and the installer will ask; without them, a kit is a zipped folder that works exactly the same. A shell makes the sanitize gate a one-liner; without one, it's find-in-files. A platform that can spawn a context-free subagent makes the cold test push-button; without one, a fresh chat window does the job.

**Model recommendation:** run the install — and the extraction step of every kit you build afterwards — on the most capable model you have, at high reasoning effort. Separating mechanism from wiring is the judgment the whole procedure rests on, and weaker models cut too much: they strip the subject matter along with the private details and leave a correct, unusable abstraction. The mechanical parts (the search gate, the publish commands) are fine anywhere.

## What's in here

| Path | What it is |
|---|---|
| `OVERVIEW.md` | The full explanation — read this first to understand the system before installing |
| `EXAMPLE-KIT.md` | The worked example: one real build packaged start to finish — extraction, decisions, sanitize report, cold-test findings — plus a translation table for other kinds of builds |
| `IMPLEMENT.md` | The installer script, written to your AI (humans can follow it too) |
| `STATUS.md` | Install progress — scan results, decisions, phase ticks; the resume spine |
| `AGENTS.md` / `CLAUDE.md` | Entry instructions for coding agents that auto-read those files |
| `skill/SKILL.md` | The procedure itself, in portable agent-skill format |
| `skill/references/kit-skeleton.md` | The kit file set, the installer's phase structure, the decision-block format, the degradation tiers, and every authoring rule a cold test has taught so far |
| `skill/references/sanitize-checklist.md` | Building the term list, the strip-and-genericize pass, the search gate, the report |
| `skill/references/lessons.md` | Seeded with what the first three kits taught; you append to it |
| `templates/KIT-REGISTRY.md` | The kits index the installer creates for you, with an install record of your choices |
| `templates/kit-scaffold/` | Stub files the generator stamps out at the start of each kit — see `templates/README.md` for what maps to which decision |

## What you end up with

- The **kit generator** installed where your AI finds it (or kept as documents you paste, on chat-only).
- A **kits home** folder with a registry index and an install record of your choices.
- A **sanitize term list** — the words that must never survive into a kit — written down once instead of remembered each time.
- A line in your AI's **standing instructions** so "package this for Dana" just works, without you naming a procedure.
- **One real kit**, built during install, sanitized, published, and cold-tested to at least 8/10.
