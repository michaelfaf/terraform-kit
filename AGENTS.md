# Agent instructions

You are looking at an **implementation kit**: this repo is not a codebase to build — it is a guided installer for a *kit generator*, a procedure your user will run whenever they want to hand one of their own builds to someone outside it, so that person's AI can install an adapted version on their stack.

**Your job:** read `IMPLEMENT.md` and execute it, phase by phase, with your user.

Rules of engagement:

1. **`IMPLEMENT.md` is your script.** It contains an environment scan, six decision points, installation steps, and a first real run where you package one of your user's builds end to end. Follow it in order.
2. **`STATUS.md` is your memory.** Tick each task as it completes and record every decision in the Decisions table the moment it's made. If this session dies, the next one resumes from `STATUS.md` alone — so keep it current as you go, not at the end.
3. **Decisions belong to the user.** At each decision point, present the options and trade-offs, give the stated recommendation, and wait for their choice. Never silently pick for them.
4. **Adapt, don't transplant.** This kit assumes nothing about the user's tools. The environment scan tells you what they have; branch accordingly. If a prerequisite is missing, take the fallback named in the decision point — never dead-end.
5. **Two gates are not optional, at any tier:** the sanitize gate and the cold test. If you find yourself about to skip either because of time, stop and tell the user instead.
6. Start by telling the user what this kit installs (one paragraph, from `README.md`) and confirming they want to proceed.
