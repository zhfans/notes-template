# Notes Template

A starting point for a personal [Obsidian](https://obsidian.md) vault that [Claude Code](https://claude.com/claude-code) helps maintain. Almost everything lives in one file, [`CLAUDE.md`](CLAUDE.md): the conventions that keep a vault consistent as Claude adds to it. It is distilled from the `CLAUDE.md` of a vault in daily use.

## What's in it

- [`CLAUDE.md`](CLAUDE.md) — how notes are laid out, named, linked and indexed; what counts as *knowledge* versus *project narrative*; how to mark claims that rest only on your own testing; and when Claude syncs, commits and pushes.
- [`.claude/commands/`](.claude/commands) — two optional slash commands that maintain the vault:
  - `/extract-knowledge` sweeps notes changed since the last run for general facts that belong in `Knowledge/`.
  - `/review-index` checks `Index.md` against the notes that actually exist and fixes drift.
- [`.claude/skills/create-notes/`](.claude/skills/create-notes/SKILL.md) — a skill that creates a new notes repo from this template: it asks where to put it, copies `CLAUDE.md` and the slash commands there, and makes the first commit.

There are deliberately no folders or sample notes. Claude creates `Notes/`, `Dailies/`, `Knowledge/`, `Sources/` and `Index.md` the first time something belongs in them.

## The conventions in brief

- **Atomic notes** under `Notes/`, one topic per note, cross-linked with `[[wikilinks]]`.
- **Dailies** are a short log: one file per day, a sentence or two per topic, each pointing at the note that holds the detail.
- **Knowledge** holds source summaries and general, reusable facts, kept apart from project narrative, with a callout marking claims that rest only on your own testing.
- **Index** maps the whole vault and is updated whenever a note is added, renamed, moved or removed.

## Using it

Create your notes repository either way:

- **With Claude Code.** Clone this repository, start Claude Code in it, and ask it to create your notes repo (or run `/create-notes`). It asks where to put it, copies `CLAUDE.md` and the slash commands there, and makes the first commit. This repository stays untouched.
- **From GitHub.** Create a repository from this template (**Use this template**), then delete `README.md`, `LICENSE` and `.claude/skills/` from it, since they belong to the template and your notes are private.

Then:

1. Open the new folder in Obsidian as a vault, and start Claude Code in it.
2. Read through `CLAUDE.md` and edit it to taste. The part you are most likely to change is the Syncing and Committing rules.

## Before you start

- Keep your notes repository **private**. `CLAUDE.md` tells Claude to commit and push automatically, and `Sources/` can end up holding PDFs you have no right to redistribute.
- The Syncing and Committing rules assume a git remote named `origin`. Without one, Claude only commits locally.

## License

[MIT](LICENSE). It covers the template only and is not copied into your notes repository: the skill leaves it out, and the GitHub route above deletes it.
