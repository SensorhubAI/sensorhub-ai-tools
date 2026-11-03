---
name: reddit-sov-report
description: >
  Generate a Reddit Share of Voice (SOV) + AEO report for any company using Sensorhub
  signal data. Produces TWO outputs: a standalone .html report and a print-ready
  .pdf made from it. Trigger whenever the user asks
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
1. A `.html` report (self-contained, opens in any browser, in the Sensorhub report style)
2. A `.pdf` made from the same HTML (print-ready, easy to forward)

---

## Workflow Overview

1. Find the Sensorhub project for the company
2. Pull project intel (competitors, tracked subreddits, positioning)
3. Search Reddit signals - brand mentions, competitors, category keywords
4. Analyze SOV, AEO gaps, conversation themes, subreddit coverage
5. Generate the `.html` report
6. Make the `.pdf` from it
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

## Step 5: Generate the .html

One self-contained file. No external JS; the only external load is Inter from Google Fonts.

### Styles

Read `assets/report.css` and paste it whole into the report's `<style>`, after this in `<head>`:

```html
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
```

Build every part from its classes; don't add colours, fonts, radii or shadows of your own. Format
every number (1,284, never 1284). Label SOV percentages as directional.

### Page structure (in order)

1. **Header** - `<header class="rp-header">` with the `rp-meta` row (`<span class="rp-brand"><i></i>Sensorhub</span>`,
   "Reddit share of voice", company, category, date, "N signals"), an `<h1>` that states the main finding
   in plain words (e.g. "Acme is mentioned in 4% of Reddit's CRM conversations"), and a one-line `lede`.
2. **Metric strip** - `stat-grid` of four `stat` cards: company SOV score, direct brand signals,
   subreddits tracked, SOV leader.
3. **Executive summary** - a `card` with 5-7 bullets, each with a real data point.
4. **Share of voice** - a `card` of `bar-row`s, largest first. The company's fill is `bar-fill is-own`
   (indigo, label in bold). Competitors take `c1`, `c2`, ... `c8` in the project's competitor order, so
   each keeps its colour. Small or niche brands share one plain `bar-fill` row ("Other"). A `callout`
   below explains how SOV was scored.
5. **Brand SOV snapshot** - a `table` with brand, est. SOV %, Reddit presence and dominant conversation type.
6. **Top signals and themes** - `grid-2`: on the left a `card` of `thread` rows (title linked, `score` pill
   `s85`/`s75`/`s65`/`slow` by relevance, `thread-meta` with the subreddit); on the right the conversation
   themes, each with a `pill` for its volume (high, medium, low).
7. **AEO and LLM citations** - `grid-3` of `card`s. High citation risk threads carry a `pill negative`
   ("High citation risk"); AEO gaps carry a `pill info` ("Gap"). A `callout` below says what to do.
8. **Subreddit coverage** - a `table` of Tier 1 subreddits with category and presence (`pill positive`
   Present, `pill watch` Minimal, `pill` Absent), Tier 2 in a sentence, and a `callout watch` for the
   biggest coverage gap.
9. **Competitor deep-dive** - `grid-2` of `card`s, one per top competitor: where they win, the threads,
   the opening for the company.
10. **Brand signals** - a `card` of `thread` rows for every direct mention, with a sentiment `pill`
    (`positive`, plain for neutral, `negative`). Use `callout negative` if every mention is founder-authored.
11. **Composite score** - a `card` of `bar-row`s, one per scoring axis, fill `strong`/`mid`/`low`, with
    the composite in the heading.
12. **Strategic implications** - `grid-3` of `strat-card`s: positioning, product, content.
13. **Footer** - `footer`: "Generated by sensorhub.ai · Signal pool: N · Reddit focus · [date] · [company]".

Motion: bars grow once on load (built into the styles) and stop for reduced motion. No fade-up on sections.

Save as `[company-slug]-reddit-sov-report.html` in the current working folder (or the outputs folder
if your environment has one).

---

## Step 6: Make the PDF

Convert the HTML to PDF with weasyprint. Install it if needed (one-time):

```bash
pip install --break-system-packages weasyprint
```

Then, replacing `<company-slug>`:

```bash
cd <folder with the HTML> && python3 -c "
from weasyprint import HTML, CSS
print_css = CSS(string='''
  @page { size: A4; margin: 18mm 16mm 18mm 16mm; }
  body { background: white !important; }
  .container { padding: 0 !important; max-width: 100% !important; }
  .bar-fill { animation: none !important; }
''')
HTML('<company-slug>-reddit-sov-report.html').write_pdf('<company-slug>-reddit-sov-report.pdf', stylesheets=[print_css])
"
```

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
- The HTML file is fully self-contained (no external JS, only Google Fonts CDN), and the PDF is made from it
- Company slug for filenames: lowercase, spaces replaced with hyphens (e.g., "Acme Corp" → "acme-corp")
