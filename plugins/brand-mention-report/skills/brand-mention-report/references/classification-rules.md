# Intent Classification Rules

Every signal in the 120-signal sample gets assigned to exactly one of these six
buckets, or tagged "general industry mention" if none fit. Use the title +
snippet text to decide. Real examples from the SEMRush test run are included
below each bucket so you can calibrate.

---

## Bucket 1: Buying Intent (category)

**Sentiment label:** Opportunity
**Priority card:** Yes (highlighted in the intent grid)

Asking for recommendations in the category without naming the brand yet. The
asker is in evaluation mode and the brand has a chance to be named in a reply.

**Cues:**
- "What's the best X for Y"
- "Looking for recommendations"
- "Anyone using X for Y"
- "Best [category] tool in 2026"

**Examples:**
- "What are some of the best AEO/GEO platforms that you've used?"
- "Best AI visibility tools for B2B sites? Need to improve ChatGPT mentions"
- "What's the best SEO reporting software you've actually used?"

**Do NOT classify here:**
- Threads that name the brand directly (those are likely Comparison or
  Feature feedback)
- "How do I do X" tutorial questions (those are general industry mention)

---

## Bucket 2: Comparison

**Sentiment label:** Neutral
**Priority card:** Yes

Multiple named brands in the same thread, framed as a "vs" or stack
recommendation. The asker is comparing options or asking others to compare.

**Cues:**
- "X vs Y"
- "X vs Y vs Z"
- "Which is better, X or Y"
- "My stack: X, Y, Z. Should I add A?"

**Examples:**
- "Ahrefs vs SEMrush vs Screaming Frog vs Moz Pro - what's your actual stack?"
- "SEMrush vs Ahrefs vs SEranking for SEO clients"
- "Ahrefs vs Semrush vs Mangools 2026"

**Do NOT classify here:**
- "Looking for an alternative to X" - that's Churn / displacement
- A single comparison article republished by the author - if it's promotional
  content from the brand or an affiliate, it's a Win / testimonial

---

## Bucket 3: Churn / Displacement

**Sentiment label:** Risk
**Priority card:** Yes

The asker is currently using the brand or considers it the default and is
actively looking to leave or replace it. Highest-leverage save-the-customer
moment in the entire report.

**Cues:**
- "Looking for an alternative to X"
- "Looking to replace X"
- "Switching from X to Y"
- "X killer" / "Replace X with Y"
- "Cheaper than X"

**Examples:**
- "Looking for tools to replace Semrush"
- "What's a good alternative to SEM rush for SEO analysis and GEO ranking checks?"
- "Holy shit... Someone just open sourced the Semrush killer"

**Edge case:**
A tweet declaring a "Brand X killer" is a churn signal in tone even if no
single user is switching yet. It is still a competitive threat worth surfacing.
Classify here.

---

## Bucket 4: Pricing Complaint

**Sentiment label:** Risk
**Priority card:** No (regular card)

Explicit price concern naming the brand or category. Often overlaps with
Churn / displacement but is distinct: the asker is complaining about price
without necessarily looking to leave.

**Cues:**
- "Too expensive"
- "X at $Y/month is way out of my price range"
- "Worth the cost?"
- "Affordable alternative" / "Cheaper option"

**Examples:**
- "Do you guys still think expensive SEO tools are worth it now?"
- "Semrush or Ahrefs at 120/month is way out of my price range"
- "Anyone here actually using cheaper SEO tools for local clients?"

**When in doubt:** If the asker is current customer leaving → Churn.
If the asker is price-shopping in the category → Pricing complaint.

---

## Bucket 5: Win / Testimonial

**Sentiment label:** Positive
**Priority card:** No

Positive citation of the brand, case study coverage, brand-affiliated content,
or a third-party recommendation. Use this carefully - if the author is a brand
employee, affiliate, or partner, the signal is still a Win but tag it
internally and weight it lower than third-party signals.

**Cues:**
- Citing the brand's research or study favorably
- Webinar collaboration or co-marketing post
- Employee announcement (e.g., "joined Brand as VP")
- Customer story / case study

**Examples:**
- "Rethinking SEO for AI-Driven Search" (cites Semrush's AI Search study)
- "Entity SEO and AI Search Strategies with Semrush" (webinar)
- "AI Adoption Trends in Search with Datos and SparkToro" (Datos is a Semrush
  subsidiary)

**Honesty rule:** Affiliated wins should make up no more than half of the
Win bucket examples in the rendered report. If they do, surface that ratio in
the methodology footnote.

---

## Bucket 6: Feature / Product Feedback

**Sentiment label:** Neutral
**Priority card:** No

Feature requests, workarounds, "how does X handle Y" questions, plan-tier
questions, cancellation questions. The user is engaged with the product
itself.

**Cues:**
- "X workaround"
- "Does X support Y"
- "Best X plan for Y"
- "Cancellation question"
- "Trial details"

**Examples:**
- "SEMrush export workaround?"
- "Do listings disappear if I cancel Local SEO/citation service?"
- "Best SEMrush Plan for SEO and AEO"

---

## What does NOT fit any bucket

Tag these as "general industry mention" and count them only in the methodology
footnote, not in the intent grid:

- News articles mentioning the brand in passing
- Industry thought leadership that namechecks the brand among others
- "Best tools in 2026" listicles
- Brand mention as a competitor in a different category report

These signals still matter for brand presence tracking but are too low-signal
to drive any action.

---

## Sentiment posture per bucket

These labels feed the colored chip in each intent card:

| Bucket | Sentiment chip |
|--------|---------------|
| Buying intent (category) | Opportunity (green) |
| Comparison | Neutral (gray) |
| Churn / displacement | Risk (red) |
| Pricing complaint | Risk (red) |
| Win / testimonial | Positive (green) |
| Feature / product feedback | Neutral (gray) |

These are fixed by bucket, not derived per-signal. The sentiment of the
individual signal can vary within a bucket - that's expected and noted in
the methodology section.
