---
name: "consumer-purchase-research"
description: "Structured research workflow for purchase decisions and financial product comparisons. Use this skill whenever the user is trying to decide whether to buy something, compare competing products, evaluate a financial product (credit card, insurance, loan), or needs help making a \"should I get X or Y\" decision. Triggers on: \"help me choose\", \"should I buy\", \"compare these options\", \"which is better\", \"cost-benefit\", \"is it worth it\", or any request to research and recommend a product or service. Also use when the user has a list of options and wants a structured comparison — even if they don't explicitly ask for \"research.\" If someone brings you a purchase decision, use this skill. Exception: if the user is checking current prices against a baseline they're already tracking (\"has it dropped\", \"should I buy NOW\"), that's the price-monitor skill, not this one."
---

# Consumer Purchase Research

**Created by Josie Lagarde, designed with Claude (Cowork)** · Created: 2026-04-30

**License:** Released under CC BY 4.0 — free to use, adapt, and share with
attribution.

A structured methodology for research-backed purchase decisions. Covers physical products,
subscriptions, financial products (credit cards, insurance, loans), and service comparisons.

Boundary with price-monitor: this skill answers "what should I buy?" — a net-new decision
with no baseline. Once a decision is "wait for a better price," hand off to price-monitor
to track the chosen product against a baseline.

---

## The Core Pattern

Most purchase decisions fail at the same points: criteria aren't defined upfront, options
are compared on incompatible terms, or hidden costs undermine the headline price. This
skill fixes all three by front-loading clarity and building a structured comparison before
making a recommendation.

The workflow has four phases: **Clarify → Research → Compare → Recommend.**

---

## Phase 1: Clarify Criteria

Before researching anything, nail down what matters. Ask or extract:

- **Budget** — hard ceiling, or soft target? One-time vs. ongoing cost?
- **Must-haves** — features/attributes that make an option non-viable if absent
- **Nice-to-haves** — things that would be good but aren't dealbreakers
- **Context** — who uses it, how often, under what conditions?
- **Comparables already considered** — what has the user already looked at or ruled out?

If the user has given you this information already (in the conversation or in a document),
extract it rather than asking again. Only ask about gaps.

**Revisits: prior criteria are inputs to re-examine, not settled constraints.** When the task
is a revisit and a criteria set already exists on file (a baseline doc, an earlier
comparison), the requirement list is as stale as the prices — and staler in effect, because a
wrong price shifts a recommendation by a percentage while a wrong must-have deletes whole
tiers of the market before comparison begins. So:

- Identify the **most constraining must-have** — the one eliminating the most otherwise-
  qualifying options, or setting the price floor.
- State explicitly what the field and the entry price look like **with that requirement
  relaxed**, even if you end up keeping it.
- If the constraining must-have is what pushes the recommendation past the user's budget, say
  that directly. Never report "nothing affordable qualifies" when the actual finding is "one
  requirement is pricing you out."
- Criteria age: a must-have recorded months ago may reflect a since-solved problem, or an
  assumption the user would drop if shown the price of holding it.
- Options a prior run explicitly excluded from tracking get re-checked too — an exclusion is
  a criteria decision, and it expires with the criteria that produced it.

**In autonomous/scheduled runs** (no user present to answer): don't stall on missing
criteria. State the assumptions you're making explicitly at the top of the output, proceed,
and note which assumptions most affect the recommendation so the user can correct them.

**Why this matters:** Skipping criteria definition leads to researching the wrong things.
A user who says "compare lawn mowers" might actually need a robotic mower for a large
irregular yard — and the best manual push mower is irrelevant to them.

---

## Phase 2: Research Options

Search for options that plausibly meet the stated criteria. Aim for 3–5 options — enough
to show meaningful trade-offs without overwhelming the user.

**Research checklist:**
- Current pricing (not cached/stale — use web search or direct page fetch)
- Key specs or features relevant to the user's must-haves and nice-to-haves
- Total cost of ownership where relevant (setup fees, ongoing costs, maintenance, accessories)
- User/expert reviews or ratings — look for patterns, not just averages
- Known limitations or common complaints
- Availability or lead time if relevant

**Source quality rules:**
- **Recency:** check the date on every price, rate, or fee. Treat anything older than
  ~30 days as unverified — re-check it or flag it as possibly stale in the comparison.
  Spec details age slower; prices and financial terms age fast.
- **Independence:** weight review patterns that recur across independent sources. Discount
  a consensus that exists only on affiliate/referral sites or in suspiciously uniform
  review batches — name the discount in your notes if it changes an option's standing.
- **Regional/contextual match:** when a product's performance depends on local conditions
  (climate, terrain, grass/soil, water, altitude, regulation, utility rates), ask whether the
  sources were produced under conditions matching the user's. A source can be independent,
  current, and still useless because it was generated somewhere that doesn't resemble where
  the user lives. Before accepting a category consensus that turns on a local condition,
  actively search for a **regionally-matched independent source** — university agricultural
  extension services, state agencies, regional trade bodies — rather than more product
  reviews. This is the search that breaks a manufactured consensus: the correcting evidence
  usually exists, but never appears in the obvious product query.

**Verification gate on negative findings — never report absence from a narrow query.**
A negative result from a search is evidence about the search, not about the world.
Before writing "no product does X", "brand Y doesn't make one", or "not available here",
run at least one *broader* query — manufacturer, category, series, or brand — and confirm
the item does not appear there either. The absence claim is sourced from the broad sweep,
never from the narrow per-item lookups, because a search miss and a true absence are the
same string in the output. Positives largely self-verify; negatives never do, so spend
verification effort asymmetrically.

For **financial products** (credit cards, insurance, loans):
- Verify current rates/fees from the provider directly — these change frequently
- Look for introductory vs. standard terms (teaser rates, annual fee waivers)
- Calculate the break-even point for benefits vs. costs at the user's realistic usage level
- Note eligibility requirements

---

## Phase 3: Build the Comparison

Organize what you found into a structured comparison. A table is usually the right format.

**Comparison table columns (adapt to the domain):**
- Option name
- Price (and pricing model if relevant)
- Meets must-haves? (yes/no/partial for each)
- Key strengths
- Key weaknesses or gaps
- Best for (the scenario where this option wins)

For financial products, add:
- Effective annual cost/benefit at the user's actual usage
- Break-even or payback calculation

**After the table, note:**
- Any options that were disqualified and why (so the user knows you considered them)
- Data gaps or things you couldn't verify — be explicit about uncertainty
- Hidden costs or caveats that don't fit neatly in the table

### Gut-Check for Unquantifiable Non-Financial Costs

Some decisions carry a real non-financial cost — time, energy, stress, dread — that the
user can name but genuinely can't price. Don't discard it for lack of precision, and don't
manufacture false precision by forcing a dollar estimate onto it. Instead, use a
relief-vs-loss framing: ask the user to imagine *not* proceeding with the purchase or
commitment — does that feel like relief, or like loss/regret? That answer converts a
subjective, effort-based cost into a directional signal without pretending it's a number.

Combine this with the financial math from Phase 3/4: when both the payback numbers and the
gut-check point the same direction, the recommendation is stronger than either alone. When
they conflict, say so explicitly in the recommendation — that tension is information the
user needs, not something to smooth over.

---

## Phase 4: Make a Recommendation

Give a clear recommendation — don't hedge into "it depends" without a follow-up answer.
"It depends" is acceptable only if followed immediately by "here's what to do depending
on your situation."

**Recommendation structure:**
1. **Top pick** — name it and give a one-sentence reason
2. **Runner-up** — the best alternative for a different set of priorities (e.g., tighter
   budget, different use case)
3. **When to skip all of these** — if none of the options clearly fits, say so and suggest
   what the user should look for instead
4. **Caveats** — any assumptions underlying the recommendation, or things that could change it

If the recommendation is "wait" (prices trending down, new model imminent), offer to set
up price monitoring on the top pick via the price-monitor skill so the wait has an exit
condition.

**Tone:** Be direct. The user came for a recommendation, not a list of considerations.
If one option is clearly better for their stated criteria, say so.

---

## Output Format

For a full research task, deliver:
1. A brief criteria summary (confirms you understood the decision; in autonomous runs,
   this is where stated assumptions go)
2. The comparison table
3. The recommendation section
4. Sources inline (links to product pages, reviews, or financial product pages used),
   with the date each price/term was verified
5. A short **source-quality note** — which sources were relied on, which were discounted, and
   why. This belongs in the deliverable as visible reasoning, not in private notes: when a
   single source is doing enough work to flip the recommendation, the user is entitled to see
   that it was load-bearing and that alternatives were weighed.

**If the headline finding is an absence** ("no product does X", "that feature isn't
available"), add a short **what I searched** section with three named buckets: (1) sources
fetched and read; (2) sources blocked or unreachable, named individually, with what they
would have told us; (3) evidence classes deliberately not checked (e.g. "documentation only;
the in-app UI was not inspected"). Bucket 3 is the one that gets skipped and the one that
most often hides the real answer. Phrase every negative as "I could not find X," never "X
does not exist" — an absence is only as trustworthy as the search that produced it.

For simpler questions ("is X worth it for someone in my situation?"), skip the full table
and go straight to a direct answer with supporting evidence.

If the user will want to save or share the comparison, offer to create an Excel file with
the table — the xlsx skill handles this.

---

## Common Pitfalls to Avoid

- **Stale pricing.** Always search for current prices — never rely on training data for costs.
- **Spec-matching without context.** The option with the best specs isn't always the right
  answer if it's overkill for the user's actual usage.
- **Ignoring total cost of ownership.** A cheap option with high ongoing costs often loses
  over time — calculate it out.
- **Trusting a manufactured consensus.** Affiliate-heavy or uniformly-worded reviews are a
  signal to verify elsewhere, not evidence.
- **Burying the recommendation.** Put the top pick first, not last. The user shouldn't have
  to read to the end to find out what you recommend.
- **Over-hedging.** Qualifications are important, but they shouldn't swamp the recommendation.
  Name the winner, then add the caveats.

---

## Related skills

**In this pack:**
- `price-monitor` — tracks an already-chosen product's price against a baseline.

These are boundary notes, not requirements. Each skill works on its own; the cross-references only tell you which one fits a given question.

**Not included here:** a spreadsheet skill, if you want comparison tables exported to Excel rather than rendered in the conversation.

