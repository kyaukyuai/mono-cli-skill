# mono-cli-skill

[Vercel Agent Skill](https://vercel.com/docs/agent-resources/skills) for [mono CLI](https://www.npmjs.com/package/@kyaukyuai/mono-cli) — a growth platform for indie developers.

## Install

```bash
npx skills add kyaukyuai/mono-cli-skill
```

## What is this?

This skill teaches AI agents (Claude Code, Cursor, GitHub Copilot, etc.) how to use the mono CLI to manage works, articles, questions, profiles, and image uploads.

## Prerequisites

```bash
npm install -g @kyaukyuai/mono-cli
```

Create a Personal Access Token at your mono dashboard (Settings → API Tokens), then:

```bash
mono auth login --pat <TOKEN>
```

## Links

- [mono](https://www.mono.style) — the platform
- [@kyaukyuai/mono-cli](https://www.npmjs.com/package/@kyaukyuai/mono-cli) — npm package
- [SKILL.md](./SKILL.md) — full skill specification
