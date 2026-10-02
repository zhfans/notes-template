---
description: Sweep Notes/ files modified since the last run for general, source-verifiable technical facts that belong in Knowledge/ instead — an existing note or a brand-new one — and advance the cutoff
---

Look for content sitting in `Notes/` that actually belongs in `Knowledge/` instead, per the Knowledge rules in CLAUDE.md. `Knowledge/` in this vault isn't only source summaries (a paper, a repo) — it also holds general, reusable technical facts captured from a conversation rather than a document, whenever they'd read the same regardless of which project surfaced them. Such notes often open with a line like "General [X] background, not tied to a specific paper or project in this vault — captured from a direct Q&A rather than a document," and some have no inbound link from any `Notes/` file at all. That's the real target shape — not "does this note link to an existing Knowledge file," which misses every candidate with no link yet, new note or old.

Two shapes a migration can take:
- **Source-specific fact → existing Knowledge note.** A detailed check of how a particular model or tool behaves, found while working on a project, lives in full in that source's own `Knowledge/` summary, with just a pointer left in the topic note.
- **General fact → its own new Knowledge note.** A timing comparison recorded in a project note that is really a fact about two tools in general, not about that project, earns its own `Knowledge/` note — even when nothing forced it to cite an external source.

1. **Read the cutoff.** `.claude/extract-knowledge-last-run` holds a UTC timestamp (`YYYY-MM-DDTHH:MM:SSZ`) marking the end of the last pass. If the file doesn't exist yet, this is the first run — treat every `Notes/` file as a candidate.

2. **List candidates**: `Notes/` files whose most recent commit is at or after the cutoff, using the author date normalized to UTC so it's safely string-comparable regardless of the committer's local timezone:

   ```bash
   awk '
     NR==FNR { tracked[$0]=1; next }
     /^@@/ { date=substr($0,3); next }
     NF && tracked[$0] && !seen[$0]++ { print date "\t" $0 }
   ' <(git -c core.quotepath=false ls-files Notes/) \
     <(TZ=UTC0 git -c core.quotepath=false log --name-only --diff-filter=AMR --pretty=format:'@@%ad' --date=format-local:'%Y-%m-%dT%H:%M:%SZ' -- Notes/) \
     | awk -F'\t' -v cutoff="$(cat .claude/extract-knowledge-last-run 2>/dev/null)" '$1 >= cutoff || cutoff==""'
   ```

   Full-timestamp precision matters here specifically because this cutoff can advance more than once per day: a day-only cutoff would re-flag everything touched earlier that same day as still "since cutoff" on a same-day rerun — including content this command already migrated earlier that day.

3. **Read every candidate note in full** — not just the ones with existing `[[Knowledge note]]` wikilinks; a note with zero Knowledge links can still hold general-knowledge content, and that's exactly the case a links-only check would miss entirely. For each note, look for passages that state an objective, source-verifiable fact — something true about a tool, protocol, API, library, or cited source's own behavior, independent of this vault's specific project — as opposed to a decision, a judgment call, a project-specific finding, or narrative about what was tried and why. The test: would this sentence read the same, and still be useful, in a completely unrelated future project? If yes, it's a candidate. If it only makes sense as part of *this* project's story, it stays in `Notes/`.

4. **For each match found, decide where it belongs:**
   - If it's a fact about a source that already has a `Knowledge/` note (check by filename, whether or not the candidate note currently links to it), fold it in at the point it actually belongs, reorganizing existing content if that's what fits, rather than tacking it onto the end. If the specific fact being folded in is itself only backed by this vault's own testing — not by that source's own paper/docs/code — apply the provenance callout below to just that passage; don't let it inherit the surrounding note's externally-sourced confidence by default.
   - If it's a general fact with no existing home, create a new `Knowledge/` note for it, shaped like the existing general-knowledge notes: an opening line naming the general topic and, if useful, what prompted capturing it (a wikilink back to the originating `Notes/` file is fine). Look for real external sources (docs, an RFC, a paper, a blog post) backing the general claim, and give it a proper `## Sources` section if any are found.
   - Either way, if no external corroboration exists and the claim rests solely on this vault's own one-off empirical test, mark it per CLAUDE.md's `> [!info]` provenance-callout convention (at the note or passage level, whichever fits) instead of presenting it with the same unmarked confidence as an externally-sourced fact.
   - Replace the passage in the `Notes/` file with a short pointer back to it — the same teaser-and-link shape already used for `## Ideas` entries and `Dailies/` entries. If part of the original passage is project-specific (why this mattered here, what was decided), keep that part in `Notes/` rather than deleting it wholesale.
   - Skip anything ambiguous or that reads more like project narrative than a standalone fact — leave it in `Notes/` untouched. A note appearing in the candidate list doesn't obligate a migration out of it.

5. **Update `Index.md`** whenever a new `Knowledge/` note gets created — this is a normal outcome of this command, not an edge case, so don't skip it.

6. **Write the current UTC timestamp to `.claude/extract-knowledge-last-run`**, overwriting the previous cutoff (create the file if this is the first run):

   ```bash
   TZ=UTC0 date '+%Y-%m-%dT%H:%M:%SZ' > .claude/extract-knowledge-last-run
   ```

7. **Report a short summary**: how many candidate notes were examined, what was actually migrated (`Notes/` file → `Knowledge/` note, one line each — flag which target notes are newly created), and the new cutoff saved. If nothing qualified, say so plainly rather than forcing a result.
