# IMPLEMENT — install walkthrough for the kit generator

> **To the AI running this:** this file is your script and `STATUS.md` is your memory — update it after every scan, decision, and phase, not at the end. A human can follow this file by hand; every step is doable without an AI. Present the decisions conversationally, one at a time, and wait for answers. Adapt to your user's stack; never transplant a path or a name that doesn't fit it. **Set the expectation when you confirm the go-ahead:** the install itself is 20–30 minutes; Phase 3 — where you package one of their real builds into a kit and cold-test it — is 60–120 minutes for something small. Plan one long sitting, or two with `STATUS.md` as the bridge.

## What you are installing

A **generator**: a procedure your user runs whenever they want to hand one of their internal builds to someone outside it — a teammate, a partner, a client, a friend — so that person's own AI can install an adapted version on *their* stack.

The thing it produces is an **implementation kit**: a small repo whose job is to be read by *the recipient's* AI, which then scans their environment, walks them through a handful of decisions, installs the adapted system, and tracks progress in a file that survives a dead session.

| Piece | Job |
|---|---|
| **The mechanism/wiring split** | The core judgment. Read the source build and separate what makes it work *anywhere* (file jobs, protocol, gates, logic) from what only works *here* (paths, names, org-specific trees, accounts). Mechanism becomes the kit; wiring gets genericized or becomes a decision the recipient makes. |
| **Decision points** | Every place the recipient must choose. They come from exactly two sources: platform capabilities the build leans on, and choices the author made that others could reasonably make differently. Three to seven per kit. Each carries options, trade-offs, a recommendation with reasoning, and "record it in STATUS.md". |
| **The kit file set** | `AGENTS.md` (agent entry) · `CLAUDE.md` (one-line pointer) · `IMPLEMENT.md` (the installer, written to the AI) · `STATUS.md` (the resume spine) · `README.md` + `OVERVIEW.md` (the human's why and what) · payload (`templates/` and/or a portable `SKILL.md`). |
| **Degradation tiers** | Every kit is designed to work four ways: full agent → mid agent (files + shell only) → chat-only AI → a human with no AI. Missing capabilities take a named fallback; they never dead-end. |
| **The sanitize gate** | Mandatory. Strip names, paths, business data, credentials; prove it with a search gate over the finished kit; deliver a removals report to whoever owns the build. Nothing ships unsanitized. |
| **The cold test** | Mandatory. A fresh agent with zero context fetches the kit from where recipients actually get it and runs the installer for real, role-playing a mid-tier platform. It reports a verdict out of 10. Under 8 means fix and re-run. |
| **The lessons loop** | Every kit build appends what it taught to `lessons.md`; anything that should change how kits are built gets edited into the skeleton immediately, not "later." |

The payload lives in `skill/` (the procedure in portable agent-skill format: `SKILL.md` plus three references) and `templates/` (a kit registry and a scaffold the generator stamps out per kit). `OVERVIEW.md` explains the concepts; `EXAMPLE-KIT.md` walks one real packaging job start to finish.

**A note on the name.** This is called `terraform` because it terraforms someone else's environment to run your build. It has nothing to do with HashiCorp Terraform, the infrastructure-as-code tool. DP-1 covers renaming it if that collision would confuse your user's team.

---

## Phase 0 — Environment scan

Before asking your user anything, establish these and record each in `STATUS.md`'s scan table:

1. **What am I?** Platform and capabilities: can I create files? run shell commands? spawn parallel subagents? persist memory across sessions? Can I run a step on a cheaper model — which decides only whether the mechanical steps (inventorying a large source, running the search gate, the publish commands) can be delegated cheaply, never whether the extraction can be.
2. **Where does the user's work live?** The folder, vault, or repo that holds the builds they'd want to package. Ask if nothing is visible from where you're running.
3. **Existing conventions.** Is there a standing instructions file — `CLAUDE.md`, `AGENTS.md`, `.cursor/rules/*.mdc`, a legacy `.cursorrules`, a custom-instructions block, a "start here" note? **Read it.** Phase 2 merges into it; it never overwrites it.
4. **Tooling checks** — record each present or absent, because they steer DP-2, DP-3 and DP-5:
   - `git --version` — needed to publish a kit as a repo.
   - `gh --version` — the GitHub CLI. Optional; without it, repo creation is a web-UI step plus `git remote add`.
   - A shell with `grep` (or `rg`) — the sanitize search gate. Without it, DP-5 routes to find-in-files.
   - Can you launch a **fresh subagent with no inherited context**? That's the cold test in its strongest form.
   - Does the platform discover skills from a directory (a `skills/` folder it auto-loads), or does the procedure have to be referenced from a rules file, or pasted?
5. **Who owns the builds?** Is your user packaging their own work, or someone else's? If someone else's, that person is the approver in DP-6 and the sanitize report goes to them.
6. **Who receives the kits?** Ask directly — nothing else establishes this and three later decisions depend on it. Record one of: **inside the org** (employees, contractors) · **outside the org** (partners, clients, friends — named individuals) · **the public** (a repo anyone can read). If the answer is "some of each," record the *widest* audience they expect; the defaults should be safe for it.

**Chat-only fallback:** if you can't see a file system, you narrate every step and the user creates the files. `STATUS.md` becomes a note they paste back at the start of each session. The procedure is unchanged — the sanitize gate becomes a find-in-files pass they run in their editor, and the cold test becomes a second chat window with no context.

---

## Phase 1 — Decisions

Before the first one, orient your user with `EXAMPLE-KIT.md` — one real build packaged start to finish, in about three minutes of reading. Every decision below is easier to answer against a concrete picture.

Work through them in order. Record each in `STATUS.md`'s decisions table **before** moving to the next.

### DP-1 — Where does the generator live, and what do you call it?

**Options:**
- **A. Installed as a skill your AI auto-discovers** — copy `skill/` into the platform's skills directory (Claude Code and Claude-family tools: a `skills/` folder; the frontmatter `description` is what makes it fire on its own). *Trade-off:* the path is platform-specific, and it lives on one machine unless it's in a synced folder.
- **B. Kept in the workspace and referenced from the rules file** — copy `skill/` to something like `<workspace>/_tools/terraform/` and point the standing instructions file at its `SKILL.md`. *Trade-off:* your AI needs a nudge ("use the packaging procedure") more often than in A; it travels with the workspace, which is the point.
- **C. Kept as documents the user pastes** — chat-only platforms. *Trade-off:* the user carries the procedure by hand; everything else works the same.

**And the name.** The procedure is called `terraform` here. If anyone in your user's world touches HashiCorp Terraform, that collision will cost them a confused conversation every few weeks.

**Recommendation:** condition on the scan. **Platform auto-discovers skills → A.** Otherwise **B**, with the path inside the workspace where their builds already live — that's also the right answer for a team, because the procedure then arrives with a `git pull` instead of an onboarding message. **C** only if there's no file access. On the name: **rename to `implementation-kit` if the team runs infrastructure-as-code**, otherwise keep `terraform` — the trigger phrases in the description do the real work either way, so the cost of renaming is three edits (the folder name, the frontmatter `name:` field, and any mention in the wiring line from Phase 2). Default if the user says "whatever you recommend": A if the scan found a skills directory, else B at `<workspace>/_tools/`; keep the name `terraform` unless the scan or the user surfaced infrastructure tooling, in which case rename to `implementation-kit`.

### DP-2 — Where do finished kits live, and how do they reach people?

**Options:**
- **A. A private repo per kit** on the user's git host, shared with named people or the org. *Trade-off:* every recipient needs an account and access; a kit shared with someone outside the org needs an explicit invite.
- **B. A public repo per kit.** *Trade-off:* the sanitize gate stops being a courtesy and becomes the only thing between the user and a leak. Also: anyone can read it, which is usually the intent and occasionally a mistake.
- **C. No git — a zip on the shared drive**, or a folder in an existing internal repo. *Trade-off:* no update path. When you fix a kit, every recipient has an old copy and no way to know.

**Whichever you pick, kits need a home and an index.** A `<kits home>` folder in the workspace (the authoring copy) plus `templates/KIT-REGISTRY.md` installed as its `README.md`. Without a registry, the second person to want a kit builds one that already exists.

**Recommendation:** condition on who the recipients are. **Recipients are employees or contractors → A**, private. It gives you the update path (they `git pull` a fix) without publishing internal procedure to the internet, and the sanitize gate still runs in full — private is not a substitute for sanitizing, because kits get forwarded. **Recipients are the public, or the kit is deliberately a calling card → B.** **No git in the picture → C**, and put a version line at the top of the kit's README so a recipient can at least tell whether theirs is current. Default if the user says "whatever you recommend": condition it on the recipients, the same way the recommendation does — **B** if the scan says recipients are the public or the user described the kit as a calling card; **A** if the scan found `git` and recipients are named individuals or an org; **C** if there is no `git`. Either way `<kits home>` goes at `<workspace>/kits/`.

### DP-3 — Who runs the cold test, and what clears the bar?

The cold test is where kits actually get fixed. Every kit built with this procedure so far has come back between 7 and 8.5 out of 10 on its first run, with real defects: a decision the kit used but never asked about, a placeholder with no default, an option that deleted a file the rest of the kit required. None of them were visible from the inside.

**Options:**
- **A. A fresh subagent with zero inherited context** — it fetches the kit the way a recipient would, role-plays a mid-tier platform, and executes the installer for real. *Trade-off:* needs a platform that can spawn a genuinely context-free agent.
- **B. A fresh session** — a brand-new chat or window with no project context, given the cold-test brief. *Trade-off:* the user has to shuttle the brief and the report by hand; otherwise as good as A.
- **C. A colleague dry run** — someone else follows the kit while the author stays silent and writes down every question asked. That list is the friction log. *Trade-off:* costs a person's hour, and they can't role-play a weaker platform convincingly.
- **D. Author self-review against the skeleton's authoring rules.** *Trade-off:* it is not a cold test. It catches formatting, never assumptions.

**Recommendation:** the highest tier the scan supports — **A if you have subagents, otherwise B**; both are cheap and both work. **C is a genuine fallback**, not a lesser one, when a real person is available. **D never clears the bar on its own**: if D is all that happened, say so in the kit's registry row so the next reader knows what was and wasn't proven. **The bar is 8/10, and it's the same at every tier** — under 8 means fix and re-run. One check on the test itself: a cold test that reports zero friction and no findings didn't run properly — confirm it actually created files and ran the commands before you accept the verdict. Default if the user says "whatever you recommend": A if the scan found subagents, else B; bar stays at 8/10.

### DP-4 — How much explainer layer does each kit carry?

**Options:**
- **A. Full** — README step-back section, `OVERVIEW.md`, and a worked example (`EXAMPLE-<THING>.md`). *Trade-off:* the explainer layer is roughly half the writing; a kit that took two hours takes three.
- **B. Standard** — README step-back plus `OVERVIEW.md`, no worked example. *Trade-off:* fine for concrete systems; **an abstract protocol without a worked example is unusable**, and that mistake has been made once already — a correct, incomprehensible kit that had to be rebuilt.
- **C. Lean** — README step-back only. *Trade-off:* the recipient's AI can execute it, but the human never understands what they installed, so they don't extend it and they don't defend it to their team.

**Recommendation:** **A when the recipient is outside the user's org, or when the kit's subject matter had to be stripped as wiring** — those two cases are exactly where a stranger has no context to fall back on. **B for internal kits about something concrete** where the recipient already knows the domain: a teammate installing a reporting convention doesn't need the philosophy. **C only for a short, self-evident kit going to someone who already knows the domain** — a checklist or SOP where the README's step-back section genuinely says everything; never for anything the user wants *adopted* rather than merely executed. **If and only if you take C**, delete the OVERVIEW pointer line and its table row from the kit's README (both are marked in `templates/kit-scaffold/README.md.template`) and don't create the file — a README that links a missing file reads as a broken kit. B keeps `OVERVIEW.md` and drops only the worked example. Default if the user says "whatever you recommend": A if the recipients are outside the org (from the Phase 0 scan) or the kit is an abstract protocol; else B.

### DP-5 — How is the sanitize gate enforced, and who owns the term list?

The gate has three parts and the first one is the part people skip: **the term list** — every word that would leak private context if it survived (people, company, products, customers, internal tools, the home-directory username, absolute paths). The search is only as good as that list.

**Options:**
- **A. Shell search gate plus a judgment read** — two commands from the kit root, both required to return nothing, then one human read of the whole kit for structural leakage the search can't see. *Trade-off:* needs a shell. None.
- **B. Editor find-in-files plus a judgment read** — same list, one term at a time, case-insensitive, across the kit folder. *Trade-off:* slower and easier to do sloppily; identical coverage if done completely.
- **C. A reviewer read with no search.** *Trade-off:* a human read reliably misses a path buried on line 340 of a template. Not sufficient on its own.

**Recommendation:** **A if the scan found a shell, else B — and the judgment read is not optional in either case.** The search catches literal strings; only a person catches "this example is transparently our biggest customer with the name changed." Never C alone. Own the term list as **two** files the gate reads directly — `<kits home>/.sanitize-terms` (case-insensitive, one plain term per line) and `<kits home>/.sanitize-terms.cs` (case-sensitive, one `\bWord\b` pattern per line) — so kit number four doesn't rebuild the list from memory, and so the search command consumes the artifact instead of a retyped string. Add them to the workspace's ignore file if the workspace is a repo and the terms are themselves sensitive — and note two traps that have cost real time: names that collide with ordinary words (Bill, Grace, Frank) go in a **separate case-sensitive, word-boundary** pattern instead of being skipped, and multi-word names are searched by their **most distinctive single token**, because line-oriented search tools cannot match across a line break. Default if the user says "whatever you recommend": A if the scan found a shell, else B; term list stored as a file at `<kits home>/.sanitize-terms`.

### DP-6 — Who approves a kit before it ships?

**Options:**
- **A. The build's owner approves every kit** — the author presents the summary, the sanitize report, and what was deliberately left out; the owner rules on the name and the visibility before anything is pushed. *Trade-off:* one round-trip per kit.
- **B. The author self-approves and files the report** — the report goes into the registry row instead of a person's inbox. *Trade-off:* nobody who knows the business checks whether a "neutral example" is actually neutral.
- **C. No approval step.** *Trade-off:* the first public kit that leaks a customer name is the last one anyone lets you build.

**Recommendation:** first check whether A and B are even different here. **If your user is packaging their own build and is the only person who could approve it, A and B are the same act** — take **B**, and make the report real: written out in full and filed in the registry row, not skipped because the author already knows what's in it. **A applies when there is a second person to ask**: the build's owner is someone else, or the kit leaves the org and somebody else's data could be in it. Then the owner rules on name and visibility before anything is pushed — they're the only one who knows which customer example is actually sensitive, and it costs them two minutes. **Never C.** Word the approval ask so the answer is unambiguous about visibility: *"reply: publish `<name>` as a private repo"* — an approval that doesn't name the visibility isn't an approval to publish publicly. That's a real incident, not a hypothetical. Default if the user says "whatever you recommend": **A** if there is a second person who could approve — the scan says the builds belong to someone else, or the Phase 0 recipient answer is *outside the org* or *the public*; **B with the report filed in the registry row** if the user is the owner and the only approver.

---

## Phase 2 — Install

1. **Install the procedure** where the platform finds it, per DP-1:
   - Auto-discovering platform (A): copy `skill/` into the skills directory as `terraform/` (or `implementation-kit/` if renamed) — `SKILL.md` plus `references/`. If renamed, the folder name and the frontmatter `name:` field must match.
   - Workspace-referenced (B): copy `skill/` to `<workspace>/_tools/terraform/` and reference its `SKILL.md` from the rules file in step 4.
   - Chat-only (C): the user keeps `skill/SKILL.md` and the three references as documents to paste.
   - Adapt minimally. Rename only terms that clash with the user's vocabulary, and record every rename in `STATUS.md`.
2. **Create the kits home** at the DP-2 location — the folder, plus `README.md` copied from `templates/KIT-REGISTRY.md`. **Fill in the install record with the user** (kits home · distribution · cold-test tier · explainer depth · approver · term-list path · date). Never install it blank; that record is how a session six months from now learns the local policy instead of inventing one.
3. **Create the sanitize term lists** — copy `templates/sanitize-terms.example` to `<kits home>/.sanitize-terms` and `templates/sanitize-terms.cs.example` to `<kits home>/.sanitize-terms.cs`, then **replace the example contents with the user's real terms, now, while they're paying attention**: their name and handles, teammates, company and abbreviations, product names, top customers, internal tool names, their home-directory username. Plain words go in the first file; names that collide with ordinary English (`\bBill\b`, `\bGrace\b`) go in the `.cs` file. One pattern per line, no comments, no blank lines — the search reads these files directly. Then run the gate once against the kit repo you're standing in, to prove the command works on their machine before it matters. If the workspace is a git repo and the terms are themselves sensitive, add both files to its ignore file and say that you did.
4. **Wire the standing instructions file.** This is the stickiness step — an install that only creates folders dies at the user's first new chat. Merge into their `CLAUDE.md` / `AGENTS.md` / rules file, in their format:
   > **Packaging a build for someone else** — "share this with <person>", "package X for the team", "make this portable", "how would they implement this" → use the kit-generator procedure at `<path to SKILL.md>`. Kits live in `<kits home>/`; check its `README.md` first — the kit may already exist. Nothing ships without the sanitize gate and a cold test, and nothing publishes without the approval named in the install record.

   **Scope warning:** some platforms scope instruction files to a workspace or folder rather than the whole account — Cursor uses `.cursor/rules/*.mdc` on current versions (legacy: `.cursorrules`); use whichever the workspace already has, and if creating one, give it frontmatter `description: Kit generator routing` and `alwaysApply: true`, because without `alwaysApply` the rule may never fire. Put the wiring where the user's AI actually works and **tell them which file you edited.**
5. Record every install path in `STATUS.md`.

---

## Phase 3 — First live run — package something real

Build **one real kit**, on a real build of theirs. Not a toy — but keep it small; the point is to run every gate once, not to ship their crown jewels tonight.

1. **Pick the source with the user.** The best first candidate is a small, self-contained procedure they've already explained to someone by hand — a checklist, a folder convention, a recurring report, an onboarding routine. If they name something huge, propose the smallest capability inside it instead and say why.
2. **Run the procedure end to end** from `skill/SKILL.md`: intake → read and extract (write the mechanism-versus-wiring split down explicitly, as two lists, in `<kits home>/_working/<kit-name>-extraction.md` — outside the kit folder so the notes don't ship inside it; this is the step people skim and it is the whole job) → mine 3–7 decision points → draft the kit from `templates/kit-scaffold/` following `references/kit-skeleton.md` → sanitize → approval per DP-6 → publish per DP-2.
3. **Cold test it, per DP-3.** Note the ordering and the vocabulary: a kit is **published** at the step above (it exists at a URL) and only **shipped** once it has cleared the cold test — do not send anyone the link before then. The tester fetches the kit from where recipients get it — the pushed repo, the shared zip — **not** from the authoring folder. Half the defects found so far were files that only existed locally. Apply the fixes and push them. Under 8/10 means fix and re-run; that is not a formality, and it has happened on most kits.
4. **Verification test — does the generator fire on its own?** Open a *fresh* session with no context about this install and say something in the user's own words, like *"package the <thing> for <person>"* — nothing that names the skill. **With subagents:** launch one and give it that sentence. **Without subagents:** the user opens a new chat or window, types the sentence, and brings back what happened. If the procedure loads without being named, the wiring works. If it doesn't, the trigger phrases in the description (or the wiring line from Phase 2 step 4) are too narrow — widen them with the user's actual phrasing and retest. Record the sentence used and the outcome in `STATUS.md`.
5. **Retro.** Append what this run taught to `skill/references/lessons.md` (dated, two to four lines per lesson), edit `kit-skeleton.md` right now if something should change how kits get built, and register the kit in `<kits home>/README.md`.

---

## Phase 4 — Wrap

1. Walk the user through what's installed and where: the procedure, the kits home and its registry, the term list, the instructions-file wiring, and the kit they just shipped.
2. Tick every phase in `STATUS.md`; empty the Blockers row or say plainly what's still open.
3. Leave the habit behind, one line: **"The second time you explain one of your systems to someone, stop explaining and package it."**
4. **Preserve the install record before anything else** — confirm the choices from `STATUS.md` are written into the kits registry's install record. Only then is this kit folder safe to delete; keeping it around for re-reads is also fine.

---

## If things go wrong

- **A capability is missing** (no subagents, no shell, no git): take the nearest degradation path named in the decision point. Every step here has a sequential, chat-only, or by-hand form.
- **The user's conventions conflict with this kit's names:** keep their names and map the jobs. The kits home can be called anything; `STATUS.md` can be `PROGRESS.md`. Keep one canonical entry file per kit and record which in the install record.
- **The extraction feels impossible — everything looks like wiring.** That's the signal the source is a *workflow*, not a system: it works because of who runs it, not because of how it's structured. Say so rather than shipping an abstraction. Some builds shouldn't be kits.
- **A cold test comes back below 8 twice in a row:** stop patching and re-read the mechanism/wiring split. Repeat failures at that level are almost always over-cutting in step 2 — the kit is missing the concrete layer, not a paragraph.
- **The sanitize search finds a hit:** fix the content, never the pattern. Editing the pattern to make a hit disappear is how a name ships.
- **A step fails twice:** don't loop. Note it in `STATUS.md` Blockers, take the fallback, and tell the user.
