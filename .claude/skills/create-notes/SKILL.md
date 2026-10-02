---
name: create-notes
description: Create a new notes repository from this template, an Obsidian vault that Claude Code maintains. Asks the user where to create it, copies the template's CLAUDE.md and slash commands there, runs git init and makes the first commit. Use when the user wants to create, set up, scaffold or start a notes repo or notes vault from this template.
argument-hint: "[location]"
---

Create a new notes repository from this template. The template is the repository this skill lives in: its root is three levels above this skill's directory, `${CLAUDE_SKILL_DIR}` (the directory that contains `.claude/skills/create-notes/`). It is the source, not a vault, so don't create notes in it and don't modify, commit or push it.

## 1. Ask where it goes

The location is the user's to choose. Ask: "Where should the notes repo live? Give the full path of the directory to create, for example `~/notes`." Then wait for the answer.

- Arguments given to the command: `$ARGUMENTS` (empty or unexpanded means none). If the user gave a location there, or named one in their request, use it instead of asking again.
- Never assume, default or invent a location: not the home directory, not a path from CLAUDE.md, memory or another repository. If you can't ask (non-interactive run) and none was given, stop and say a location is required.

Expand `~`, resolve a relative path against the current directory, and state the resulting absolute path back before touching anything.

## 2. Check before creating

The template must contain `CLAUDE.md`, `.claude/commands/extract-knowledge.md` and `.claude/commands/review-index.md`; if not, stop, because this isn't the template.

The location:

- It exists: fine only if it is an empty directory, or a git repository with no commits and no files (a fresh clone of an empty remote; skip `git init` for it and keep its branch). Anything else, stop and ask for another location. Never overwrite or merge into existing content.
- It is inside the template: stop and ask for another.
- It is inside another git repository (`git -C <the location, or its nearest existing parent> rev-parse --show-toplevel`): say which one and ask whether that is intended. A notes repo is normally its own repository.
- Its parent doesn't exist: fine to create it, but say so, since it may be a typo.

## 3. Create it

Copy exactly two things from the template root: `CLAUDE.md` and the `.claude/commands/` directory. Nothing else: not `README.md`, not `LICENSE` (it belongs to the template; the user's notes are private and must not carry it) and not `.claude/skills/` (it is for the template itself). Don't create folders, notes or `Index.md`; `CLAUDE.md` has Claude create those when something first belongs in them.

```bash
mkdir -p "<location>/.claude"
cp "<template>/CLAUDE.md" "<location>/"
cp -R "<template>/.claude/commands" "<location>/.claude/"
git -C "<location>" init -b main
git -C "<location>" add CLAUDE.md .claude
git -C "<location>" config user.name && git -C "<location>" config user.email && git -C "<location>" commit -m "Initial commit: notes repo from template"
```

On Windows use the PowerShell equivalents. `git init -b` needs git 2.28 or newer; on older git run `git init`, then `git symbolic-ref HEAD refs/heads/main`.

The two `git config` checks chained before the commit are a guard, not steps to skip: git will otherwise invent an author from the username and hostname. If either value is empty, nothing is committed; leave the files staged, say so, and give the user the commands to set an identity (`git config user.name` and `user.email`, repo-local, or `--global` if they choose). Never change git config yourself.

## 4. Report

Check `git -C "<location>" log --oneline` and `git -C "<location>" status --short` (clean), then tell the user briefly:

- where the repo is and what is in it: `CLAUDE.md`, plus the `/extract-knowledge` and `/review-index` commands;
- next steps: open the folder in Obsidian ("Open folder as vault"), start Claude Code in it, and read through `CLAUDE.md`, especially its Syncing and Committing rules;
- its remote: a new repo has none yet, and a fresh clone's first commit is local until pushed. Any remote should be **private**: the new repo's `CLAUDE.md` commits and pushes automatically, and `Sources/` may hold PDFs that can't be redistributed. Offer to create a private remote and push; do it only if the user says yes.
