---
description: Check Index.md against the current Notes/ and Knowledge/ files, and fix any drift
---

Audit `Index.md` (repo root) against the vault's actual contents, per the upkeep rule in CLAUDE.md.

1. List every file currently in `Notes/` and `Knowledge/`.
2. List every `[[wikilink]]` target currently referenced in `Index.md`.
3. Compare the two:
   - A `Notes/` or `Knowledge/` file missing from `Index.md` → add it to whichever existing section fits (or a new section if none does), with a short one-line descriptor matching the existing style.
   - An `Index.md` link pointing to a note that no longer exists (renamed or deleted) → fix the link or remove the entry.
4. `Dailies/` and `Sources/` are intentionally *not* linked individually from `Index.md` — don't flag daily notes or source PDFs as missing.
5. Leave everything else untouched — don't reword descriptors or reorder existing entries just because you're in the file.
6. Report a short summary: what was added, what was fixed, or that everything was already in sync.
