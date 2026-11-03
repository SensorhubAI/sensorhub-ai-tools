---
name: signal-analysis-report
description: >
  Generate a comprehensive competitive intelligence and signal analysis report for any
  company using Sensorhub MCP data. Trigger whenever the user says "sensorhub report for
  [Company]", "competitive intel report for [Company]", "signal report for [Company]",
  "analyze signals for [Company]", or any variation that implies generating a structured
  market intelligence report using Sensorhub data. The user only needs to provide a company
  name - the skill handles all research, signal pulling, and report generation automatically.
  Also trigger when the user asks to "run a report", "pull signals", "competitive analysis",
  "signal analysis", or "market intel" for a named company. Always use this skill instead of
  doing ad-hoc research when the user wants a full structured report.
---

# Sensorhub Signal Analysis Report

Generates a comprehensive competitive intelligence and signal analysis report for any
company. The user provides only a company name. The skill pulls all data from Sensorhub
MCP tools and web research, then outputs a formatted .docx report.

---

## Workflow Overview

1. Identify the Sensorhub project for the company
2. Pull project intelligence from Sensorhub (KSPs, ICPs, competitors, signals)
3. Supplement with web research where Sensorhub data has gaps
4. Search and analyze live signals
5. Compile everything into the report template
6. Output as .docx

---

## Step 1: Find the Project

Use `list_projects` to get all Sensorhub projects. Match the company name the user
provided to a project. If no exact match, try partial/fuzzy matching and confirm with the
user. If no project exists at all, tell the user and ask if they want a web-research-only
report instead.

Save the `projectId` for all subsequent calls.

---

## Step 2: Pull Project Intelligence

Use `ask_genie` with the projectId to gather structured data. Run these queries in
parallel to save time:

```
Query 1: "What is the product's full name, URL, one-line description, and core value proposition?"
Query 2: "What are the product's core differentiators, unique selling proposition, and pricing model?"
Query 3: "Who are the ideal customer profiles? List all B2C personas and B2B personas with their pain points."
Query 4: "Who are the main competitors? List each with strengths, weaknesses, and how this product differentiates against them."
Query 5: "What subreddits, LinkedIn sources, X accounts, and other platforms are being tracked?"
```

These queries pull from the project's knowledge base - KSPs (Key Selling Points), ICPs
(Ideal Customer Profiles), competitor data, and tracked sources that have been configured
in the Sensorhub project.

---

## Step 3: Fill Research Gaps

After pulling Sensorhub data, check which template fields still have gaps. Use web search
to fill them. Common gaps include:

- Detailed pricing tiers and numbers (search "[Company] pricing")
- Recent regulatory or industry context (search "[industry] regulations 2026")
- Seasonal buying patterns for the industry
- High-intent trigger phrases specific to the category
- Specific competitor complaint patterns (search "[competitor] complaints reddit",
  "[competitor] alternatives")

The goal is a complete dataset - no placeholder fields should remain empty in the final
report.

---

## Step 4: Pull Live Signals

Use `search_signals` to find real conversations relevant to the company. Run searches for:

1. **Direct brand mentions** - the company name itself
2. **Competitor names** - each major competitor
3. **Category keywords** - the product category, key use cases
4. **Pain point keywords** - common frustrations the product solves
5. **High-intent phrases** - buying/switching language

For each search, capture: signal title, snippet, source platform, score, and signal ID.

Filter by category when useful:
- `hot-leads` (score > 0.85) for Section 4 outreach targets
- `high-potential` (0.75-0.85) for general analysis
- `trust-builders` (0.65-0.75) for community engagement opportunities

Sort by relevance for analysis, by score for the Top 10 outreach list.

---

## Step 5: Analyze and Compile

Using all gathered data, compile the report following the exact structure below. Every
section must be grounded in real data from Sensorhub signals or web research. Do not
fabricate signals or invent data points.

### Report Structure

The report has 4 sections, always in this order:

**Section 1 - Project Intel Summary**

Reproduce the full structured intelligence so it travels with the analysis:
- Product name, URL, one-line description
- UVP and USP
- Full ICP list with personas and pain points (B2C and B2B, formatted as specified)
- Full competitor list with strengths, weaknesses, and the product's wedge against each
- All tracked sources (subreddits, LinkedIn, X, other platforms)
- Pricing model

**Section 2 - Executive Summary**

5-7 bullet points with the sharpest findings. These should be actionable and specific,
not generic observations. Each bullet should reference a real signal or data point.

**Section 3 - Full Analysis**

Eight sub-sections, each driven by the signal data:

1. **User Intent Mapping** - Categorize signals by intent type (active problem,
   research/comparison, frustration venting, community sharing, decision-making). For
   each intent type, identify the best outreach angle given the product's UVP.

2. **Signal Friction Analysis - Competitor Drop-off** - Identify specific friction points
   in competitors' user journeys from signal data. Format each as: "[Competitor] did X
   and it didn't work / cost too much / required too much". Map each friction point to a
   product "wedge" message.

3. **High-Intent Trigger Phrase Detection** - Flag signals containing buying/switching
   language. List 8-12 trigger phrases specific to the category. Surface high-priority
   outreach targets with signal links and suggested reply angles.

4. **Industry and Regulatory Hooks (current year context)** - Describe regulatory shifts
   or industry trends making the product more relevant. Find signals from users dealing
   with these shifts. Note the compliance/regulatory angle for positioning.

5. **Sector Activity** - Which platforms and communities are most active in this space?
   Recurring events, threads, or seasonal patterns? Influencers or voices showing up
   repeatedly?

6. **B2B Decision Maker Mapping** - Signals from decision makers (VPs, managers, CTOs)
   discussing operational pains. Map each to the relevant product angle, highlighting key
   B2B features.

7. **Community Events and News** - Recent events, regulatory changes, or news driving
   conversation. These are timely hooks for outreach and content.

8. **Core Conversation Clusters** - Signals where the product's primary value prop topic
   AND the secondary topic are both mentioned. Group by theme. These are highest-fit
   conversations.

**Section 4 - Top 10 Outreach-Ready Signals**

For each of the 10 highest-priority signals:
- Signal link (from Sensorhub data)
- Platform/source
- Intent type
- ICP match (B2C or B2B persona)
- Trigger phrase detected (if any)
- Suggested reply angle

Select signals that are both high-relevance and actionable. Prefer signals with clear
buying intent or explicit competitor frustration.

---

## Step 6: Generate the .docx

Read the docx skill at the path indicated in available skills for formatting instructions.
Use the `docx` npm package to create a professional Word document.

### Document formatting guidelines

- US Letter page size (12240 x 15840 DXA)
- 1-inch margins all around
- Arial font family throughout
- Title page with: report title, company name, date generated, "Generated by Sensorhub"
- Table of contents after title page
- Section headers as Heading1, sub-sections as Heading2
- Use proper bullet lists (LevelFormat.BULLET), never unicode bullets
- Tables for structured data (competitor comparison, top 10 signals)
- Page numbers in footer
- Hyperlinks for all signal URLs

Save the final .docx to the outputs folder and present it to the user.

---

## Edge Cases

- **No Sensorhub project found**: Offer to run with web research only. Skip Steps 2 and
  4 (no ask_genie, no search_signals). Fill all fields from web search. Flag the report
  as "web research only - no live signal data".

- **Sensorhub project exists but has few signals**: Lower the bar. Include all available
  signals regardless of score. Note thin signal coverage in the executive summary and
  suggest the user configure more tracked sources.

- **Company is very niche or new**: Web research may return limited results. Fill what you
  can, mark gaps explicitly rather than inventing data. A partial but honest report is
  better than a fabricated one.

- **Multiple matching projects**: List them and ask the user which one to use.

---

## Notes

- The entire workflow should run without asking the user for input beyond the company name.
  If something is ambiguous, make a reasonable choice and note it in the report.
- Every claim in the report should trace back to either a Sensorhub signal or a web source.
  No unsourced assertions.
- Use single dashes (-) instead of em dashes throughout the report.
- Keep the tone professional but direct. No filler language.
