# templates/ — install matrix

What the installer (IMPLEMENT.md Phase 2) puts into the user's workspace, and which decision gates each.

| Template | Installed as | Gated by | Notes |
|---|---|---|---|
| `KIT-REGISTRY.md` | `<kits home>/README.md` | DP-2 (where kits live and how they're distributed); the Install record line summarizes DP-1…DP-5 | The index every finished kit is registered in. Fill it in *with the user* — never install it blank. |
| `kit-scaffold/*.template` | Copied into `<kits home>/<kit-name>/` at the start of each kit build, then written over | Used on every kit at every tier — except `OVERVIEW.md.template`, which the explainer-depth decision (DP-4) gates: skip it at lean depth and delete the README template's pointer line to it | Stubs, not content. They exist so the generator never has to remember the file set. Drop the `.template` suffix when copying. The scaffold is deliberately thin; the rules live in `skill/references/kit-skeleton.md`. |

The scaffold is **not** installed at setup time. It's a working tool for the generator, used once per kit. If the user's platform can't reach the kit folder later (chat-only), have them keep the scaffold files as documents they paste.
