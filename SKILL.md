---
name: javascript-skill-template
description: >
  Guide for packaging JavaScript-based skills with proper dependency management using pnpm.
  Use this skill whenever someone is creating or structuring a JavaScript skill, script, or tool
  that needs dependencies — especially in multi-skill or shared-code scenarios.
  Covers single-file scripts all the way to multi-package workspace projects.
---

# JavaScript Skill Packaging Guide

This skill helps you structure JavaScript skills correctly based on complexity.
Three tiers are supported — pick the right one and follow its reference file.

## Tiers at a Glance

| Tier | Shape | When to Use |
|------|-------|-------------|
| **1 — Single Script** | One `.js` file + `package.json` | Self-contained tool, no shared code |
| **2 — Multi-Script** | Flat folder of scripts + shared `lib/` | A few scripts that share utilities |
| **3 — Workspace** | `pnpm` monorepo with packages | Many skills, deep shared code, CLI tools |

## Step 1: Classify the Skill

Ask:
1. Is it one script with no imports from other local files? → **Tier 1**
2. Does it call other scripts, or share code with 2–5 other scripts? → **Tier 2**
3. Is it part of a larger repo with many skills, needs installable CLIs, or has complex shared code? → **Tier 3**

## Step 2: Read the Reference File

- Tier 1 → `references/tier1-single-script.md`
- Tier 2 → `references/tier2-multi-script.md`
- Tier 3 → `references/tier3-workspace.md`

## Step 3: Apply the Template

Each reference file contains:
- Canonical directory structure
- Annotated template files (copy-paste ready)
- Common pitfalls
- How users run the skill

## Prerequisites (for all tiers)

Users need `pnpm` installed once:
```bash
npm install -g pnpm
```

That's the only global install needed. Everything else is handled automatically.
