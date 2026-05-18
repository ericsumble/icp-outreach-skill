---
name: icp-outreach
description: |
  Generates personalized ABM outreach end-to-end: researches a recipient via Sumble, generates a full ICP brief for their company (delegating to the icp-brief skill), deploys the brief to Netlify, pulls the recipient's email via Sumble EnrichPerson, and creates a Gmail draft whose body references 2-3 specific data points from the deployed brief plus a warm-receipt line tied to the recipient's career background. Supports two input modes: a single LinkedIn URL (one recipient) or a Sumble Contact List ID (batch). Trigger phrases include "run outreach for [LinkedIn URL]", "build briefs and emails for Sumble list [N]", "ABM outreach for [contact]", "outreach pipeline for [person/list]", "do outreach to my Sumble list".
---

# ICP Outreach

Wraps the `icp-brief` skill with two more steps: pull the recipient's email, and create a Gmail draft that references concrete brief content so the link feels like the proof of research, not a pitch.

The skill always produces an **unsent Gmail draft** for human review — never auto-sends.

---

## Step 0: Detect input mode

The user will provide one of:

1. **A LinkedIn URL** (e.g. `https://www.linkedin.com/in/somebody`) → **Single mode**. Skip to Step 1.
2. **A Sumble Contact List ID or list name** (e.g. "list 42" or "outreach Q2 marketers") → **Batch mode**. Go to Step B0.

If the input is ambiguous, ask which mode using `AskUserQuestion`.

---

## SINGLE MODE — one recipient

### Step 1: Identify the recipient

Use Sumble to resolve the LinkedIn URL to a full profile. Recommended order:

1. **`WebFetch` the LinkedIn URL** to extract current company name (LinkedIn lets the public-profile preview show employer even when full bio is gated). This is the fastest, most reliable starting point.

2. **`FindPeople` at the recipient's company** with a function/level filter that should include them (e.g. for a CMO: `job_function IN ('Marketing', 'Executive') AND job_level IN ('CXO', 'EVP', 'SVP')`). Match by name in the response. This returns the Sumble person ID, exact title, start date, and location.

3. **Fallback only if Steps 1-2 fail:** `RunSqlQuery` against the `people_info` table. Important schema note: `people_info` has limited columns (`linkedin_url`, `current_title`, `connections_count`, `country_id`, `current_experience_date_from`) — **no `id`, `full_name`, `organization_id` columns**. So SQL on this table is only useful as a presence check (does Sumble know this LinkedIn URL at all), not as a profile-detail lookup. Example:
   ```sql
   SELECT linkedin_url, current_title, current_experience_date_from
   FROM people_info
   WHERE linkedin_url ILIKE '%[slug from URL]%'
   LIMIT 5
   ```

**Then enrich via WebSearch** for prior employers + notable career achievements (this is the warm-receipt material — the single most important input to the email). Good queries: `"[Full Name]" "[Current Company]" career background prior companies` or `"[Full Name]" CMO/CRO/VP career LinkedIn`. Cross-reference with RocketReach, TheOrg, Wiza, Crunchbase results — these surface what LinkedIn hides.

Capture: full name, current title, current company (name + domain + slug), tenure in role (start_date), prior employers with notable results (IPO, acquisition, revenue growth, product launch), location.

### Step 2: Build the brief via the `icp-brief` skill

Invoke the `icp-brief` skill with the recipient's company as the target. The brief is built **for the recipient's company** (i.e. the 10 best ABM-fit accounts that *their* sales team should target). Pass through:
- Recipient full name + title (used in the "Prepared for" header)
- Company name + domain
- Recipient's prior employers + role context (used to personalize the top-3 openers)

The `icp-brief` skill will:
- Research the company and define 3 plays
- Find 10 ABM-fit accounts (using account-score + job-post-count sort if the target is in healthcare/regulated/non-tech-native industry — see icp-brief Step 4 adaptive strategies)
- Build buying groups for the top 5 (US-only filtered)
- Generate the HTML at `~/icp-briefs/<slug>/index.html` where `<slug>` = `<firstname>-<lastname>-<targetcompany>-icp`
- Deploy to Netlify at `https://<slug>.netlify.app`

### Step 3: Extract brief specifics for the email

After the brief deploys, **re-read the generated HTML** to extract 2-3 concrete data points to weave into the email. Pick:
- The **headline stat** (from the hero — e.g. "1,347 active HCHB-mentioning job posts")
- **1-2 named accounts** from the top 3 (the ones with the strongest signal or most recognizable names)
- The **lead play** (Play 01's name) if it's distinctive

The email body must reference these by name. The goal: when the recipient clicks the link, they see exactly what was teased.

### Step 4: Pull the recipient's email

Call `EnrichPerson` with the recipient's Sumble person ID and `include_email: true`. Cost: 10 credits first time per person, free thereafter.

If `EnrichPerson` returns no email, **construct a fallback** in the form `firstname.lastname@<company-primary-domain>` (e.g. `maya.williams@gladly.ai`). Confirm the domain pattern from other recipients in the same batch or from the company's website — a single Sumble-enriched email at the same company is enough to confirm the convention. Common patterns: `first.last@`, `first@`, `flast@`, `firstinitiallast@`. Log the fallback in the batch summary so the user knows which drafts used a constructed address vs. a Sumble-verified one.

Edge cases:
- **Recipient has an enriched email at a different domain** than the listed company (e.g. `ali@grin.co` for someone listed as Gladly CMO). Flag for verification — they may have moved companies or the enriched address may be stale. Draft anyway if the user has pre-authorized that behavior; surface the flag in the batch summary either way.
- **Nickname vs. full first name** (e.g. "DJ" vs. "Donald John", "Andy" vs. "Andrew"). Prefer the LinkedIn display-name format since that's usually what their employer email also uses.

### Step 5: Draft the Gmail

Use `mcp__claude_ai_Gmail__create_draft` with these structural rules:

### HARD PUNCTUATION RULE — read this first

**Never use em-dashes (`—`), en-dashes (`–`), or double hyphens (`--`) in any drafted email — neither subject nor body.** These read as "AI wrote this" to a human eye and instantly destroy the credibility of the warm-receipt line.

This rule overrides every example in the rest of this skill file. If you see an em-dash in an example below, rewrite it before drafting.

**Allowed substitutes** for the dash break:
- **Comma**: "Maya, the .com to .ai migration you ran at Gladly..."
- **Period**: "Maya. The .com to .ai migration you ran at Gladly..."
- **Colon**: "One thing about the .com to .ai migration: it was a brand-as-product move..."
- **Parentheses**: "The .com to .ai migration you ran (a brand-as-product move) is exactly..."
- **Restructure the sentence** so the aside becomes a separate sentence

**Single hyphens inside compound words are fine** (`cold-call`, `ABM-ready`, `Zendesk-anchored`, `brand-as-product`, `top-of-house`, `.com-to-.ai`). The rule applies only to dashes used as sentence-break punctuation.

**Arrows (`→`) are fine** — they're not dashes and they're a deliberate visual anchor for the link line.

Before sending the draft to Gmail, scan the full subject + body for `—`, `–`, and `--`. If any appear, rewrite that sentence.

---

**Subject**: 6-9 words, references the deliverable not the sender. Examples (em-dash-free):
- `Built this for you, 10 ABM accounts ready for [product/play name]`
- `[N] [segment] operators showing live [competitor] displacement signals`
- `Q3 pipeline: 10 luxury D2C accounts ready for your AEs`

**Body**: 130-160 words, structured as:

1. **Warm-receipt line** (1 sentence) — names something specific and verifiable from the recipient's career history (prior employer + notable result, or notable move into current role). Never "Hope this finds you well" or "Welcome aboard"-style openers. Reference: user's `feedback_email_opener` memory.

2. **The Netlify URL on its own line, immediately after the warm-receipt** — formatted as `**→ https://<slug>.netlify.app**`. The link comes BEFORE the proof, not after. When the reader sees "Built this for you" they should see the link in the same eye-stroke. No scrolling, no hunting. The link IS the hook's payoff.

3. **"Three to lead with:"** — then 3 named accounts, each formatted as:
   - **Account name** (1 specific signal in parentheses).
   - "Contact **[Full Name, Title]**."
   - 1 sentence explaining WHY this specific person is the right entry. The WHY should tie to a concrete attribute: new in role (typically <12 months = high buying window), recent move, owns a stated mandate, named in the job posting, or sits at the intersection of the buying signal and a verified function.
   
   Do not just name accounts and data points. Always pair each account with one specific named contact + the reason that person (not the account in general) is the right entry. If a top-5 buying group is too noisy to name a confident individual (e.g. a global IDN with hundreds of facility-level CXOs), name a specific role/title or team that's actively hiring instead ("Their Senior Manager, UC&C in Miramar FL is the IT Ops entry"). Never leave the contact step ambiguous.

4. **Low-friction CTA** (1 sentence) — a sortable yes/no, not a calendar push. Examples: "Worth a chat on which to pursue first?" / "Useful for [team they own]?" / "Want the per-account model?"

**Why this order**: the warm-receipt earns 3-5 seconds of attention. Spending that attention on the link maximizes click-through. The "Three to lead with" structure delivers the synthesis in the email itself, so even a recipient who never clicks walks away with three named, justified contacts. That is the deliverable. The full brief at the URL is the supporting evidence; the email body IS the playbook.

Example structure (no em-dashes, no double hyphens, no en-dashes):

> Andy, your run at Cisco leading $5.5B in Collaboration and Contact Center GTM means you already know what these 10 accounts look like from the inside out.
>
> **→ https://andy-dignan-five9-icp.netlify.app**
>
> Three to lead with:
>
> **Visa** (297 Genesys-anchored job posts across 77 teams). Contact **Aaron Bearce, SVP and Global Head of Procurement**. 8 months in the seat, and year one for a new CPO is when vendor spend gets consolidated.
>
> **Humana** (Senior Cloud Solutions Engineer Contact Center role tagged "Cloud Cost Optimization, Generative AI, Cloud Migration"). Contact **David McGettigan, SVP and COO Technology**. 4 months in the role, no legacy platform commitments, owns the cost-optimization mandate.
>
> **Royal Caribbean** (Call Center Operations Analyst job requires BOTH Avaya AND Genesys today). Their **Senior Manager, Unified Communication and Collaboration** in Miramar FL is the IT Ops entry. Parallel-stack consolidation, the textbook Cisco-era pattern you ran for years.
>
> Worth a chat on which to pursue first?

Set `replyToMessageId` to null (cold draft, not a reply). No CC/BCC unless the user has specified one in their memory (check for things like Salesforce BCC drop address).

### Step 6: Report

Return one tight summary to the user:
- Recipient name + title + company
- Live URL
- Gmail draft ID + subject line (so they can find it in Gmail)
- Sumble credits used this run

---

## BATCH MODE — Sumble Contact List

### Step B0: Read the list

Call `mcp__claude_ai_Eric_Sumble_com__GetContactList` with the list ID (or first `ListContactLists` to find by name). Extract every contact's: person ID, full name, title, company, LinkedIn URL.

### Step B1: Preview and confirm

Before any per-recipient work, show the user:
- **Total recipient count** and the full list of names + companies
- **Same-company detection**: If all (or most) recipients share an organization, flag it. Same-company batches can amortize the ICP research (one shared set of 10 accounts, 9 personalized variants) instead of running independent research per recipient. Default to **shared research** unless the user requests fully independent runs.
- **Pre-enriched emails**: count how many already have `contact_info.emails` populated in the list (vs. how many will need EnrichPerson or constructed fallbacks). Surface any email-domain anomalies (e.g. a CMO listed at Company A but enriched with `@company-b.com`) so the user can decide before drafting.
- **Estimated Sumble credits**:
  - Per-recipient (different-company batch): ≈ 500-800
  - Same-company batch (shared research): ≈ 500-800 for the shared portion + 10-20 per recipient for email enrichment + per-recipient research
- **Estimated Netlify deploys** (1 per recipient)
- **Estimated runtime** (≈ 3-5 min per recipient sequential, or ≈ 15-25 min total for a 9-recipient same-company batch with parallelization)

Ask explicit "go / cancel" via `AskUserQuestion`. Do not start until confirmed.

### Step B1.5: Shared-origin cluster detection (after research, before personalization)

Once recipient career-history research is complete, scan for **shared prior employers across ≥2 recipients**. Example: in one Gladly run, three recipients (CMO Ali Fazal, Sr Dir Demand Gen Jennifer Baker, Director RevOps Jenny Baumgartner) were all GRIN alumni — strong signal of either a GRIN acquisition or a coordinated talent move.

When a cluster is detected:
- Surface it in the user-facing progress log: `🔗 GRIN alumni cluster: Ali Fazal, Jennifer Baker, Jenny Baumgartner`
- Treat the shared origin as a **secondary warm-receipt opportunity** for any of those recipients (the email can reference the cluster, not just the individual: "your GRIN demand-gen run").
- Consider whether the cluster pattern itself is sales intelligence worth surfacing back to the user (e.g. "Gladly seems to have absorbed GRIN talent").

### Step B2: Hybrid execution — calibrate, then auto

After "go":

1. **Run the first recipient end-to-end** (Steps 1-5 of Single mode). Show the user:
   - Brief preview (live URL)
   - Email draft (full body)
   - Confirm: continue with this format, edit format, or stop
2. **Run the second recipient end-to-end** with any format adjustments. Same confirmation gate.
3. After two consecutive confirmations, **switch to auto**: process all remaining recipients sequentially without per-recipient confirmation. Log progress as you go (one line per recipient: "✅ [Name] — [URL] — draft [ID]" or "⚠️ [Name] — skipped: [reason]").

#### Same-company shared-research execution pattern

When all recipients are at the same company:

1. **Build the first recipient's brief in full** (the calibration recipient sets the template — full HTML with 10 accounts, 3 plays, top-5 buying groups). This becomes the **master template**.
2. **For each subsequent recipient**: `cp` the master HTML to their `~/icp-briefs/<slug>/` directory, then personalize **only three things**: the `hero-eye` (eyebrow), the `hero-h` + `hero-sub` (headline + sub, framed per role), and the footer "Prepared for" line. Use a Python script via Bash for batch substitution rather than many individual Edit calls.
3. **Parallelize aggressively across recipients**: Netlify URLs are deterministic from the slug, so Gmail drafts can be created BEFORE deploys finish. Recommended Round-1 batch: clone all HTMLs + create all Netlify projects + draft all Gmails in a single tool-call wave. Round 2: personalize all HTMLs via script. Round 3: deploy all in parallel. Round 4: single polling loop checking all URLs.

#### Role-based hero framing (apply per recipient)

The `hero-h` (headline) and `hero-sub` (subhead) must match the recipient's role:

| Role family | Hero framing | Example hero-h |
|---|---|---|
| Marketing / CMO / PMM / Demand Gen | "What your sales team should target" — recipient is the engine of pipeline | "10 ABM-ready luxury D2C accounts." / "AI-native CX, ready to land." |
| Sales / CRO / AE / Strategic Accounts | "Your next 10 quota-bearing accounts" — recipient is the closer | "Your next 10 quota-bearing accounts." / "All hiring against [competitor] right now." |
| RevOps | "Pre-scored for AE territory routing" — recipient is the orchestrator | "10 ranked accounts. Pre-scored for AE routing." |
| Partnerships / BD | "Flagged for partner-channel potential" — recipient is the channel | "10 accounts. Partner-channel motions flagged." |
| C-suite (CEO/COO/President) | Strategic framing — recipient is the air cover | "10 accounts. Q3 pipeline at a glance." |

The 10 accounts themselves don't change — the framing of *what to do with them* does.

### Step B3: Failure handling

For each recipient, on failure:
- **Skip-and-log, never halt the batch.** Failure modes to handle gracefully:
  - Recipient not found in Sumble (no person ID, no email) → log "no Sumble match"
  - Company can't be profiled (Sumble returns no org match) → log "no org data"
  - EnrichPerson returns no email → still produce the brief + Netlify URL, log "no email — manual send required"
  - Netlify deploy fails → retry once with `-2` suffix on slug; if still failing, log and continue
  - Gmail API rate limit → wait 30s, retry once

### Step B4: End-of-batch summary

After the batch finishes, return a single table:

| # | Recipient | Company | Live URL | Gmail Draft | Status |
|---|-----------|---------|----------|-------------|--------|
| 1 | Jane Smith | HubSpot | jane-smith-hubspot-icp.netlify.app | Draft abc123 | ✅ |
| 2 | Bob Lee | Drift | bob-lee-drift-icp.netlify.app | (no email) | ⚠️ manual send |

Plus totals: N succeeded, N skipped, total Sumble credits used, total runtime.

---

## Quality Checklist (per recipient)

Before marking a recipient complete:

- [ ] Recipient identified in Sumble (person ID captured, US-located if possible)
- [ ] Brief generated and Netlify URL returns HTTP 200
- [ ] Email body references the recipient's career history in line 1 (warm-receipt, not template)
- [ ] Email body names 2-3 specific accounts/findings from the brief (not generic)
- [ ] Netlify URL appears on its own line in the email, visually prominent
- [ ] CTA is a low-friction sortable yes/no, not a calendar ask
- [ ] Gmail draft created (or "no email" logged with reason)
- [ ] Live URL reported back to the user

---

## Defaults baked in (push back if any feel wrong for a given run)

- **Never auto-send** — always Gmail draft for human review
- **Skip-and-log on failure** — batch never halts on one failure
- **Per-recipient unique brief** — even two people at the same company get separate briefs and separate URLs (Sumble credits are not the constraint; personalization is the point)
- **Sequential across recipients, parallel within a brief** — keeps each brief fast (parallel Sumble queries) without overwhelming any API with concurrent deploys
- **Idempotent** — same recipient re-run = same slug = updates existing Netlify project in place
- **US-only people filter** when building buying groups — Sumble's domain-based person matching pulls international noise on common names

---

## Skill heuristics (learned from initial runs)

These are the patterns that make the skill work — apply them by default:

1. **Email line 1 must be specific and verifiable.** "Your OneSpot ABM run is what made me build this" beats "Hi Natalie, I came across your profile" by an order of magnitude. The line 1 hook comes from the recipient's prior-employer research, not their current title.

2. **The email is a trailer, the brief is the movie.** Every claim in the email body must be visible in the deployed brief within 5 seconds of clicking through. If the email name-drops Elara Caring with "1,347 HCHB-mentioning posts," account #1 in the brief must show exactly that number.

3. **CTA conditional on the recipient's own pipeline.** "Worth a chat if any of the 10 are already on your radar?" forces a binary mental sort and feels low-friction. "Got 15 minutes Thursday?" is a calendar ask and gets ignored.

4. **For marketing recipients specifically**, frame the brief as "what your sales team should target" — they value being the engine of pipeline, not the consumer. For sales recipients, frame as "your next 10 quota-bearing accounts."

5. **For recipients with ABM/marketing backgrounds**, they will judge the brief on craft. The opener should acknowledge they'll recognize good ABM work when they see it (no need to explain what ABM is).

6. **Insider-knowledge hooks beat tenure hooks.** If the recipient previously worked at a *competitor named in the brief* (e.g. Steve West was a Kustomer PMM and the brief targets Kustomer displacement), lead with that — "your Kustomer PMM run puts you on the right side of this play" is stronger than any current-role framing. Cross-reference each recipient's prior employers against the brief's `Displace` column when drafting line 1.

7. **Constructed-email fallbacks should be transparent.** When using a guessed `firstname.lastname@company.tld` (vs. Sumble-enriched), surface it in the per-recipient log and end-of-batch summary so the user knows which drafts need extra verification before sending. Don't silently hide the guess.

8. **Same-company batches reveal talent clusters.** Multiple recipients sharing a prior employer (e.g. three GRIN alumni in a Gladly batch) is a strong signal of an acquisition or coordinated talent move. Surface this back to the user, it's often sales intelligence in its own right.

9. **Em-dashes and double hyphens are banned in email content.** This is the single hardest rule in the skill. Recipients pattern-match on `—` and `--` as "AI sent this" within the first half-second of reading. The warm-receipt line is the only thing earning the click, and a dash kills it. Pre-send scan: grep the draft body and subject for `—`, `–`, `--`. If any match, rewrite the sentence using a comma, period, colon, parens, or restructure. The earlier sections of this file are the source of truth; this heuristic is the backstop. (See "HARD PUNCTUATION RULE" at the top of Step 5.)

10. **Named contact + WHY beats account-only data.** The email body's job is not to show off how much Sumble data you found. It's to deliver the synthesis: which 3 accounts, who specifically to call at each, and why that person (not the org in general). The brief at the URL is the deep evidence; the email IS the playbook. When picking the named contact, prefer in this order:
    1. **New-in-role buyers** (less than 12 months in seat, especially less than 6) — they're rebuilding stack, no legacy commitments
    2. **Owners of an explicitly stated mandate** in their title or recent announcement (Cost Optimization, Digital Transformation, AI, Cloud Migration, etc.)
    3. **Long-tenure operators** at orgs where decision velocity matters (a 10-year operator = the actual decision-maker, not the new CIO who's still onboarding)
    4. **Cross-functional roles** that bridge the buying signal and a verified function (a "Chief Digital and Information Officer" is more actionable than "CIO + CDO listed separately")
    
    If the top-5 buying-group data is too noisy to name an individual with confidence (typical for global IDNs with hundreds of facility-level CXOs surfacing first), name a specific role/team/location that's actively hiring instead. Never leave the contact step ambiguous with phrases like "the right person at X" or "someone in their CX org."

11. **Selecting the "three to lead with" set is curation, not ranking.** Pick the three accounts where the contact + WHY story is sharpest, not just the top 3 by Sumble score. A score-26 account with a brand-new CIO and a stated cost-optimization mandate beats a score-41 account with a noisy global buying group. The brief still surfaces all 10 ranked; the email surfaces the 3 the recipient should act on first.

---

## Reference Files

- `~/.claude/skills/icp-brief/SKILL.md` — the brief generation + Netlify deploy skill that this skill delegates to
- `~/.claude/skills/icp-brief/references/html-template.md` — HTML/CSS used by the brief
