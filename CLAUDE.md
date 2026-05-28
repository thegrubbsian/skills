# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

A personal collection of Claude Code **skills**, packaged so they install globally. The repo is
*both* a plugin and the local marketplace that serves it. There is no application code, build
system, or test suite — each skill is a Markdown prompt (`SKILL.md`), not executable code.

## Architecture

Three pieces work together; understand the wiring before changing any of them:

- `.claude-plugin/plugin.json` — defines a single plugin named `skills`. Claude Code auto-discovers
  every `skills/<name>/SKILL.md` under the plugin root, **one level deep**. The manifest does *not*
  list individual skills.
- `.claude-plugin/marketplace.json` — defines a marketplace named `jc-skills` whose one plugin entry
  has `"source": "./"` — i.e. the plugin *is* this repo's root. So the repo is simultaneously the
  marketplace and the single plugin it serves.
- `skills/<name>/SKILL.md` — one directory per skill. Skills are namespaced by the plugin, so they
  are invoked as `/skills:<name>` (e.g. `/skills:thermo-nuclear-code-quality-review`), never `/<name>`.

Consumers install it with `/plugin marketplace add <repo-path>` then `/plugin install skills@jc-skills`.

## SKILL.md conventions

Each skill is a folder under `skills/` containing a `SKILL.md` with YAML frontmatter:

- `name` — kebab-case; must match the folder name.
- `description` — what the skill does **and** the trigger phrases that should activate it. The model
  matches user intent against this text, so front-load the phrases that should fire it.
- `disable-model-invocation: true` — optional; makes the skill user-invoked only (via its slash
  command), never auto-triggered by the model. The existing `thermo-nuclear-code-quality-review`
  skill uses this.

## Working in this repo

- **New skill:** create `skills/<new-name>/SKILL.md`. No manifest edits needed — it's auto-discovered.
  Folder name and frontmatter `name` must match.
- **Take effect:** new or edited skills only load after a Claude Code **restart**. If a new skill
  doesn't appear, run `/plugin marketplace update jc-skills` first, then restart.
- **After editing `.claude-plugin/*.json`:** confirm it parses, e.g.
  `python3 -m json.tool .claude-plugin/marketplace.json`.

There is no lint/build/test step. Verification is "does the skill load and behave correctly on restart."
