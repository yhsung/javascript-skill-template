# Quick Reference: pnpm + Node.js Cheatsheet

## Install tools (once per machine)
```bash
npm install -g pnpm
```

## Tier 1 — Single Script
```bash
pnpm install                  # setup (first time)
node skill.js                 # run
pnpm start -- --flag value    # run via npm script
```

## Tier 2 — Multi-Script Project
```bash
pnpm install                  # setup (first time)
node script_a.js              # run a script
pnpm run script-a             # run via npm script alias
```

## Tier 3 — Workspace
```bash
pnpm install                              # setup (first time)
pnpm --filter skill-a start              # run a package's start script
pnpm --filter skill-a node src/main.js  # run directly
pnpm vitest run                          # run all tests
pnpm --filter skill-a add lodash         # add dep to one package
pnpm add -D vitest -w                    # add workspace dev dep
```

## Dep Format (package.json)
```json
"chalk": "*"            // any version
"chalk": "^5.3.0"       // compatible with 5.3.0
"chalk": ">=5.0.0 <6"   // range
"@skills/shared": "workspace:*"   // local workspace package
```
