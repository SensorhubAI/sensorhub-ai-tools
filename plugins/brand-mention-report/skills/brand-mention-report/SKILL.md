---
name: brand-mention-report
description: >
  Generate a cross-platform brand mentions report (Reddit + LinkedIn + X) for any
  brand tracked in Sensorhub. Produces TWO deliverables: a standalone HTML file
  and a print-ready PDF, both designed to be shared with a marketing manager or
  CMO as a competitive intel snapshot. Trigger whenever the user
  asks for a "brand mentions report", "brand mention analysis", "mentions report
  for [company]", "brand monitoring report", "social listening report", or any
  variation asking to analyze how a brand is being discussed across Reddit,
  LinkedIn, and X. Also trigger on "run a brand report", "create a mentions
  analysis", "how is [company] being talked about", or "cross-platform brand
  report". The user provides only a brand name - the skill handles project
  lookup, competitor co-mention counts, intent classification, brand event
  detection, and report rendering automatically. All data comes from Sensorhub
  MCP only - never use web search or external sources.
---

# Sensorhub Brand Mention Report

Generates a cross-platform brand mentions report for any brand the user has set up
in Sensorhub. Produces two files from the same data:

1. A `.html` file - standalone, self-contained, shareable, designed for in-browser
   viewing or embedding in a Notion / landing page.
2. A `.pdf` file - print-ready, forwardable to a CMO, generated from the same HTML
   via weasyprint.

The report's value comes from being honest, specific, and sourced. Every figure
traces back to a Sensorhub signal.

---

## Hard Rule: Sensorhub MCP Only

**Never use web search, WebFetch, scraping, or any external data source.** Every
number, every example, every competitor must come from Sensorhub MCP data
(`list_projects`, `get_project`, `list_signals`, `search_signals`).

If a question cannot be answered from Sensorhub data, drop the question rather
than fill it from elsewhere. A report that quietly pulls from web search mixes
unverified numbers with tracked ones and erodes trust.

---

## Workflow Overview

1. Find the Sensorhub project for the brand
2. Pull project context (category, competitors, ICPs)
3. Confirm detection with the user (only if ambiguous)
4. Pull signals across Reddit, LinkedIn, X
5. Search competitor co-mentions for SOV
6. Classify signals into the 6 intent buckets
7. Detect brand events (if any)
8. Build the action list
9. Render the HTML from the template
10. Convert HTML to PDF via weasyprint
11. Save both files and present them

---

## Step 1: Find the Project

Call `list_projects` to get all Sensorhub projects in the workspace. Fuzzy-match
the brand name the user provided to the `name` field.

- **One clear match**: save the `id` as `projectId` and proceed.
- **Multiple candidates**: ask the user to confirm which one.
- **No match**: tell the user the brand isn't in Sensorhub yet and ask them to
  set up a project first. **Do not fall back to web research.** This is a hard
  guardrail.

---

## Step 2: Pull Project Context

Call `get_project` with the `projectId`. Capture:

- `name`, `url`, `category`, `summary`
- `competitors` (array - take the first 5 as the SOV comparison set)
- `icps` (used later for tone calibration in the action list suggestions)
- `keywords` (helpful for category-level buying-intent threads)

---

## Step 3: Confirm Detection

If the project context is unambiguous, skip this step. Otherwise show the user
what the skill detected and ask to proceed. Example:

> Detected: Acme Corp (category: marketing automation). Top competitors I'll
> compare against: HubSpot, Marketo, Klaviyo, Brevo, ActiveCampaign. Sound right?

This guard catches brand-name ambiguity early. A report about the wrong company
is worse than no report.

---

## Step 4: Pull Signals Across Platforms

Call `list_signals` three times - once per source - with these parameters:

```
projectId: <projectId>
after: <ISO date 30 days ago>
sources: ["reddit"]   then ["linkedin"]   then ["x"]
limit: 40
sort: "score_desc"
```

That gives 120 top-scored signals across the three platforms for the last 30
days. This is the working sample for the intent classification and action list.

**If a platform returns zero signals**, the project isn't tracking that source.
The report should still render, but flag the gap in the methodology and skip
the platform's section of the platform-mix chart.

---

## Step 5: Pull Competitor Co-Mention Counts

For each of the top 5 competitors from Step 2, call `search_signals`:

```
projectId: <projectId>
query: <competitor name>
after: <ISO date 30 days ago>
limit: 50
sort: "newest"
```

The result length is your co-mention count for that competitor (capped at 50).
**If the result returns exactly 50, note in the report that the count is capped
and the real co-mention rate is higher.** This is the SOV map data.

---

## Step 6: Classify Signals into 6 Intent Buckets

Read every signal in the 120-signal sample and assign each to **one** of these
six buckets. See `references/classification-rules.md` for the full rule set with
examples. Quick summary:

| Bucket | Sentiment | When to assign |
|--------|-----------|----------------|
| Buying intent (category) | Opportunity | Asking for recs in the category, brand not named |
| Comparison | Neutral | Multiple brands named in a "vs" or "stack" thread |
| Churn / displacement | Risk | "Replace X", "alternative to X", "switching from X" |
| Pricing complaint | Risk | Explicit price concern naming the brand or category |
| Win / testimonial | Positive | Positive citation, case study, brand-affiliated content |
| Feature / product feedback | Neutral | Feature requests, workarounds, "how does X handle Y" |

Signals that don't fit any bucket are tagged "general industry mention" and
counted in the methodology footnote, not in the intent grid.

For each bucket pick 3-4 example threads to surface in the report. Prefer the
highest-scored and most recent threads. Each example needs a title, source
label (Reddit r/xxx, LinkedIn - Author, X - @handle), and URL.

---

## Step 7: Detect Brand Events

A brand event is a single concentrated topic that dominates the period (e.g.,
an acquisition, a product launch, a public incident). The rule:

> If 15% or more of the high-relevance signals (score > 0.65) cluster on the
> same topic in the same week, surface it as a brand event.

If you detect one, populate the brand event section. If not, leave the
`{{BRAND_EVENT_SECTION}}` token empty in the template and the section is
hidden by the surrounding CSS conditional.

Common brand event triggers in this dataset: acquisitions, leadership changes,
product launches, viral threads, security incidents.

---

## Step 8: Build the Action List

Pick 6-8 threads for the "Monday list" - the highest leverage threads from the
report period. Source them from these buckets, in priority order:

1. **Buying intent (category)** with no brand-affiliated reply yet
2. **Churn / displacement** where the asker is a current customer
3. **Comparison** threads asking for opinions
4. **Pricing complaint** that can be addressed with a real plan option
5. **Feature / product feedback** that maps to a product the brand already has

For each, write a one-sentence "suggested angle" that reflects the ICP context
from Step 2. The angle should be specific to that thread, not boilerplate.

---

## Step 9: Render the HTML

Read `assets/report-template.html`. The template uses `{{TOKEN}}` placeholders
for every variable section. See `references/template-tokens.md` for the full
list of tokens and the HTML shape each one expects.

Save the rendered HTML to:
`<brand-slug>-brand-mentions-report.html` in the current working folder (or the outputs
folder if your environment has one).

---

## Step 10: Generate the PDF

Convert the HTML to PDF using weasyprint. Install if needed (one-time):

```bash
pip install --break-system-packages weasyprint
```

Then run this Python via bash, replacing `<brand-slug>` with the actual slug:

```bash
cd <folder with the HTML> && python3 -c "
from weasyprint import HTML, CSS
print_css = CSS(string='''
  @page { size: A4; margin: 18mm 16mm 18mm 16mm; }
  body { background: white !important; }
  .container { padding: 0 !important; max-width: 100% !important; }
  .bar-fill { animation: none !important; }
  .cta, .intent-card, .action-item, .event-card, .stat-grid { page-break-inside: avoid; }
  a { color: #4F46E5 !important; }
''')
HTML('<brand-slug>-brand-mentions-report.html').write_pdf(
  '<brand-slug>-brand-mentions-report.pdf', stylesheets=[print_css]
)
"
```

---

## Step 11: Present Both Files

Share both files with the user (use `present_files` if your environment has it, otherwise give the paths).

End with one or two sentences summarizing the single most interesting finding
from the report. Examples of good summaries:

- "Most surprising signal: 9 active churn / displacement threads in the last 30
  days, all citing pricing as the trigger."
- "Brand event detected: the Adobe acquisition is dominating the LinkedIn
  conversation (22 mentions of the deal, ~80% positive framing)."

---

## Edge Cases

**Low volume brand** (fewer than 100 total signals in 30 days)
Switch to "you're not in the conversation" framing. The action list page becomes
the centerpiece. SOV map becomes a "where the conversation is happening" map.
Note thin coverage in the methodology section.

**Project tracks only one or two platforms** (no LinkedIn or X data)
Render the report with the platforms that have data. In the platform-mix chart,
show only the active platforms. In the methodology, explicitly note that
LinkedIn and/or X tracking can be enabled in the Sensorhub project settings
to broaden coverage in future runs.

**No clear top competitor** (search_signals returns under 5 for all competitors)
The SOV bar chart will be sparse but should still render. Add a note that the
brand isn't being compared against competitors much, which can be either a
moat (uncontested category) or a visibility problem (not in the consideration
set).

**Search cap hit on multiple competitors** (limit=50 hit on 3+ competitors)
The relative SOV is unreliable when caps are hit. Add a note that several
competitors hit the search cap and the real ordering may differ.

**Ambiguous or generic brand name**
"Notion", "Linear", "Square" can all hit unrelated companies. Always confirm in
Step 3 when the project name matches but the category from Step 2 looks
inconsistent with what the user described.

---

## Output Style Rules

- Single dashes (-) throughout the report copy. No em dashes (—) or double
  dashes (- -).
- Every claim in the report traces back to a real Sensorhub signal. If you
  cannot source it, don't write it.
- Sentiment posture per bucket is derived from signal text at report time. The
  methodology section calls this out explicitly so marketing managers know it's
  not a stored sentiment score.
- SOV is co-mention rate, not absolute share. The methodology explains the
  difference and the chart label says "co-mentions" not "share of voice".
- CTA at the bottom of the report points to the Sensorhub dashboard, framed as
  "set this up to track weekly", not a sales demo.

---

## What This Report Doesn't Cover

These are explicitly out of scope for v1, documented in the methodology page
of the report itself so readers don't expect them:

- AI citation visibility (how often the brand is cited inside ChatGPT, Gemini,
  Perplexity). Roadmap item, depends on Sensorhub data availability.
- Sentiment trend lines week-over-week. Requires longer historical window.
- Author reach scoring per mention.

If a user asks for any of these, the report should not silently estimate them.
Either flag as not available, or skip.
