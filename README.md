# chartworks-skill

Agent skill for [Chartworks](https://chartworks.dev) — chart rendering for AI agents (Claude Code, Cursor, Codex, Copilot, Gemini CLI, …).

## What it does

Teaches the host agent to use the `chartworks` CLI through a two-step flow: fetch one guide for every chart type it needs, then render. The full skill content is in [SKILL.md](./SKILL.md).

## Install

Use your agent's skill mechanism, e.g.:

```bash
gh skill install chartworks/skill
```

Or copy [SKILL.md](./SKILL.md) into the directory your agent watches for skills.

## Use

Once installed, the agent will reach for these commands when asked to render a chart:

```bash
npx chartworks guide line column table      # rules + guides for every type, one call
npx chartworks render --spec spec.json      # render to PNG / SVG
```

## Issues

[github.com/chartworks/issues](https://github.com/chartworks/issues)
