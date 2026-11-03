# Template Tokens Reference

The HTML template at `assets/report-template.html` uses `{{TOKEN}}` placeholders
for every variable section. Replace each one with the data you prepared during
the workflow. This reference lists every token and the shape of the content
each one expects.

---

## Simple value tokens

These take a single string. Replace as-is.

| Token | Example value | Notes |
|-------|--------------|-------|
| `{{BRAND_NAME}}` | `SEMRush` | Used multiple times in the document |
| `{{PERIOD_START}}` | `19 Apr 2026` | Human-readable date |
| `{{PERIOD_END}}` | `19 May 2026` | Human-readable date |
| `{{REPORT_DATE}}` | `19 May 2026` | Today's date in the footer |
| `{{TOTAL_MENTIONS}}` | `1,595` | From project signalCount or 30-day sum |
| `{{PLATFORM_COUNT}}` | `3` | Number of platforms with non-zero coverage |
| `{{PLATFORMS_TEXT}}` | `Reddit, LinkedIn, X` | The platform list as comma-separated |
| `{{TOP_COMPETITOR_NAME}}` | `Ahrefs` | The competitor with the highest co-mention count |
| `{{TOP_COMPETITOR_COUNT}}` | `50+` | Use "50+" if the search cap was hit |
| `{{PROJECT_ID_SHORT}}` | `59519437...4653040a3c5` | First 8 + "..." + last 11 chars of UUID |

---

## Fourth stat card (flexible)

The fourth stat card adapts to the most interesting finding for this brand.

If a brand event was detected:
- `{{FOURTH_STAT_NUM}}` → e.g., `22`
- `{{FOURTH_STAT_LABEL}}` → e.g., `Adobe deal mentions`
- `{{FOURTH_STAT_CONTEXT}}` → e.g., `Acquisition coverage`

If no brand event, use the highest-priority intent bucket count:
- `{{FOURTH_STAT_NUM}}` → e.g., `9`
- `{{FOURTH_STAT_LABEL}}` → e.g., `Churn signals`
- `{{FOURTH_STAT_CONTEXT}}` → e.g., `Save-the-customer threads`

---

## Prose tokens (Claude writes these)

These take a short paragraph of plain English. Keep concise (2-4 sentences).

**`{{LEDE_PARAGRAPH}}`**: The 90-second read. Lead with the single most
interesting finding. Mention the brand event if there was one. Mention the top
competitor. Mention the dominant intent bucket. Example:

> SEMRush is in the middle of a structural brand moment. The Adobe acquisition,
> completed 28 April for $1.9B, dominates the LinkedIn conversation. Reddit
> conversations are mostly buyers asking for help and a meaningful share of
> users looking to leave for cheaper alternatives. Ahrefs is the single most
> co-mentioned competitor by a wide margin.

**`{{PLATFORMS_PARAGRAPH}}`**: What each platform is doing for this brand. Two
sentences max per platform.

**`{{TOP_CHANNELS_NOTE}}`**: A one-liner naming the top subreddits / hashtags /
authors driving the conversation. Example: "Subreddits driving the most
mentions: r/SEO, r/DigitalMarketing, r/localseo."

**`{{SOV_NOTE}}`**: A short bolded interpretation of the SOV chart. Lead with
"Read this carefully" or a similar framing. Call out search caps if any
competitor hit 50.

**`{{INTENT_REMAINDER_NOTE}}`**: One sentence noting how many signals in the
sample were "general industry mention" and not classified into the six buckets.

---

## Platform bars (HTML chunk)

`{{PLATFORM_BARS}}` expects one `<div class="bar-row">` per platform that has
non-zero coverage. Each looks like:

```html
<div class="bar-row">
  <div class="bar-label">Reddit</div>
  <div class="bar-track"><div class="bar-fill" style="width: 100%;"></div></div>
  <div class="bar-value">40 / 40</div>
</div>
```

Width should reflect the relative count per platform within the sample. If
all platforms are at 40, set all widths to 100%.

---

## SOV bars (HTML chunk)

`{{SOV_BARS}}` expects one `<div class="bar-row">` per competitor, plus one
for the brand itself at the top. The brand row uses `class="bar-fill sov-bar brand"`
and the competitors use `class="bar-fill sov-bar"`.

```html
<div class="bar-row">
  <div class="bar-label">BrandName</div>
  <div class="bar-track"><div class="bar-fill sov-bar brand" style="width: 100%;"></div></div>
  <div class="bar-value">1,595</div>
</div>
<div class="bar-row">
  <div class="bar-label">Ahrefs</div>
  <div class="bar-track"><div class="bar-fill sov-bar" style="width: 100%;"></div></div>
  <div class="bar-value">50+</div>
</div>
```

Width is relative to the highest competitor count, not to the brand count
(otherwise competitor bars would all be tiny). Use the highest competitor as
the 100% reference.

---

## Intent grid (HTML chunk)

`{{INTENT_GRID}}` expects six `<div class="intent-card">` blocks, in this order:

1. Buying intent (category) - add `class="intent-card priority"`
2. Comparison - add `class="intent-card priority"`
3. Churn / displacement - add `class="intent-card priority"`
4. Pricing complaint - regular
5. Win / testimonial - regular
6. Feature / product feedback - regular

Each card looks like:

```html
<div class="intent-card priority">
  <div class="intent-name">Buying intent (category)</div>
  <div class="intent-count">22</div>
  <span class="intent-sentiment sent-pos">Opportunity</span>
  <ul class="intent-threads">
    <li>
      <a href="THREAD_URL">Thread title here</a>
      <span class="intent-source">Reddit r/aeo</span>
    </li>
    <!-- 2-3 more li elements -->
  </ul>
  <div class="intent-more">+ 18 more in this bucket</div>
</div>
```

Sentiment classes: `sent-pos` (green), `sent-neg` (red), `sent-neu` (gray).

Source label format:
- Reddit: `Reddit r/subreddit`
- LinkedIn: `LinkedIn - Author Name`
- X: `X - @handle`

If a bucket has fewer than 3 threads, show only what you have and drop the
`intent-more` line.

---

## Action items (HTML chunk)

`{{ACTION_ITEMS}}` expects 6-8 action item blocks. Each one looks like:

```html
<div class="action-item">
  <div class="action-meta">
    <span class="tag tag-platform">Reddit</span>
    <span class="tag tag-intent">Buying intent</span>
    <span class="tag tag-priority">Hot lead</span>
  </div>
  <div class="action-title"><a href="THREAD_URL">Thread title</a></div>
  <div class="action-snippet">"First ~200 chars of the snippet text..."</div>
  <div class="action-suggest"><strong>Suggested angle:</strong> One specific sentence about how to respond, tailored to this thread.</div>
</div>
```

Priority tag values:
- `Hot lead` for buying intent threads
- `Save the customer` for churn signals
- `Engage in comments` for comparison threads
- `Address pricing` for pricing complaints

---

## Brand event section (full HTML block or empty)

`{{BRAND_EVENT_SECTION}}` is either an empty string (no event detected, no
section rendered) or this full HTML block:

```html
<div class="section">
  <div class="section-label">Brand event</div>
  <h2>Brand event watch</h2>
  <div class="event-card">
    <div class="event-tag">Major event this period</div>
    <div class="event-title">EVENT TITLE HERE</div>
    <p style="color: var(--ink-2); font-size: 14px;">EVENT NARRATIVE - 2-4 sentences sourced from real signals. Quote at least one specific post or author.</p>
    <div class="event-stats">
      <div>
        <div class="event-stat-num">NUMBER</div>
        <div class="event-stat-label">Mentions of the event across all platforms</div>
      </div>
      <div>
        <div class="event-stat-num">PERCENT</div>
        <div class="event-stat-label">Positive or neutral framing on LinkedIn</div>
      </div>
      <div>
        <div class="event-stat-num">NUMBER</div>
        <div class="event-stat-label">Threads questioning or critical of the event</div>
      </div>
    </div>
  </div>
  <p style="font-size: 13px; color: var(--ink-3);">The skill flagged this as a brand event because a single topic exceeded 15% of the period's high-relevance signal. Brand events are surfaced separately from steady-state mention activity so they don't distort the SOV or intent baselines.</p>
</div>
```

---

## Sanity checklist before saving the HTML

- Every `{{TOKEN}}` has been replaced (search the rendered file for `{{` to
  catch leftovers).
- All thread URLs in the report are real Sensorhub-sourced URLs, not
  fabricated.
- Brand name spelling matches across the report (capitalization is from the
  project record, not what the user typed).
- The "+ N more" line in each intent card uses the actual bucket count minus
  what was shown, not a made-up number.
