# ICP Outreach — Claude Code Skills

Two composable Claude Code skills for end-to-end ABM outreach powered by Sumble account intelligence:

- **`icp-brief`** — Generates a personalized "Top 10 ABM Accounts" intelligence brief (full HTML, branded, with buying groups + plays + draft openers per account), then deploys it to Netlify as a per-recipient public site.
- **`icp-outreach`** — Wraps `icp-brief`. Takes a LinkedIn URL (single) or Sumble Contact List (batch), generates the brief, deploys it, and creates a Gmail draft that references the brief content with a warm-receipt line tied to the recipient's career history.

## Workflow

```
LinkedIn URL or Sumble List
         ↓
  icp-outreach
         ↓
  ┌──────────────┐
  │  icp-brief   │  → 10 ABM-fit accounts + buying groups + plays
  └──────────────┘
         ↓
  Deploy to Netlify (per-recipient URL)
         ↓
  Gmail draft (warm-receipt + brief link + CTA)
         ↓
  Human review → send
```

The skill always produces an **unsent Gmail draft** — never auto-sends.

## Install

Clone or copy the two skill folders into `~/.claude/skills/`:

```bash
git clone https://github.com/ericsumble/icp-outreach-skill.git
cp -r icp-outreach-skill/icp-brief ~/.claude/skills/
cp -r icp-outreach-skill/icp-outreach ~/.claude/skills/
```

Claude Code will pick them up on next startup.

## Dependencies

This skill chain relies on three MCP servers being connected:

- **Sumble** (`claude.ai_Eric@Sumble.com`) — account intelligence (orgs, people, jobs, technologies, contact lists)
- **Netlify** — deploy the per-recipient HTML as `<slug>.netlify.app`
- **Gmail** — create draft messages

And the Netlify CLI installed + authenticated locally:

```bash
npm i -g netlify-cli && netlify login
```

## Usage

**Single recipient:**
> Run icp-outreach for https://www.linkedin.com/in/somebody

**Batch from a Sumble list:**
> Build briefs and emails for Sumble list 4504

The skill will preview the plan, ask for confirmation, run the first 1–2 recipients as calibration, then auto-run the remainder with an end-of-batch summary.

## What's in each skill

```
icp-brief/
  SKILL.md                       # Brief generation + Netlify deploy steps
  references/
    html-template.md             # Full CSS + structural HTML for the brief

icp-outreach/
  SKILL.md                       # Recipient identification + Gmail draft wrapper
```

## License

MIT — use, modify, fork as you like.
