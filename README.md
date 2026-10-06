# Chartworks skills

Agent skills for [Chartworks](https://chartworks.dev) — chart rendering for AI agents (Claude Code, Cursor, Codex, Copilot, Gemini CLI, …).

## What it does

Teaches the host agent to use the `chartworks` CLI through a two-step flow: fetch one guide for every chart type it needs, then render. The full skill content is in [skills/chartworks/SKILL.md](https://github.com/chartworks/skills/blob/main/skills/chartworks/SKILL.md).

## Install

```bash
npx skills add chartworks/skills --skill chartworks -g
```

It asks which agent to install for; add `-a claude-code` (or `cursor`, `codex`, `gemini-cli`) to skip the question. Update later with `npx skills update chartworks`.

Or use the GitHub CLI: `gh skill install chartworks/skills chartworks --scope user`. Or copy [SKILL.md](https://github.com/chartworks/skills/blob/main/skills/chartworks/SKILL.md) into the directory your agent watches for skills.

Prefer MCP? Point your agent host at `https://mcp.chartworks.dev/mcp` instead. Chat apps like claude.ai and ChatGPT need it — their code sandbox can't reach chartworks.dev, so the skill won't work there. Per-host steps: [chartworks.dev/docs/connect](https://chartworks.dev/docs/connect).

## Use

Once installed, the agent will reach for these commands when asked to render a chart:

```bash
npx -y chartworks@latest guide line column table      # rules + guides for every type, one call
npx -y chartworks@latest render --spec spec.json      # render to PNG / SVG
```

## Issues

[github.com/chartworks/issues](https://github.com/chartworks/issues)

## License

MIT
