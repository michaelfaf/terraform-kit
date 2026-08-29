# Kit progress

> Maintained by the implementing AI. Tick tasks as they complete; record decisions the moment they're made. A fresh session resumes from this file alone.

## Scan results (Phase 0)

| Check | Finding |
|---|---|
| Platform (files? shell? subagents? persistent memory?) | _pending_ |
| Can mechanical steps run on a cheaper model? (delegation only — never the extraction) | _pending_ |
| Where the user's builds live (folder / vault / repo) | _pending_ |
| Existing conventions / standing instructions file (path) | _pending_ |
| `git` available? | _pending_ |
| `gh` (GitHub CLI) available? | _pending_ |
| Shell search available (`grep` / `rg`)? | _pending_ |
| Can launch a context-free subagent? | _pending_ |
| How the platform discovers skills (auto-loaded directory / rules-file reference / paste-only) | _pending_ |
| Who owns the builds being packaged (user, or someone else) | _pending_ |
| Who receives the kits — inside the org / outside the org / the public | _pending_ |

## Decisions (Phase 1)

| # | Decision | Choice | Notes |
|---|---|---|---|
| DP-1 | Where the generator lives + what it's called | _pending_ | |
| DP-2 | Kits home + how kits reach recipients | _pending_ | |
| DP-3 | Cold-test tier (bar stays 8/10) | _pending_ | |
| DP-4 | Explainer depth per kit | _pending_ | |
| DP-5 | Sanitize enforcement + term-list home | _pending_ | |
| DP-6 | Publish approver | _pending_ | |

## Tasks

### Phase 0 — Environment scan
- [ ] Platform + capabilities identified
- [ ] Workspace located (or chat-only path confirmed)
- [ ] Existing conventions read
- [ ] git / gh / search / subagent checks done
- [ ] Scan results recorded above

### Phase 1 — Decisions
- [ ] DP-1 through DP-6 decided and recorded above

### Phase 2 — Install
- [ ] Procedure installed where the platform discovers it (path recorded here)
- [ ] `<kits home>/` created with `README.md` from `templates/KIT-REGISTRY.md` — install record filled in *with the user*
- [ ] Sanitize term lists created and populated from the user's real terms (`.sanitize-terms` + `.sanitize-terms.cs`); gate run once to prove the command works
- [ ] Wiring added to the user's standing instructions file (file path recorded here)
- [ ] Install paths recorded here

| Install paths | |
|---|---|
| Procedure | _pending_ |
| Kits home | _pending_ |
| Term lists | _pending_ |
| Working-notes folder (`<kits home>/_working/`) | _pending_ |
| Instructions file edited | _pending_ |

### Phase 3 — First live run
- [ ] Source build chosen with the user (small, real)
- [ ] Mechanism / wiring split written down explicitly, as two lists, in `<kits home>/_working/`
- [ ] 3–7 decision points mined and drafted
- [ ] Kit drafted from the scaffold, following the skeleton
- [ ] Sanitize gate run — search clean, non-vacuity check done, judgment read done, report delivered
- [ ] Approval obtained per DP-6 (visibility named in the approval)
- [ ] Kit published / distributed per DP-2
- [ ] Cold test run against the *distributed* copy — verdict recorded, fixes pushed
- [ ] Trigger test: fresh session, user's own words, procedure fired without being named
- [ ] Retro appended to `lessons.md`; kit registered in `<kits home>/README.md`

| First kit | |
|---|---|
| Kit name + what it packages | _pending_ |
| Where it lives | _pending_ |
| Cold-test verdict + top fixes | _pending_ |
| Trigger sentence used, and did it fire? | _pending_ |

### Phase 4 — Wrap up
- [ ] User walked through what's installed and where
- [ ] Install record preserved in the kits registry
- [ ] This file fully ticked; final state recorded

## Blockers

- none
