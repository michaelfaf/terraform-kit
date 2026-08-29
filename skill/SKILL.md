---
name: terraform
description: Use when the user wants to share, package, hand off, or export something they built — a skill, project, system, procedure, or workflow — so someone else can implement it on THEIR AI stack (Claude, Claude Code, Codex, Cursor, Copilot, a chat-only assistant, or no AI at all). Triggers on "terraform X", "package X for <person>", "make an implementation kit for X", "share this build with <team>", "make X portable", "turn X into a repo someone can use", "how would <person> implement this", "onboard the team onto X", or any request to de-personalize an internal build so outsiders can run their own version of it. Produces a sanitized, stack-adaptive kit the recipient's AI reads and walks them through. (Unrelated to HashiCorp Terraform — infrastructure-as-code questions do not trigger this skill.)
---

# Implementation kits

Take any build you own and generate an **implementation kit**: a self-contained repo (or folder) that the recipient's AI reads and executes — scanning their stack, negotiating decision points with them, installing an adapted version, and tracking progress in a checkable STATUS file.

A kit is not documentation and not a copy of your files. It is an **installer written to another person's AI**, and it degrades on purpose: full coding agent → mid-tier agent (files + shell, no subagents) → chat-only AI → a human with no AI at all.

Terms used throughout: **the owner** is the person whose build is being packaged (usually the user you're working for). **The recipient** is whoever receives the kit. `<kits home>` is the folder where finished kits live — chosen at install, recorded in the kit registry. **Identity terms** are the words that would leak the owner's private context: their name, teammates, company, customers, products, internal tool names, private paths.

---

## 1 — Intake

Establish, from the request or by asking (one round of questions, maximum):

- **Source** — which build, and where its files live.
- **Audience** — a named person or team (tailor recommendations to what the owner knows of their stack), or general.
- **Scope** — the whole build, or one capability out of it.

If the source is bigger than one capability, say so and propose a split before writing anything. Two focused kits beat one that tries to install a department.

---

## 2 — Read and extract

Read the **entire** source: entry doc, status/readme, every reference file, every script. On a large source, dispatch a cheap-model subagent to inventory the files and return one line per file, then read the load-bearing files yourself. No subagents? Read the entry doc and the referenced files in sequence — inventory first, skim never.

Then split what you found, explicitly, in working notes — write them to `<kits home>/_working/<kit-name>-extraction.md`, *outside* the kit folder, so they never ship inside the kit:

- **Mechanism** — what makes it work anywhere: the jobs each file does, the protocol, the gates and tests, the pipeline logic, the rules that exist because something went wrong. **This becomes the kit.**
- **Wiring** — what only works in the owner's world: their paths, their folder trees, their wikilinks, people's names, company/customer names, their accounts, their specific integrations. **This gets genericized, or becomes a decision point.**

**This split is the core judgment of the whole procedure. Do not rush it.**

The most common failure is cutting too much: strip the *subject matter* as wiring and you ship a correct, incomprehensible abstraction. If the source's subject had to go, put a sanitized version of it back as a worked example (see step 4).

---

## 3 — Mine the decision points

A decision point (DP) is anywhere the recipient must choose. Scan the extraction for exactly two categories — nothing else generates a real DP:

1. **Platform capabilities the source leans on** — file access, shell, subagents, persistent memory, specific integrations or APIs. Each becomes a DP **with a degradation path**. Never a hard requirement unless the thing is genuinely impossible without it.
2. **Choices the owner made that others may make differently** — structure, depth, cost-versus-simplicity trade-offs, tool selection. Each becomes a DP with the trade-offs stated and **the owner's choice as the recommendation, with the reasoning that produced it**.

**3–7 DPs is the sweet spot.** More than seven and the kit should probably be split. Fewer than three and you almost certainly hardcoded a choice that isn't yours to make.

---

## 4 — Draft the kit

Read `references/kit-skeleton.md` and follow it exactly — the file set, the IMPLEMENT.md phase structure, the DP block format, the STATUS tables, the degradation tiers, and the authoring rules that came out of past cold tests. Write into `<kits home>/<kit-name>/`.

Naming: `<thing>-kit`, kebab-case, meaningful to a stranger who has never met the owner.

---

## 5 — Sanitize (mandatory, never skipped)

Run `references/sanitize-checklist.md` in full. Two parts: the pass (strip and genericize), then the proof (a search gate over the finished kit that must come back with zero hits), then the report. **No kit is delivered without the owner seeing the sanitize report.**

---

## 6 — The owner's gate

Present, in one message: what the kit installs, the sanitize report, what you deliberately left out and why, and the proposed name plus visibility (private/internal or public). Wait for the ruling. If the owner already ruled "go" for this kit in this session, don't re-ask — but still deliver the report.

Word the ask so the answer is unambiguous about visibility: *"reply 'publish `<name>` as a private repo'"* — an approval that doesn't name the visibility isn't an approval to publish publicly.

---

## 7 — Publish

Per the distribution decision recorded at install:

1. `git init` in the kit folder; commit.
2. If the kit folder sits inside another repo, add its path to the **outer** repo's ignore file — the published kit owns its own history; the outer repo ignores it.
3. Create the remote and push (e.g. `gh repo create <owner>/<name> --private --source=. --push`), with a one-line description written for a stranger. No `gh`? Create the repo in the web UI, then `git remote add origin <url> && git push -u origin main`. No git at all? Zip the folder or drop it on the shared drive — the kit works the same; only the update path changes.

Mechanical work — safe to delegate to a cheap subagent given the folder and the name.

---

## 8 — Cold test (mandatory before calling it shipped)

Dispatch a **fresh agent with zero context** on the owner's world:

- It fetches the kit from where recipients will get it (the pushed repo or the distributed zip), **not** from the authoring folder — half the defects are missing files that only exist locally.
- Give it a role-play profile: a **mid-tier platform** ("files and shell, no subagents, no persistent memory") and a terse, semi-technical user. Mid-tier exercises the most branches.
- It executes IMPLEMENT.md **end to end for real** — actually creating the files, actually running the checks, maintaining the kit's STATUS.md as it goes.
- It reports: execution trace, friction log (every guess it had to make), broken references, DP clarity, **a verdict out of 10 and the top fixes**.

Apply the fixes and push. **A verdict under 8 means fix and re-run the cold test.**

No subagents? Open a brand-new session with no project context, paste the cold-test brief, and let it run. No second session available? The degraded form is a colleague following the kit while you stay silent and write down every question they ask — that list *is* the friction log. Self-review is not a cold test and doesn't clear the bar; if that's all you can do, say so in the registry entry.

---

## 9 — Retro (the self-improvement loop, never skipped)

1. Append what this run taught you to `references/lessons.md` — dated, two to four lines per lesson.
2. If a lesson should change how kits get built, edit `references/kit-skeleton.md` (or this file) **now**, not "later," and tell the owner what changed.
3. Register the kit in `<kits home>/README.md` — name, source, audience, distribution, cold-test verdict, date.

---

## Hard rules

- **The sanitize gate and the cold test are never skipped**, whatever the time pressure. An unsanitized kit leaks the owner's private context; an untested kit embarrasses them in front of the exact people they're sharing with.
- **Every decision point carries options, trade-offs, a recommendation, and "record your choice in STATUS.md."** Never options without a lean.
- **IMPLEMENT.md is written to the consuming AI, in the second person.** The human's path lives in README.md only.
- **Every platform dependency gets a degradation path.** A kit that dead-ends on a missing capability is a failed kit.
- **The kit must wire itself into the recipient's standing instructions file.** A kit that only creates folders dies at their first new chat.
- **A kit inside another repo is ignored by that repo** — the kit owns its own history.
- **Recommend in every kit's README:** run the install on the most capable model available, at high reasoning effort.
