# Tier 2 — Multi-Script with Shared Library

**Shape:** A folder of scripts that share a local utility module.  
**User runs it with:** `node script_a.js`

---

## Directory Structure

```
my-skill/
├── package.json        ← project def + shared deps
├── pnpm-lock.yaml      ← committed lockfile (reproducible installs)
├── lib/
│   └── utils.js        ← shared code imported by all scripts
├── script_a.js         ← entry point A
├── script_b.js         ← entry point B
└── README.md
```

---

## Template: `package.json`

```json
{
  "name": "my-skill",
  "version": "0.1.0",
  "private": true,
  "type": "module",
  "scripts": {
    "script-a": "node script_a.js",
    "script-b": "node script_b.js"
  },
  "dependencies": {
    "chalk": "^5.3.0"
  }
}
```

---

## Template: `lib/utils.js`

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

## Template: `script_a.js`

```javascript
#!/usr/bin/env node
/**
 * Script A — does X.
 *
 * Usage:
 *   node script_a.js --name hello
 */

import { parseArgs } from "node:util";
import { logErr, logOk } from "./lib/utils.js";

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

> **Note:** Import with `.js` extension — this is required for Node's ESM resolver.

---

## Script calling Script

### Pattern A — Function import (preferred)
```javascript
import { main as runA } from "./script_a.js";
await runA();
```

### Pattern B — Subprocess
```javascript
import { spawnSync } from "node:child_process";

const result = spawnSync("node", ["script_a.js", "--name", "foo"], {
  stdio: "inherit",
});
if (result.status !== 0) process.exit(result.status ?? 1);
```

---

## How to Set Up (once, by skill author)

```bash
cd my-skill/
pnpm install
git add pnpm-lock.yaml
```

## How Users Run It

```bash
cd my-skill/
pnpm install
node script_a.js --name hello
# or via npm script:
pnpm run script-a -- --name hello
```

---

## Common Pitfalls

| Problem | Fix |
|---------|-----|
| `Cannot find module './lib/utils.js'` | Use `.js` extension in imports |
| `SyntaxError: Cannot use import` | Add `"type": "module"` to `package.json` |
| Scripts have conflicting deps | They share one lockfile — if conflict, use Tier 3 |
| `pnpm-lock.yaml` not committed | Always commit it for reproducible installs |
