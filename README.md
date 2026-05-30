# javascript-skill-template

A tiered template for packaging JavaScript skills with [`pnpm`](https://pnpm.io) and Node.js.

## Tiers

| Tier | Shape | When to Use |
|------|-------|-------------|
| **1** | Single `.js` file + `package.json` | Self-contained, no shared code |
| **2** | Flat folder + shared `lib/` | Few scripts sharing utilities |
| **3** | `pnpm` workspace monorepo | Many skills, CLI tools, deep sharing |

## Prerequisites

```bash
npm install -g pnpm
```

## Usage

See [`SKILL.md`](SKILL.md) for the decision guide, then the relevant file under [`references/`](references/).
