# Sanitize checklist

Run in full on every kit before the owner's gate. Three parts: the pass, the proof, the report.

## Part 0 — Build the term list (do this first, once per kit)

The search gate is only as good as the words you feed it. Before the pass, write down every term that would leak the owner's private context if it survived. Ask for anything you can't infer:

| Category | Examples of what to list |
|---|---|
| People | The owner's first and last name, usernames and handles, teammates, customers, partners, friends |
| Organizations | Company name and abbreviations, product names, brand names, client names |
| Places on disk | The home-directory username, workspace folder names, any absolute path |
| Internal tools | Names of internal systems, databases, internal repos, the AI persona's name if it has one |
| Accounts | Account emails, subdomains, tenant names, integration endpoints |

Save this list — you'll reuse it for every kit from the same source. Two practical notes:

- **Common-word collisions:** a person named Bill, Grace, or Frank will drown a case-insensitive search in false hits ("billing", "disgrace", "frankly"). Put those names in a **separate case-sensitive, word-boundary** pattern instead of skipping them.
- **Multi-word phrases don't survive line wrapping.** Line-oriented search tools match one line at a time, so a two-word company name split across a line break will not be found. Always search the **most distinctive single token** of a phrase, not the whole phrase.

## Part 1 — The pass: strip or genericize

- **Names** — people, company, products, teams, customers → "the user", "your AI", "the owner", or invented neutral examples.
- **Paths** — anything under the owner's home directory or workspace tree → relative paths, or the recipient's own locations decided at a DP.
- **Workspace mechanics** — wiki-style `[[links]]` become plain markdown links unless the kit specifically targets that tool; tool-specific frontmatter is dropped on the same test; folder trees that only make sense inside the owner's workspace get cut or generalized.
- **Business data** — revenue, pricing, margins, targets, customer lists, real company names in examples → invented neutral examples that carry the same shape.
- **Credentials and accounts** — API keys, tokens, account emails, integration configs with personal endpoints, database contents → a placeholder plus an instruction that the AI asks the user for theirs.
- **The owner's rulings as law** — "(decided by <name>, <date>)" stamps and "<name> rules that…" become "the user decides." Their *choices* survive only as DP recommendations, with the reasoning attached.

## Part 2 — The proof: the search gate

From the kit root. Both commands must produce **no output** (a clean run exits non-zero, which is the pass):

The term list from Part 0 lives in two files in the kits home, so the gate reads it instead of you retyping it each time. One pattern per line, no comments, no blank lines:

- `<kits home>/.sanitize-terms` — the main list, matched case-insensitively. Plain words: `acme`, `widgetflow`, `northwind`.
- `<kits home>/.sanitize-terms.cs` — names that collide with ordinary words, matched case-sensitively with explicit word boundaries: `\bBill\b`, `\bGrace\b`.

Then, from the kit root (point `TERMS` at your kits home):

```bash
TERMS=../.sanitize-terms
test -s "$TERMS" && test -s "$TERMS.cs" || echo "TERM LIST MISSING OR EMPTY — the gate below proves nothing"
grep -rniEf "$TERMS"    . --exclude-dir=.git   # case-insensitive pass
grep -rnEf  "$TERMS.cs" . --exclude-dir=.git   # case-sensitive, word-boundary pass
```

Both must print nothing (`grep` exits 1 on no match — that is the pass).

**Do not judge this by exit code alone**, and this is tested, not theoretical: when the term file is missing, some `grep` builds print an error to stderr and still **exit 0** — which reads as "match found" to a careful agent and as "fine" to a careless one, and is neither. That is why the `test -s` guard is on the first line. Read the actual output every time: no lines printed and no error printed is the only pass.

Any hit means fix the **content**, then re-run — never edit the pattern to make a hit disappear. This step is mechanical; a cheap subagent can run it.

**Prove the gate isn't passing vacuously**, once per kit: append a line containing three real terms to a scratch file inside the kit, re-run, confirm it fires, delete the scratch file. A gate that has never fired is a gate you have not tested.

**One tested false positive:** if the kit you are sanitizing is itself *about* sanitizing (or otherwise quotes a term list), the pattern will match its own documentation. Read every hit before acting — a hit on the line that *defines* the pattern is not a leak. Exclude that file explicitly rather than weakening the pattern.

**No shell?** Use your editor's find-in-files across the kit folder, one term at a time, case-insensitive, and record the same result. It's slower and equally valid.

**The false negative to watch for:** the search cannot catch *structural* leakage — an example that is transparently the owner's business even with the names swapped, or a folder tree that only makes sense in their workspace. That takes one judgment read of the full kit, top to bottom, after the search comes back clean.

## Part 3 — The report (always delivered to the owner)

Three short lists, in the message that asks for the publish ruling:

1. **Removed or genericized** — what, and what it became.
2. **Deliberately left out** — source sections that didn't survive, one line of reasoning each. The owner may veto a cut.
3. **Survived as recommendations** — the owner's choices, now framed as DP leans with their reasoning intact.
