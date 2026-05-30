# Tier 1 — Single Script

**Shape:** One `.js` file + a minimal `package.json` for dependencies.  
**User runs it with:** `node skill.js`

---

## Directory Structure

```
my-skill/
├── skill.js        ← the entire skill
├── package.json    ← deps declared here
└── README.md       ← optional, describe what it does
```

No build step. Node runs the file directly.

---

## Template: `package.json`

```json
{
  "name": "my-skill",
  "version": "0.1.0",
  "private": true,
  "type": "module",
  "scripts": {
    "start": "node skill.js"
  },
  "dependencies": {
    "chalk": "^5.3.0"
  }
}
```

---

## Template: `skill.js`

```javascript
#!/usr/bin/env node
/**
 * One-line description of what this skill does.
 *
 * Usage:
 *   node skill.js --url https://example.com
 *   ./skill.js --url https://example.com   # after chmod +x
 */

import { parseArgs } from "node:util";
import chalk from "chalk";

const { values } = parseArgs({
  options: {
    url: { type: "string" },
  },
});

if (!values.url) {
  console.error(chalk.red("Error: --url is required"));
  process.exit(1);
}

async function main() {
  try {
    const res = await fetch(values.url);
    if (!res.ok) throw new Error(`HTTP ${res.status}`);
    console.log(chalk.green("OK"), res.status);
  } catch (err) {
    console.error(chalk.red("Error:"), err instanceof Error ? err.message : err);
    process.exit(1);
  }
}

main();
```

---

## Key Rules

1. **`"type": "module"` in `package.json`** enables ES module imports (`import`/`export`).  
   Drop it if you need CommonJS (`require`).

2. **Use `parseArgs` from `node:util`** for CLI flags — it's built-in since Node 18, no extra dep needed.

3. **Prefer `chalk` (v5+) over `console.log` color codes** for readable output.

4. **Exit with `process.exit(1)`** on failure so callers can detect errors.

---

## How Users Run It

```bash
# First time: install deps (~2s)
pnpm install

# Run the script
node skill.js --url https://example.com

# Via npm script
pnpm start -- --url https://example.com

# Make it directly executable (Unix)
chmod +x skill.js
./skill.js --url https://example.com
```

---

## Common Pitfalls

| Problem | Fix |
|---------|-----|
| `Cannot find module 'chalk'` | Run `pnpm install` first |
| `SyntaxError: Cannot use import` | Add `"type": "module"` to `package.json` |
| Want to call another script | Use `import` directly — or consider Tier 2 |
| Need a `.env` file | Add `dotenv` as a dependency, call `config()` at the top |
