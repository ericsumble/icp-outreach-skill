---
name: icp-brief
description: |
  Generates a personalized ICP Account Intelligence Brief — a polished HTML document showing a company's top 10 target accounts with live Sumble-powered signals, three strategic plays, buying groups, and personalized pitch openers. Trigger this skill whenever the user wants to create a prospecting brief for a company, find ICP-fit target accounts using Sumble data, generate a "top 10 accounts" brief, or build a sales intelligence brief to pitch to a specific executive. Trigger phrases include "create a brief for [Company]", "find ICP targets for [Company]", "build the same brief for [Company]", "do this for [Company]", or any mention of a company name and LinkedIn URL in a sales prospecting context. Also trigger when the user says something like "run this for [Company]" after having generated a brief previously.
---

# ICP Account Intelligence Brief

This skill generates a full HTML account intelligence brief showing a target company's best 10 ICP-fit accounts, powered by live Sumble data. The brief is personalized for a specific executive using their LinkedIn profile.

## Step 0: Collect Inputs

Before doing any research, use AskUserQuestion to collect:
1. **Company name** — the company whose ICP we're building the brief for (e.g., "HubSpot", "Drift", "Salesloft")
2. **LinkedIn URL** — the LinkedIn profile URL of the specific person at that company you're pitching to

If both are already present in the conversation, skip this step.

---

## Step 1: Research the Company

Use WebSearch to understand the company. Your goal is to extract:
- **Products/platform** — what they actually sell
- **Problems solved** — what pain they address for customers
- **ICP characteristics** — industry, employee size, tech stack, buying persona
- **Displacement targets** — what tools/vendors they replace or compete with (critical for Sumble queries)
- **Named reference customers** — real companies they cite in case studies (you'll use these in pitches)
- **ACV range** — deal size signals (check pricing pages or press coverage)

Good search queries:
- `"[Company]" ideal customer profile`
- `"[Company]" customers case studies`
- `"[Company]" vs [competitor]`
- `site:[company].com/customers`

---

## Step 2: Profile the Person Being Pitched

Use the LinkedIn URL to find this person in Sumble. Call FindPeople at their company and match by name or title from the URL slug.

Extract:
- **Full name and title**
- **Tenure in role** — new leaders (< 18 months) actively re-evaluate their stack; this is a compelling angle
- **Prior employers** — shapes which reference customers will resonate (ex-Salesforce exec → Salesforce-native references; ex-startup → growth-stage references)
- **Function** — CRO/VP Sales → pipeline and rep productivity angles; RevOps → data quality and forecast accuracy; CEO → ROI and efficiency

This shapes two outputs:
1. A **"Prepared for"** header personalized to this executive
2. **Tonal adjustments** to the top 3 account openers — which play gets priority and which reference customers get cited

---

## Step 3: Find Technology Slugs

Based on the tools the company displaces, use SearchTechnologies to get correct Sumble slugs. Run one search per tool.

Common displacement patterns:
- Sales engagement platform → Salesloft, Gong, Clari, MindTickle, Highspot
- CRM → Salesforce, HubSpot, Pipedrive, Zoho CRM, Microsoft Dynamics
- Learning/enablement → Docebo, Cornerstone OnDemand, SAP Litmos
- Marketing automation → Marketo, Pardot, Eloqua

---

## Step 4: Find 10 ICP-Fit Accounts

Run 2–3 parallel FindOrganizations queries, varying the displacement technology:

```
technology EQ 'slug' AND employee_count IN ('1000-5000', '5000-') AND hq_location EQ 'US'
```

Adjust employee_count and industry filters to match the company's actual ICP.

**Filter out** private equity firms, holding companies, and non-operating entities even if they appear with high scores.

From all results, select the 10 best by combining Sumble account_score, diversity of play types, and company recognizability.

### Adaptive strategies if Step 4 returns zero or sparse results

Tech-displacement filters often return zero in healthcare, regulated, government, or non-tech-native industries where org tech stacks aren't widely surfaced through job-post mentions. If that happens:

1. **Verify the industry label before blaming the filter.** Sumble uses census/NAICS-style labels — e.g., `"Health Care and Social Assistance"`, not LinkedIn's `"Hospital & Health Care"`. Run a diagnostic SQL query to confirm the right string before retrying:
   ```sql
   SELECT DISTINCT industry, COUNT(*) AS n FROM organizations
   WHERE LOWER(industry) LIKE '%[keyword]%'
   GROUP BY industry ORDER BY n DESC LIMIT 30
   ```

2. **Drop the size filter.** Specialized sectors often have ICP targets below the SaaS-default 1,000+ employees.

3. **Sort by `account_score`, not just tech presence.** With just `technology IN ('competitor-slugs') AND hq_location EQ 'US'` (no industry/size filter), Sumble's built-in fit scoring often surfaces the right ABM accounts directly.

4. **Use `matching_job_post_count` as the secondary buying-window signal.** Higher counts = more active hiring referencing that technology = stronger buying signal. In one healthcare run, the #1 account had 1,347 HCHB-mentioning job posts vs. the median of ~30 — a meaningful intensity gradient that score alone wouldn't surface.

5. **Trust fuzzy domain matches in FindJobs as adjacency intel.** When you scope `FindJobs` to one domain and get results from a different but related org (parent, subsidiary, recent spinoff), that's a feature — surface those adjacent orgs as candidates. In one healthcare run this surfaced Aveanna (under a Five Points query) and Enhabit (under an Encompass query, both real corporate spinoffs).

---

## Step 5: Build Buying Groups (Top 5 Accounts)

For the top 5 ranked accounts, call FindPeople with:
```
job_function IN ('Sales', 'Revenue Operations', 'Executive') AND job_level IN ('CXO', 'VP', 'SVP', 'EVP', 'Director', 'Head')
```

Adapt the `job_function` set to the target company's actual buyer profile — in healthcare/clinical software, use `IN ('Executive', 'Operations', 'Information Technology')` instead of sales-oriented functions.

**Filter buying group results to US-based people** (`country_code == 'US'` in the response). Sumble's people-org mapping is fuzzy — common names will pull in international executives at unrelated orgs that happen to share a domain string. Always validate by location before assigning a buying-group role.

For very large IDNs / multi-region orgs (e.g., 500+ verified leaders), the top FindPeople slice will skew to facility/division-level CXOs rather than corporate buyers. In that case, surface 1-2 named contacts you can verify, then link to the Sumble org people page for full discovery rather than guessing.

Assign buying group roles: Executive Sponsor (CRO/CEO/President), Key Evaluator (VP Sales/RevOps/CIO/COO), Economic Champion (Director RevOps/Finance/CFO), End User (AE/SDR/Operations leadership).

For the bottom 5, link to the Sumble org people page.

---

## Step 6: Find Job Signals (Top 5 Accounts)

For the top 5 accounts, call FindJobs. Look for roles that reference displacement technologies, signal scaling pressure (AE/SDR hiring), or confirm an active buying window. Use 1–2 job rows per account.

---

## Step 7: Define Three Plays

Synthesize three plays specific to this company — not generic labels. Each play needs:
- A number (01, 02, 03)
- A specific name (e.g., "Stack Consolidation", "RevOps Data Unification", "Agentic AI Pipeline")
- The exact tools displaced or pain being solved
- The specific products being sold and their value
- The primary buyer persona with title
- A realistic ACV range

Assign each of the 10 accounts a primary play (and optionally a secondary) based on their Sumble signals.

---

## Step 8: Write Account Wedges and Openers

For each account write:

**Wedge** (2–4 sentences): The specific reason this account is in the brief right now, grounded in real Sumble data — job counts, team counts, technology names. Explain what the signal reveals about their current pain or buying window.

**Opener** (1 paragraph, italicized): A personalized cold outreach message to the buying group contact that references the Sumble signal by name, cites a relevant reference customer with a specific metric, and ends with a low-friction call to action.

For the **top 3 accounts only**, adjust the opener to reflect the pitched person's background from Step 2 — emphasize the play that matches their function, cite reference customers from companies they'd recognize.

---

## Step 9: Generate the HTML Brief

Read `references/html-template.md` for the complete CSS and structural patterns. Use it exactly.

Key personalization elements:
- **Topbar**: Company name and first-letter logo mark in brand color
- **Hero eyebrow**: `⚡ Prepared for [Full Name], [Title] · [Company] · Internal`
- **Hero headline**: Reference the most striking Sumble stat found during research
- **Hero sub**: Describe the target company and why these 10 accounts are their ideal prospects
- **Brand color** (`--brand`): Use the company's actual brand color if known, or pick from presets in html-template.md
- **Footer**: Credit Sumble, name the person it was prepared for, include generation date

Save the brief to a dedicated, deploy-ready directory so Step 10 can push it to Netlify as-is:

```
~/icp-briefs/<site-name>/index.html
```

Where `<site-name>` is the slug defined in Step 10 (e.g. `jane-smith-hubspot-icp`). The file MUST be named `index.html` (not `[company-slug]_icp_brief.html`) so Netlify serves it at the site root.

Also share a local `computer://` link to the file for preview before deploy.

---

## Step 10: Deploy to Netlify

Push the brief to Netlify so the recipient gets a clean, shareable URL personalized to them.

### 10a. Build the site name

Derive a stable, Netlify-safe slug from the **recipient** (the person being pitched) and the **target company** (the company whose ICP brief this is — same one from Step 0):

```
<firstname>-<lastname>-<target-company>-icp
```

Rules:
- Lowercase only
- Replace spaces and punctuation with `-`
- Strip diacritics and any character that isn't `a-z`, `0-9`, or `-`
- Collapse repeated hyphens
- Must match the regex `^[a-z0-9-]+$` (Netlify enforces this)

Examples:
- Jane Smith @ HubSpot → `jane-smith-hubspot-icp`
- José O'Brien @ Salesloft → `jose-obrien-salesloft-icp`

This slug is reused as both the local directory name (Step 9) and the Netlify project name (resulting URL: `https://<slug>.netlify.app`).

### 10b. Find or create the Netlify project

First check whether a project with this name already exists (so re-runs update the same site instead of creating duplicates):

Call `mcp__netlify__netlify-project-services-reader` with:
```
operation: get-projects
params: { projectNameSearchValue: "<slug>" }
```

- If a project matching `<slug>` exactly is returned, capture its `siteId` for the deploy.
- If no match, create one. Call `mcp__netlify__netlify-project-services-updater` with:
  ```
  operation: create-new-project
  params: { name: "<slug>" }
  ```
  Capture the returned `siteId`.

If `create-new-project` fails because the name is taken globally on Netlify, append `-2`, `-3`, ... until it succeeds, and use the resulting name in the final URL.

### 10c. Deploy

Call `mcp__netlify__netlify-deploy-services-updater` with:
```
operation: deploy-site
params:
  siteId: "<siteId from 10b>"
  deployDirectory: "/Users/ericfitz/icp-briefs/<slug>"
```

The directory must contain `index.html` (from Step 9). No build step is needed — Netlify serves the static file directly.

### 10d. Report the live URL

After deploy succeeds, share the live URL with the user in this format:

```
✅ Brief deployed: https://<slug>.netlify.app
   Prepared for: <Full Name>, <Title> @ <Recipient Company>
   Local file: ~/icp-briefs/<slug>/index.html
```

If the Netlify API returns a different primary URL (custom domain, deploy preview, etc.), use that instead of constructing the `.netlify.app` URL.

---

## Quality Checklist

Before finishing:
- [ ] All signal pills cite real Sumble numbers (job counts, team counts, tool names from API responses)
- [ ] Each wedge references a specific data point — no generic statements
- [ ] Each opener ends with a concrete CTA addressed to a real title
- [ ] Reference customers match the account's industry and scale
- [ ] The "Prepared for" header shows correct name, title, and company
- [ ] Top 3 openers reflect the pitched person's background and function
- [ ] All Sumble links are real URLs from API responses
- [ ] No PE firms, holding companies, or non-operating entities in the 10 accounts
- [ ] HTML renders without broken CSS or unclosed tags
- [ ] File is saved as `index.html` in `~/icp-briefs/<slug>/`
- [ ] Netlify project exists (found or created) and `siteId` was captured
- [ ] Deploy succeeded and the live `.netlify.app` URL was reported back to the user

---

## Reference Files

- `references/html-template.md` — Complete CSS, color system, and structural HTML patterns. Read this before writing any HTML.
