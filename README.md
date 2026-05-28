# Claude Code Skills

A personal collection of [Claude Code](https://claude.com/claude-code) skills by JC Grubbs,
packaged as a plugin so they're available in every session, on any project.

## Skills

| Skill | Invoke | What it does |
|-------|--------|--------------|
| `thermo-nuclear-code-quality-review` | `/skills:thermo-nuclear-code-quality-review` | An unusually strict maintainability review — abstraction quality, runaway file size, spaghetti conditionals, and ambitious "code judo" restructurings. User-invoked only. |

## Install

This repo is a Claude Code plugin *and* its own local marketplace. From any Claude Code session:

```
/plugin marketplace add /Users/jcgrubbs/dev/skills
/plugin install skills@jc-skills
```

Restart Claude Code, then run a skill by its namespaced command, e.g. `/skills:thermo-nuclear-code-quality-review`.

> Pushed this repo to GitHub? Install it the same way on any machine with `/plugin marketplace add <github-repo>`.

## Adding a skill

```
mkdir skills/<new-skill-name>
$EDITOR skills/<new-skill-name>/SKILL.md
```

Each `SKILL.md` needs YAML frontmatter:

```markdown
---
name: <new-skill-name>            # kebab-case; must match the folder name
description: What it does, plus the phrases that should trigger it.
# disable-model-invocation: true  # optional: user-invoked only, never auto-triggered
---

# Skill instructions…
```

Skills are auto-discovered — no manifest edits required. Restart Claude Code to pick up new or
edited skills (run `/plugin marketplace update jc-skills` first if a new one doesn't show up).

## Layout

```
.claude-plugin/
  plugin.json       # the "skills" plugin
  marketplace.json  # the "jc-skills" marketplace (serves this repo as that plugin)
skills/
  <skill-name>/SKILL.md
```
