# Understanding the kit generator

Everything you need to understand what an implementation kit is, why it's shaped the way it is, and what installing this actually changes about how you share your work. About ten minutes. `IMPLEMENT.md` is the *how*; this is the *what and why*.

## The problem

There is a gap between "I built something that works" and "other people are using it." Most attempts to close that gap fail in one of three predictable ways.

**Hand over the files.** They're full of your paths, your folder names, your teammates' names, the tools you happen to pay for. The recipient opens them, finds a reference to a directory that doesn't exist on their machine, and quietly stops. Worse: your files encode a hundred small choices you made for reasons you no longer remember, and they have no way to tell which ones matter.

**Write documentation.** Documentation describes a system to a reader who then has to install it themselves, from prose, while doing their actual job. Nobody has that afternoon. And documentation is written from the inside — it explains the parts that were hard for *you* and skips the ones that were obvious to you and opaque to everyone else.

**Explain it in a meeting.** This works exactly once, for exactly one person, and only until they hit the first decision you didn't cover.

Meanwhile the recipient's environment is *not yours*. They might be running a different AI, or a weaker one, or none. Different folder structure, different tools, different budget, different tolerance for ceremony. A system that assumes your setup is a system they can't run.

## The core idea

**Write the installer for their AI, not the documentation for them.**

A kit is a small repo whose primary reader is a machine: the recipient opens it with whatever assistant they use, says "walk me through this," and their AI does the work — checks what the environment can actually do, asks the handful of questions where the answer genuinely depends on them, creates the files in their structure with their vocabulary, and keeps a progress file so a dead session isn't a lost afternoon.

Three things make that work, and all three are non-obvious:

1. **The mechanism/wiring split.** Before writing anything, separate what makes your system work *anywhere* from what only works *for you*. Mechanism becomes the kit. Wiring gets genericized — or becomes a question the recipient answers.
2. **Decisions instead of assumptions.** Every place you made a choice becomes an explicit decision point, with the options, the trade-offs, and your choice as a *recommendation with the reasoning attached*. The recipient can follow your lean or diverge, and either way they know what they're trading.
3. **Nothing ships untested by a stranger.** A fresh agent with no idea who you are installs the kit for real and scores it. That is the only step that reliably finds what you couldn't see.

## The concepts

**Implementation kit** — the artifact. A folder or repo containing an installer written to an AI, a progress file, human-facing explanation, and a payload. Typically two to five thousand words; the three kits built before this one ran 4,027, 8,368 and 8,400 words across their root files.

**Mechanism vs. wiring** — the extraction split. *Mechanism*: file jobs, the protocol, the gates and tests, the pipeline logic, the rules that exist because something went wrong once. *Wiring*: your paths, your names, your customers, your accounts, your folder tree. The single most common failure is cutting too much — strip the *subject matter* as wiring and you ship a correct, incomprehensible abstraction. That has happened, and the fix was to put a sanitized version of the subject back as a worked example.

**Decision point (DP)** — a structured block: the question in plain words, two to four options with trade-offs, a recommendation conditioned on what the environment scan found, and an instruction to record the choice in the progress file. DPs come from exactly two sources — platform capabilities your build leans on, and choices you made that others could reasonably make differently. Three to seven per kit; more than seven means the kit is really two kits.

**Environment scan** — the first thing the recipient's AI does, before any questions. What am I (files? shell? subagents? memory?), where does their work live, what conventions already exist, what prerequisites are present. Every later recommendation is conditioned on these findings, which is why the scan comes before the decisions and not after.

**Degradation tier** — the four levels every kit must work at: full agent (files, shell, subagents, memory) → mid agent (files and shell only) → chat-only AI → a human with no AI. A kit that dead-ends when a capability is missing is a failed kit. Cold tests always role-play the mid tier, because it exercises the most branches.

**The sanitize gate** — the mandatory pass before anything leaves: build a term list (names, company, customers, products, paths, internal tools), strip and genericize, prove it with a search over the finished kit that must return nothing, then read the whole kit once by hand for the thing search can't catch — an example that's transparently your business with the names changed.

**The cold test** — the mandatory proof: a fresh agent, zero context, fetches the kit the way a recipient would and installs it for real, then reports a friction log and a verdict out of 10. The bar is 8. Every kit so far has come back between 7 and 8.5 on the first run, and every run found real defects.

**The kits registry** — one index where every kit you build is registered, with an install record of your policy choices. Without it, the second person who wants a kit builds one that already exists.

**Standing instructions wiring** — one line added to your AI's always-loaded instructions file, so the procedure fires when you say "package this for Dana" instead of only when you remember it exists. An install that only creates folders dies at your first new chat.

## How a packaging run actually flows

1. **Intake** — which build, for whom, whole thing or one capability. One round of questions, maximum.
2. **Read and extract** — your AI reads the *entire* source and writes the mechanism/wiring split down as two explicit lists. This is the step people skim and it is the whole job.
3. **Mine the decisions** — scan the extraction for platform dependencies and personal choices. Three to seven.
4. **Draft** — stamp out the scaffold, write the installer, the progress file, the README and overview, and the payload.
5. **Sanitize** — the pass, the search gate, the judgment read, the report.
6. **Approve** — whoever owns the build rules on the name and the visibility. The approval has to name the visibility out loud.
7. **Publish** — a repo, or a zip on a shared drive.
8. **Cold test** — fresh agent, distributed copy, real execution, verdict out of 10. Under 8: fix and re-run.
9. **Retro** — append what you learned to the lessons file, and if it should change how kits are built, edit the skeleton *now*.

A small kit runs about 60–120 minutes end to end, most of it in steps 2 and 8.

## What gets installed, and your role

On disk: the procedure (where your platform finds skills, or in your workspace), a kits home with its registry, a sanitize term list, and one line in your standing instructions file.

**Your AI does:** the reading, the split, drafting the kit, the search gate, the publish commands, the cold test dispatch, the fixes.

**Only you can do:** name what's actually sensitive (your AI cannot tell which customer example is fine), rule on visibility, judge whether a recommendation is really your recommendation, and decide when a build shouldn't be a kit at all.

## Platform needs

Nothing is required. `git` plus the GitHub CLI make distribution and updates easy; without them a kit is a zipped folder and the only loss is the update path. A shell makes the sanitize gate a one-liner; without one it's find-in-files, slower and equally valid. Subagents make the cold test push-button; without them, a fresh chat window with the cold-test brief does the same job. Chat-only works throughout: you create the files, your AI narrates, and the progress file is a note you paste back.

## What it is not

**Not a template repo.** Templates hand over files; kits hand over a script, a set of decisions, and a test. The difference shows up on the recipient's second day.

**Not documentation.** Documentation is read by a human who then has to do the work. A kit's primary reader is an AI that does the work.

**Not a package manager.** There's no dependency resolution, no versioning, no registry beyond the one index you keep. A published kit is a repo you fix in place; recipients pull.

**Not a way to share things you didn't build.** The extraction step depends on understanding *why* every rule exists. If you can't answer that for a system, you can't package it — you can only forward it.

**Not for everything.** Some systems work because of who runs them, not how they're structured. If the extraction feels impossible because everything looks like wiring, that's the signal: say so instead of shipping an abstraction.

## Provenance

This procedure was built and run in a real workspace, not designed on paper. It produced three shipped kits before being packaged as one itself, and every rule in `kit-skeleton.md` traces to a specific defect a cold test found — a decision the kit used but never asked about, a placeholder with no default, an option that deleted a file the payload required, a "non-negotiable" rule that was impossible on a weaker platform, a shell recipe that could not work. Six cold tests, verdicts from 7 to 8.5 out of 10.

What it has **not** been proven on: kits packaging code-heavy systems for teams in other languages, or any recipient environment outside the common AI coding tools. The degradation tiers are designed and tested, not battle-worn across dozens of installs. Treat the recommendations as informed leans, which is exactly how the decision points present them.
