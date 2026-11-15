# skills

A collection of [Agent Skills](https://agentskills.io) (one `SKILL.md` per folder) for everyday development workflows. They ship as a plugin for Claude Code and Codex, and work with any other agent that supports skills.

## Skills

| Skill | What it does |
| --- | --- |
| [`handoff`](skills/handoff/SKILL.md) | Writes a session handoff file to `.handoffs/` at the project root and archives earlier ones to `.handoffs/archive/`, so the next session can pick up the work without the transcript. Run it yourself with `/handoff` (Claude won't trigger it on its own). |

## Install

### Any agent: `npx skills`

The [`skills`](https://www.npmjs.com/package/skills) CLI installs skills into Claude Code, Codex, Cursor, and the other agents it supports:

```bash
npx skills add vasiliadi/skills
```

It asks which skills and which agents to install to. Common variants:

```bash
# See what's in the repo without installing anything
npx skills add vasiliadi/skills --list

# Install one skill, for your user (all projects) instead of the current project
npx skills add vasiliadi/skills --skill handoff --global

# Install every skill into every detected agent, no prompts
npx skills add vasiliadi/skills --all

# Update later
npx skills update
```

### Claude Code

Install it as a plugin from this repo's marketplace:

```bash
/plugin marketplace add vasiliadi/skills
/plugin install vasiliadi-skills@vasiliadi
```

On Claude Code 2.1.275 or later, a single command does both:

```bash
/plugin install vasiliadi-skills --marketplace vasiliadi/skills
```

To get new versions later, run `/plugin marketplace update vasiliadi`.

In Claude Desktop, open **Customize** → **Personal plugin** → **+ Create plugin** → **Add marketplace**, enter `vasiliadi/skills`, then install `vasiliadi-skills`.

### Codex

```bash
codex plugin marketplace add vasiliadi/skills
```

```bash
codex plugin add vasiliadi-skills@vasiliadi
```

To get new versions later, run `codex plugin marketplace upgrade vasiliadi`.

### Other agents

Harnesses that read the generic plugin marketplace at [`.agents/plugins/marketplace.json`](.agents/plugins/marketplace.json) can add this repo as a marketplace and install `vasiliadi-skills`. Otherwise, copy the folders from [`skills/`](skills/) into your agent's skills directory.

## Repository layout

```
.claude-plugin/
  marketplace.json   # Claude Code marketplace (lists the plugin)
  plugin.json        # Claude Code plugin manifest
.codex-plugin/
  plugin.json        # Codex plugin manifest
.agents/plugins/
  marketplace.json   # Generic marketplace (Codex and other harnesses)
skills/
  <name>/SKILL.md    # One folder per skill
```
