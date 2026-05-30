# Tier 3 — pnpm Workspace (Monorepo)

**Shape:** Multiple packages in one repo, sharing a common lockfile.  
**User runs it with:** `pnpm --filter skill-a node src/main.js`

---

## Directory Structure

```
skills-repo/
├── package.json            ← workspace root (no code here)
├── pnpm-workspace.yaml     ← declares workspace members
├── pnpm-lock.yaml          ← single lockfile for all packages
├── shared/                 ← internal library
│   ├── package.json
│   └── src/
│       └── index.js
├── skill-a/
│   ├── package.json
│   └── src/
│       └── main.js
└── skill-b/
    ├── package.json
    └── src/
        └── main.js
```

---

## Template: `pnpm-workspace.yaml`

```yaml
packages:
  - "shared"
  - "skill-a"
  - "skill-b"
```

---

## Template: Workspace Root `package.json`

```json
{
  "name": "skills-repo",
  "private": true
}
```

---

## Template: `shared/package.json`

```json
{
  "name": "@skills/shared",
  "version": "0.1.0",
  "private": true,
  "type": "module",
  "exports": {
    ".": "./src/index.js"
  },
  "dependencies": {
    "chalk": "^5.3.0"
  }
}
```

---

## Template: `shared/src/index.js`

```javascript
import chalk from "chalk";

export function logOk(msg) {
  console.log(chalk.green("✓"), msg);
}

export function logErr(msg) {
  console.error(chalk.red("✗"), msg);
}
```

---

## Template: `skill-a/package.json`

```json
{
  "name": "@skills/skill-a",
  "version": "0.1.0",
  "private": true,
  "type": "module",
  "scripts": {
    "start": "node src/main.js"
  },
  "dependencies": {
    "@skills/shared": "workspace:*"
  }
}
```

---

## Template: `skill-a/src/main.js`

```javascript
/**
 * Skill A — does X.
 *
 * Usage:
 *   pnpm --filter skill-a start -- --name hello
 */

import { parseArgs } from "node:util";
import { logErr, logOk } from "@skills/shared";

const { values } = parseArgs({
  options: {
    name: { type: "string" },
  },
});

if (!values.name) {
  logErr("--name is required");
  process.exit(1);
}

async function main() {
  try {
    logOk(`Done: ${values.name}`);
  } catch (err) {
    logErr(err instanceof Error ? err.message : String(err));
    process.exit(1);
  }
}

main();
```

---

## How to Set Up (once, by skill author)

```bash
cd skills-repo/
pnpm install
git add pnpm-lock.yaml
```

## How Users Run It

```bash
# Run a skill
pnpm --filter skill-a start -- --name hello

# Or directly
pnpm --filter skill-a node src/main.js --name hello

# Run all tests (if using vitest)
pnpm vitest run
```

---

## Adding a New Skill

1. Copy `skill-a/` → `skill-c/`, rename `package.json` name accordingly
2. Add `"skill-c"` to `pnpm-workspace.yaml`
3. Run `pnpm install`

---

## Common Pitfalls

| Problem | Fix |
|---------|-----|
| `Cannot find module '@skills/shared'` | Add `"@skills/shared": "workspace:*"` to deps, re-run `pnpm install` |
| New package not picked up | Add to `pnpm-workspace.yaml` and re-run `pnpm install` |
| Circular deps | Extract common code into `shared` |
| `SyntaxError: Cannot use import` | Add `"type": "module"` to the package's `package.json` |
