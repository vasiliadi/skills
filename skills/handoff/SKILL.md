---
name: handoff
description: Write a session handoff file to .handoffs/ at the project root (a self-ignored folder) and archive the previous handoffs to .handoffs/archive/, so the next session can pick up the work without the transcript.
disable-model-invocation: true
---

# Session Handoff

Write a handoff file for the current session: a snapshot a fresh session (or another agent) can act on without reading this conversation.

The handoff lives in a `.handoffs/` folder at the project root that ignores its own contents, so it works in any project — with or without git conventions, agent rule files, or docs folders — and never shows up in the project's history. The leading dot marks it as tool-generated local state, like `.cache`, and keeps it out of the way in projects that are not yours.

## Steps

### 1. Prepare the folder

Find the project root: `git rev-parse --show-toplevel` if this is a git repository, otherwise the current working directory.

Then make sure this layout exists, creating whatever is missing:

```
.handoffs/
├── .gitignore      # contains a single line: *
├── archive/
└── handoff-*.md
```

The `.gitignore` with `*` ignores everything in the folder, itself included, so the handoffs stay local without touching the project's root `.gitignore`. If `.handoffs/.gitignore` already exists, leave it as is.

One exception: if `git ls-files .handoffs` shows the project already tracks files in `.handoffs/`, the folder means something else in this project. Do not add the ignore file or write there — stop and ask the user where the handoff should go.

### 2. Record the working state

If this is a git repository, run `git status` and note the current branch, and check whether it is pushed and has an open PR (`gh pr view` if `gh` is available; otherwise say unknown). Record the branch in the template as one of: uncommitted changes / committed, not pushed / pushed / merged as `sha` / abandoned. Not a git repository → write `Branch: none (not a git repository)`.

If tracked changes are uncommitted, list them under Unfinished Work with their paths and mention them in your summary.

### 3. Fill in the template

The template below has a fixed core and optional sections:

- **Core, always present:** the header (date, branch, PR/issue), Goal, Unfinished Work, Files to Load Next Session. These are what make a handoff useful at all — whether the job landed, and where the next session starts — so a reader can always find them in the same place.
- **Optional:** What Was Accomplished, Decisions Made, Key Numbers, Open Questions, Notes, What NOT to Re-Read. Include a section only when you have something real to put in it; omit it entirely rather than writing "None". Empty sections are noise, and filling them for the sake of the form produces filler.
- **Notes** is free-form: context, gotchas, how you got to a conclusion — anything the next session needs that does not fit another section. Do not let it become a second copy of the structured sections.

Match the length to the session. A short session can be a handful of lines within the core; a long one earns more sections. Be specific either way: exact file paths for every output, exact numbers discovered, conditional logic established. See *Writing the Goal section* and *Writing the tail* below.

Do not duplicate what is already captured elsewhere — specs, plans, ADRs, issues, commits, diffs. Reference them by path, sha, or URL and add only what they don't say (why a decision was made, what is still open). A handoff that retells a plan goes stale the moment the plan changes, and the next session reads the same content twice.

### 4. Archive the previous handoffs

Before writing the new file, move every existing `.handoffs/handoff-*.md` to `.handoffs/archive/`. Order matters: once the new handoff is on disk it matches the same glob.

- Every file moves — when this step ends, `.handoffs/` holds no `handoff-*.md`.
- A same-named file already in `archive/` is an earlier revision of the same work log: let the incoming file replace it and say so in your summary. Never keep both under a suffixed name.

### 5. Write the new handoff

Write it to `.handoffs/handoff-[YYYY-MM-DD]-[topic].md`, where `[topic]` is a short kebab-case slug of the session's work (e.g. `handoff-2026-03-14-auth-refresh.md`). Never overwrite: if that path still exists after step 4, pick a different topic slug for the date.

### 6. Report

Tell the user the path of the new handoff, what it contains, which files were archived (and any that replaced an archived file), and anything flagged under Unfinished Work that needs their attention.

## Boundaries

The handoff is a read-only snapshot of the session. It writes only inside `.handoffs/`. Do not commit, push, create branches, or edit any other file as part of it — those are decisions about the work itself, and the user should make them on a branch they choose, not have them happen as a side effect of saving notes.

This applies to knowledge too. If you notice a durable fact (a convention, a gotcha, a decision) that belongs in the project's own documentation — whatever the project uses: `README`, `CLAUDE.md`, `AGENTS.md`, a `docs/` folder — and it never made it there, do not write it now. Record it under Unfinished Work with where it should go, and mention it in your summary. Do not save it to agent memory instead, either: the handoff and memory are local to this machine, and a fact that only lives there is lost to everyone else working on the project.

Keep secrets out of the file. Being gitignored does not make it private: handoffs get pasted into other agents' prompts and shared with colleagues. Never write API keys, tokens, passwords, connection strings with credentials, or personal data — refer to where the value lives instead (e.g. "`STRIPE_KEY` in `.env.local`").

## Writing the Goal section

**Task** is the job as you understood it when work began, in your own words — not a quote of the user's message. State the objective and what would count as done, at the level of detail you actually worked to: one or two sentences a next session could act on without the transcript.

- Build it from everything settled before the work itself began — the first edit, or, in a session with no edits, the first move toward the deliverable. That is the opening request plus any clarification before that point, so a task negotiated over several turns reads as one statement, and a task given in pieces reads as the single thing it became.
- This line is a two-way check: the user reads it to see whether their ask landed the way they meant it. Write the understanding you acted on, not the safest paraphrase.
- Do not back-fill it from what got built. If your understanding was wrong or the target moved mid-session, the original understanding stays here and the correction goes under Scope changes.
- If the messages that set the task are no longer available, reconstruct from the earliest record you still hold and mark the line ASSUMED.

**Delivered** is one of DELIVERED / PARTIAL / BLOCKED (stopped by something outside the session) / DROPPED (called off), followed by what is true now and the evidence that proves it — a commit sha, a passing test run, a verified output.

- A session whose deliverable is an answer is DELIVERED when the answer is in the file.
- Judge it against the task as corrected — the Task line plus every correction under Scope changes — not against the Task line alone.
- It must agree with Unfinished Work: DELIVERED only when nothing left undone blocks the goal; PARTIAL and BLOCKED each point at the numbered Unfinished Work item covering the gap; DROPPED still records the state the abandoned work was left in (an uncommitted branch, an unapplied migration), because the next session inherits it.

**Scope changes** records work added or abandoned after the task was set, and any correction to the understanding under Task, with the reason. A session that grew shows it here, not silently inside What Was Accomplished.

## Writing the tail

- **Unfinished Work** — ordered, specific actions with paths, including uncommitted changes from step 2 and undocumented durable facts from *Boundaries*.
- **Files to Load Next Session** — a reason to *read* something goes here. Mark a file **untracked** if it is not committed, since another agent or a fresh clone will not have it.
- **What NOT to Re-Read** (optional) — the handoffs, plans, and analyses this session actually opened that a later reader should skip, naming what replaces each.

## Template

Sections marked *(optional)* below are left out of the file when there is nothing to say; drop the marker itself from the headings you keep.

```markdown
# Handoff: [Topic]
**Date:** [YYYY-MM-DD]
**Branch:** [branch — plus state: uncommitted changes / committed, not pushed / pushed / merged as `sha` / abandoned] or none (not a git repository)
**PR/Issue:** [PR and/or issue link or ID] or None

## Goal
- **Task:** [the job as you understood it when work began, in your own words — objective and what counts as done]
- **Delivered:** [DELIVERED/PARTIAL/BLOCKED/DROPPED] — [what is true now; for anything but DELIVERED, the gap and the Unfinished Work item that covers it] — evidence: [the check that proves it]
- **Scope changes:** [work added or abandoned after the task was set, or a correction to the Task line, and why] or None

## What Was Accomplished *(optional)*
- [task] → `[file:line]`

## Decisions Made *(optional)*
- [decision] BECAUSE [rationale] — STATUS: [confirmed/provisional]

## Key Numbers *(optional)*
- [exact test counts, timings, values — do not round]

## Open Questions *(optional)*
- [OPEN/ASSUMED item]

## Notes *(optional)*
[free-form context the next session needs that fits nowhere above]

## Unfinished Work
1. [ordered, specific action with paths] or None

## Files to Load Next Session
- `[path]` — [what it holds and when it matters; add `~L[start]-[end]` if only one region does; mark **untracked** if not committed] or None

## What NOT to Re-Read *(optional)*
- `[path]` — superseded by / already summarized in / unrelated to `[path]`
```
