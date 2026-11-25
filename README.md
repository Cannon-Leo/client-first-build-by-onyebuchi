# Client-First Build by Onyebuchi

A Client-First v2 skill that teaches Claude to build Webflow sites with strict Client-First structure, using the Webflow MCP.

> **Status: actively maintained.** The skill file is updated continuously as it is tested on real builds. Pull the latest version before each project.

## Files

| File | Purpose |
|---|---|
| `client-first.md` | Standalone skill file. Paste it into any Claude project or agent. |
| `client-first.skill` | Packaged skill (contains `SKILL.md` only). |
| `skill/client-first/SKILL.md` | Same content, unzipped. |

## What it covers

- Class naming: utility, custom, combo (`is-`) and global classes, with folder structure
- Page structure: `page-wrapper`, `main-wrapper`, `section_[name]`, `padding-global` with `padding-section` combo, `container-*`
- Element rules: no native Section or Container, use a Div with the tag changed instead
- Typography, spacing, rem units, and Finsweet fluid responsive setup
- Variables panel workflow, style guide editing, utility class protection with a triple warning
- Class reuse protocol, design doc compliance check, Webflow MCP workflow notes
- Compact reply mode by default

## Commands

| Command | Action |
|---|---|
| `/client-first start` or `/cfo start` | Clone the template, connect the MCP, snapshot default classes, set up site settings and style guide |
| `/client-first` or `/cfo` | Apply the skill to the current request |

## Requirements

- Webflow account with the Webflow MCP and Designer connected
- The Client-First v2 Webflow clone (Webflow Marketplace)

## Usage

1. Add `client-first.md` to your Claude project, or install `client-first.skill`.
2. Run `/cfo start` and follow the steps.
3. Build section by section. Corrections from each review get folded back into the skill.

## Credits

Based on the [Client-First](https://www.finsweet.com/client-first) system by Finsweet. Fluid responsive method follows the Finsweet generator.
