---
name: reddit-fit-score
description: >
  Grade any company A-F on how well Reddit fits as a go-to-market channel, using
  Sensorhub signal data. Produces a single standalone, on-brand .html report with a
  letter grade, a six-axis score breakdown, top communities, live high-intent threads,
  and two manual checks the data can't answer. Trigger whenever the user asks for a
  "Reddit Fit Score", "Reddit fit report", "is Reddit right for [company]", "should
  [company] be on Reddit", "grade my Reddit fit", "Reddit channel fit", or "run a
  Reddit fit check". The user only needs to provide a company name - the skill finds
  the project and handles everything else from Sensorhub data.
---

# Reddit Fit Score

Grades a company A-F on whether Reddit is worth investing in as a GTM channel, scored
entirely from Sensorhub signal data. The user provides ONE input: a company name. The
skill produces ONE output file: a standalone, self-contained `.html` report styled to
the Sensorhub brand.

**Sensorhub is the only data source. Never web-search,
never fetch outside pages, never invent numbers.** Every figure in the report must
trace to a Sensorhub call. The two things Sensorhub cannot answer (subreddit
participation rules and AI-answer presence) are rendered as manual checklist items for
the reader to complete - they are NEVER auto-scored.

---

## Workflow overview

1. Find the Sensorhub project for the company
2. Pull project intel (category, competitors, ICPs)
3. Pull Reddit signals for the last 90 days, score off the relevancy band only
4. Pull engagement on the top relevancy threads
5. Pull brand-name mentions as a dedicated tracked-keyword query
6. Compute the six axes, the composite, and the letter grade
7. Check the disqualifier flag from the ICP segments
8. Render the `.html` report
9. Present the file

Run Sensorhub calls **sequentially** - the MCP is not reliably parallel.

---

## Step 1: Find the project

Call `list_projects`. Fuzzy-match the company name the user gave. Save the `projectId`
for every later call.

If no project matches, stop and tell the user this skill needs a Sensorhub project for
the company - do NOT fall back to web research (this is a Sensorhub-only report).

## Step 2: Pull project intel

Call `get_project` with the projectId. Capture:
- `category` and `summary` (header + context)
- `competitors[]` - the names drive competitor presence (Step 3/6) and the threads list
- `icps[]` - read each `companySegment`; these drive the disqualifier flag (Step 7)

## Step 3: Pull Reddit signals (last 90 days)

Call `list_signals` with: `projectId`, `sources: ["reddit"]`, `sort: "score_desc"`,
`after` = 90 days ago (ISO), `limit: 100`.

This response is large and is usually saved to a file rather than returned inline. If
so, parse the saved file in chunks (or hand it to a subagent) and compute:
- total signals returned, and whether `hasMore`/`nextCursor` is set (means the pool is
  larger than the page - note "capped")
- count by score band: `>=0.85` (hot-leads), `0.75-0.85` (high-potential)
- **distinct subreddits**: parse the `/r/<sub>/` segment from each `url` (or use the
  `context` field from `get_signal`); list them with counts
- the top ~10 signalIds by score, with title + subreddit

**Noise filter (mandatory).** Sensorhub also returns keyword-match signals (score shown
as `1.01` / category "Keyword match") where a competitor or brand name was caught
incidentally - job postings, unrelated subreddits, etc. **Exclude these from demand
volume and commercial intent.** Score those two axes only on genuine relevancy signals
(category "85%+", "75%+" etc., score <= 1.0). Keyword matches still inform competitor
presence (Step 6) and brand presence (Step 5), but flag any obvious noise in the report's
caveat callout.

## Step 4: Pull engagement on top threads

For the top ~6-10 relevancy signalIds from Step 3, call `get_signal` on each. Capture
`platformScore` (upvotes), `numComments`, `context` (clean subreddit), and `title`.
Compute the median and max of `numComments` across them. These drive engagement depth
(Step 6) and populate the "live threads" list. Prefer threads whose titles name a
competitor or ask "best / alternative / vs" - those are the highest-value entries.

## Step 5: Pull brand mentions

Call `list_signals` with `projectId`, `category: "keyword-matches"`, `keyword:` = the
company's brand name, and a wide window (`after` = 12 months ago). Count the matches.
This is the brand presence axis. (Low weight, but pull it for real - don't estimate.)

## Step 6: Compute the six axes and the grade

Score each axis 0-100 using these buckets, then take the weighted composite.

| Axis | Weight | Bucket → score |
|---|---|---|
| **Demand volume** (relevancy signals >=0.75 in 90d) | 25% | 100+ →100 · 50-99 →85 · 25-49 →70 · 10-24 →50 · 3-9 →30 · <3 →10. If `hasMore` was true at the 100 cap, treat as 100+. |
| **Commercial intent** (hot-lead count, score >=0.85, 90d) | 20% | 40+ →95 · 20-39 →85 · 10-19 →70 · 4-9 →50 · 1-3 →30 · 0 →10 |
| **Competitor presence** (distinct competitors named in titles + competitor-owned subreddits + keyword matches) | 20% | 5+ competitors or 20+ mentions →95 · 3-4 →80 · 1-2 →55 · 0 →15 |
| **Community breadth** (distinct subreddits with a relevant thread, 90d) | 15% | 20+ →100 · 10-19 →80 · 5-9 →60 · 2-4 →40 · 1 →20 · 0 →0 |
| **Engagement depth** (numComments on top relevancy threads) | 15% | any 50+ or median >=15 →90 · top 20-49 or median 6-14 →70 · top 8-19 or median 3-5 →55 · top 3-7 →40 · <3 →20 |
| **Brand presence** (brand keyword matches, 12mo) | 5% | 20+ →90 · 8-19 →65 · 3-7 →40 · 1-2 →25 · 0 →10 |

`composite = 0.25*demand + 0.20*intent + 0.20*competitor + 0.15*community + 0.15*engagement + 0.05*brand`

Letter grade:
- **A** 85-100 — Reddit is a primary channel. Commit budget + weekly cadence.
- **B** 70-84 — Strong fit. Worth a real commitment; watch the weak axis.
- **C** 55-69 — Test lightly before scaling. Demand is there but thin.
- **D** 40-54 — Marginal. Only with spare cycles.
- **F** 0-39 — Not your channel yet. Spend the time elsewhere.

Write a 2-sentence verdict naming the strongest axes and the axis dragging the grade.

## Step 7: Disqualifier flag (caps the grade, never inflates)

Read the ICP `companySegment` values from Step 2. If the dominant buyer is **enterprise,
regulated (finance / healthcare / legal / government), or otherwise unlikely to discuss
tooling publicly**, raise the flag: cap the grade at **C**, and explain that demand may
exist but won't surface on Reddit. If ICPs are SMB / mid-market / founders / marketers /
agencies (audiences that post openly), mark the flag **Clear** and let the grade stand.

The two manual checks (participation rules, AI-answer presence) are rendered for the
reader to complete - never scored, never auto-run, never web-searched.

---

## Step 8: Render the .html report

Single self-contained file. No external JS. Only the Google Fonts CDN (Inter). Match the
Sensorhub brand exactly - all sans-serif, no serif or monospace display fonts.

### Design tokens (Sensorhub brand)

```css
:root{
  --page:#f8fafc; --card:#ffffff; --inset:#f1f5f9;
  --bd:#e2e8f0; --bd2:#cbd5e1;
  --tx:#1e293b; --tx2:#64748b; --tx3:#94a3b8; --navy:#0f172a;
  --accent:#6366f1; --accent-dim:rgba(99,102,241,0.08); --accent-bd:rgba(99,102,241,0.28);
  --sky:#e0f2fe; --sky-tx:#075985;
  --green:#16a34a; --green-dim:rgba(22,163,74,0.09);
  --amber:#d97706; --amber-dim:rgba(217,119,6,0.10);
  --red:#dc2626; --red-dim:rgba(220,38,38,0.08);
}
```

- Font: `Inter` (weights 400/500/600/700) for everything. Body text `--tx`, headings
  `--navy` 700, secondary `--tx2`, muted `--tx3`. Use `font-variant-numeric:tabular-nums`
  on all figures.
- Cards: white, `1px solid --bd`, radius 12-14px, shadow `0 1px 2px rgba(15,23,42,0.04)`.
- Bars: `--green` for strong axes (>=75), `--amber` for mid (40-74), `--tx3` for low (<40);
  the indigo `--accent` is for callout borders, the brand dot, and B-grade legend.
- Grade colors stay semantic: A `--green`, B `--accent`, C `--amber`, D `--tx2`, F `--red`.
  The grade hero tints its background with the matching `*-dim`.
- Competitor-owned subreddit chips use the `--sky` background with `--sky-tx` text.
- Animations: a `fadeUp` stagger on sections (`.d1`-`.d9`, 0.05s steps) and a `fillBar`
  keyframe so bars grow from 0 on load. Keep them subtle.

### Section order

1. **Header** — brand dot + "SENSORHUB" wordmark, `Reddit Fit Score` H1, meta row
   (company / category / window "Last 90 days" / date).
2. **Grade hero** — 2-col card: left = big letter + `NN / 100` + an "On the A/B line"
   chip only when the composite is within 2 points of a band edge; right = verdict
   (uppercase kicker + 1-2 sentences).
3. **Metric strip** — 4 cells: high-relevance threads/90d, distinct subreddits,
   competitors tracked & named, and one standout figure (e.g. threads naming the top
   competitor).
4. **Score breakdown** — one row per axis: name + weight, `NN / 100`, a filled bar, and
   one sentence of real evidence (cite actual counts and a real thread phrasing).
5. **Caveat callout** (amber) — only if keyword-match noise was filtered; name what was
   excluded and that the score ignores it.
6. **Manual checks** — two dashed cards ("Can you actually participate?" with the real
   top subreddits named; "Do these threads rank in AI answers?") each with a "verify" chip.
7. **Two-column** — top communities (chips with counts, competitor subs in sky) + the
   disqualifier check (green "Clear" or amber capped warning).
8. **Live threads** — 5-6 real threads: title, subreddit, relevancy %, and engagement
   (upvotes/comments) or an intent tag.
9. **Methodology** — the A-F bands and the composite formula, plus the line: "manual
   checks and the disqualifier can cap the grade but never inflate it, so an A always
   means real, reachable demand."
10. **Footer** — "Generated by sensorhub.ai · Reddit Fit Score · [N] signal sample · [date]".

Filename: `[company-slug]-reddit-fit-score.html`, saved in the current working folder (or
the outputs folder if your environment has one). Company slug = lowercase, spaces → hyphens.

## Step 9: Present

Share the file with the user (use `present_files` if your environment has it, otherwise give the path). Give a one-line summary: the letter grade,
the composite score, and the axis that decided it (e.g. "Acme: A (85/100) - maxed on
demand and competitor presence, held at the A/B line by engagement depth").

---

## Notes

- Single dashes (-) throughout, never em dashes.
- Sensorhub-only. No web search, no external fetch, no estimated numbers.
- Always apply the keyword-match noise filter before scoring demand and intent.
- The disqualifier and manual checks can lower a grade but never raise it.
- The report is one self-contained HTML file (Inter via Google Fonts is the only external load).
