---
name: reddit-sov-report
description: >
  Generate a Reddit Share of Voice (SOV) + AEO report for any company using Sensorhub
  signal data. Produces TWO outputs: a professional .docx Word document AND a
  standalone .html visual report with the same data. Trigger whenever the user asks
  for a "Reddit SOV report", "share of voice report", "Reddit SOV for [company]",
  "AEO Reddit report", "social SOV report", or any variation asking to analyze a
  company's Reddit presence, competitive visibility, or LLM citation risk on Reddit.
  Also trigger when the user says "run an SOV report", "create a Reddit analysis
  report", or "how does [company] compare on Reddit". The user only needs to provide
  a company name - the skill handles everything else automatically.
---

# Sensorhub Reddit SOV Report

Generates a Reddit Share of Voice + AEO/LLM citation analysis report for any company.
The user provides only ONE input: a company name. The skill produces two output files:
1. A `.docx` Word document (professional, shareable)
2. A `.html` visual report (interactive, designed for Sensorhub UI or browser use)

---

## Workflow Overview

1. Find the Sensorhub project for the company
2. Pull project intel (competitors, tracked subreddits, positioning)
3. Search Reddit signals - brand mentions, competitors, category keywords
4. Analyze SOV, AEO gaps, conversation themes, subreddit coverage
5. Generate the `.docx` report
6. Generate the `.html` visual report
7. Present both files to the user

---

## Step 1: Find the Project

Use `list_projects` to get all Sensorhub projects. Match the company name the user
provided (fuzzy match is fine). Save the `projectId` for all subsequent calls.

If no project exists, tell the user and offer a web-research-only version. Flag the
report as "web research only - no live Sensorhub signal data."

---

## Step 2: Pull Project Intel

Use `ask_genie` with the projectId. Run these two queries:

```
Query 1: "Who are the main competitors tracked in this project? List each with their
strengths, weaknesses, and how this product differentiates."

Query 2: "What subreddits are being tracked in this project? List all of them."
```

---

## Step 3: Pull Reddit Signals

Use `search_signals` scoped to the project, filtered to `source: "reddit"`. Run these
searches (in sequence, not parallel - Sensorhub MCP is sequential):

1. Brand name (direct mentions) - `sort: "newest"`, `limit: 50`
2. Category keywords (e.g., "marketing automation", "email automation") - `sort: "newest"`, `limit: 50`
3. Primary competitor names (top 2-3 from Step 2) - one search per competitor, `limit: 30`

For each search, capture: title, snippet, source, score, scoreCategory, url, createdAt.

Categorize results:
- `hot-leads` (score > 0.85) - highest AEO citation risk
- `high-potential` (0.75-0.85) - strong outreach/content targets
- `trust-builders` (0.65-0.75) - community engagement signals
- Brand signals vs category signals vs competitor signals

---

## Step 4: Analyze

Using all gathered data, produce these analytical outputs:

**SOV Estimates** - Directional brand mention frequency as % of category conversation.
Base on signal density per brand in the tracked pool. Flag as "directional estimate."

**Competitor SOV scores** - Score each competitor 0-100 using this formula:
- Mention volume: 30%
- Dedicated subreddit presence: 25%
- Thread engagement depth (signal scores): 20%
- Sentiment from snippets: 15%
- AEO citation likelihood (upvotes/evergreen nature): 10%

**Subreddit Coverage Map** - Tier each tracked subreddit:
- Tier 1: Category-specific + competitor-owned + high ICP-match communities
- Tier 2: Active but lower fit
For each, note the company's presence: absent / minimal / active.

**Conversation Themes** - Identify 4-6 recurring themes from signal snippets.
Note volume level and strategic fit for the company.

**AEO Gap Analysis** - Flag threads that are:
- High LLM citation risk (hot-lead score, brand mentioned, evergreen format)
- AEO gaps (high intent, no brand answer in thread)

**Brand Signal Summary** - List ALL direct brand mention signals found.
Note if they are organic vs founder-authored vs third-party.

**Strategic Implications** - 3 bullets for positioning, product, and content.

---

## Step 5: Generate the .docx

If a docx skill is available, read it for formatting rules.
Install docx if needed: `npm install -g docx`

### Document structure

1. Title page (company name, date, "Powered by Sensorhub.ai", signal count)
2. Executive Summary (5-7 sharp bullets with real data points)
3. Brand SOV Snapshot (table: brand, est. SOV%, Reddit presence, dominant conversation type)
4. Subreddit Coverage Map (Tier 1 table, Tier 2 prose, coverage gap callout)
5. Conversation Theme Analysis (5 themes with volume labels)
6. Competitor Deep-Dive (one section per top competitor)
7. AEO / LLM Citation Analysis (table of high-risk + gap threads, callout)
8. Brand Signals - Direct Mentions (table of all found signals)
9. SOV Composite Score (scoring table with stars)
10. Strategic Implications (3 sub-sections: positioning, product, content)

### Formatting rules (from docx skill)
- US Letter (12240 x 15840 DXA), 1-inch margins
- Arial font, Heading1 blue (#1F4E79), Heading2 blue (#2E75B6)
- Use `LevelFormat.BULLET` - never unicode bullets
- Tables: `WidthType.DXA` always, dual widths (columnWidths + cell width)
- `ShadingType.CLEAR` for table shading, never SOLID
- Page numbers in footer with tab stops
- Callout boxes as single-cell tables with colored fills
- Single dashes (-) not em dashes throughout

Save as `[company-slug]-reddit-sov-report.docx` in the current working folder (or the
outputs folder if your environment has one).

---

## Step 6: Generate the .html

Create a standalone, self-contained HTML file. Light theme (white background).
No external dependencies except Google Fonts.

### Design system

```css
fonts: DM Mono (monospace/labels) + Instrument Serif (display title) + Geist (body)
Google Fonts import: DM Mono:ital,wght@0,300;0,400;0,500 + Instrument+Serif:ital@0;1 + Geist:wght@300;400;500

background: #f9f8f6 (page), #ffffff (cards), #f4f3f0 (inset/track)
borders: rgba(0,0,0,0.07) default, rgba(0,0,0,0.13) accent
text: #16140f primary, #6b6760 secondary, #a09c96 muted
accent blue: #2255e0 (links, fills, callout borders)
red: #c53030 / dim: rgba(197,48,48,0.07)
amber: #a05e00 / dim: rgba(160,94,0,0.07)
green: #1a6b42 / dim: rgba(26,107,66,0.07)
```

### Page structure (in order)

1. **Header** - Sensorhub logo mark + wordmark, serif report title, mono metadata row
   (company / category / date), signal count in large mono on the right

2. **Metric strip** - 4-cell grid (no gaps between cells, separated by 1px border):
   - Company SOV score / Direct brand signals / Subreddits tracked / SOV leader

3. **SOV bars section** - Full-width card with horizontal bar chart per competitor.
   Each row: brand name (140px) | track bar | % | score pill badge.
   Bars animate in via CSS (`animation: fillBar`). The target company is highlighted
   at bottom with red color. Callout box below explaining scoring methodology.

4. **Two-column row** - Top signals list (left, 1.5fr) + Conversation themes (right, 1fr)

5. **AEO section** - 3-col grid of cards. First 3 = high citation risk (red risk label).
   Last 3 = AEO gaps (blue label, blue border accent). Callout below.

6. **Subreddit coverage map** - 3-col grid of sub-cards. Each: subreddit name (mono),
   category label (colored), presence indicator (colored dot + text).
   Legend below (present / minimal / absent).

7. **Brand signals** - Full-width card with signal rows. Each row: score ring (colored
   circle, ring-hot/ring-high/ring-trust) | title | meta (subreddit + note + sentiment).
   Danger callout box below if all mentions are founder-authored.

8. **Strategic implications** - 3-col grid of strat cards with icon, mono title, body text.

9. **Footer** - "Generated by sensorhub.ai · Signal pool: N · Reddit focus · [date] · [company]"

### CSS patterns

```css
/* Subtle grid texture on body */
body::before {
  background-image:
    linear-gradient(rgba(0,0,0,0.025) 1px, transparent 1px),
    linear-gradient(90deg, rgba(0,0,0,0.025) 1px, transparent 1px);
  background-size: 40px 40px;
}

/* SOV bar animation */
@keyframes fillBar { from { width: 0 !important; } }
.sov-fill { animation: fillBar 1.2s cubic-bezier(0.4,0,0.2,1) both; animation-delay: 0.4s; }

/* Page load stagger */
@keyframes fadeUp { from { opacity: 0; transform: translateY(12px); } to { opacity: 1; transform: translateY(0); } }
.animate-in { animation: fadeUp 0.5s ease both; }
.a1 { animation-delay: 0.05s; } /* through .a8 at 0.05s increments */

/* Score ring colors */
.ring-hot { background: var(--red-dim); color: var(--red); border: 1px solid rgba(197,48,48,0.3); }
.ring-high { background: var(--amber-dim); color: var(--amber); border: 1px solid rgba(160,94,0,0.3); }
.ring-trust { background: var(--accent-dim); color: var(--accent); border: 1px solid var(--accent-border); }

/* Callout box */
.callout { border-left: 2px solid var(--accent); padding: 12px 16px; background: var(--accent-dim); border-radius: 0 8px 8px 0; }
.callout.danger { border-left-color: var(--red); background: var(--red-dim); }
```

### SOV bar colors (light theme - dark enough to read on white)
- #1 brand (largest): #2255e0
- #2: #4a7aee
- #3: #a05e00
- #4: #c47a10
- #5: #1a6b42
- #6: #2e9e6a
- Other/niche: #c5c2bc
- Target company: var(--red) or #c53030

Save as `[company-slug]-reddit-sov-report.html` next to the .docx.

---

## Step 7: Present Both Files

Share both files with the user (use `present_files` if your environment has it, otherwise give the paths).

Give a one-line summary of the key finding (e.g., "X/100 composite SOV score,
N direct brand mentions, N AEO gap threads identified").

---

## Edge Cases

- **No project found**: Offer web-research-only run. Skip Steps 2-3. Fill SOV estimates
  from web research. Flag prominently in both outputs.
- **Few signals**: Lower the bar - include all signals regardless of score. Note thin
  coverage in executive summary.
- **Sold/sunset product**: Treat as benchmark analysis. Use historical framing.
- **No Reddit signals at all**: Report this honestly. Focus the report on subreddit
  coverage gaps and competitor AEO analysis as opportunity mapping.

---

## Notes

- Single dashes (-) throughout, no em dashes
- Every claim traces back to a real signal or web source - no unsourced assertions
- SOV % estimates are always labeled "directional" - not precise
- The html file is fully self-contained (no external JS, only Google Fonts CDN)
- Company slug for filenames: lowercase, spaces replaced with hyphens (e.g., "Acme Corp" → "acme-corp")
