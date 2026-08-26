---
name: "price-monitor"
description: "Recurring price monitoring workflow for a defined product list. Use this skill whenever  the user wants to track prices over time, check if something they're watching has dropped,  run a price check-in against a baseline, or monitor a set of products for buy-signal  thresholds. Triggers on: \"check the prices\", \"has anything dropped\", \"price update\",  \"should I buy now\" (for products already being tracked), \"monitor these products\",   \"price alert\", \"run the price check\", or any request to compare current prices against   a known baseline. Also use when the user mentions a recurring price-watching task or asks  to re-run a previous price check. If someone has a list of products and wants to know if  now is a good time to buy, use this skill. For net-new \"what should I buy / which is  better\" decisions with no baseline, use consumer-purchase-research instead."
---

# Price Monitor

**Created by Josie Lagarde, designed with Claude (Cowork)**

**License:** Released under CC BY 4.0 — free to use, adapt, and share with
attribution.

A structured workflow for recurring price checks against a defined product list and
baseline. Designed to run on-demand or as a scheduled task.

---

## When to Use This Skill

Use this skill for any price monitoring task — whether it's a one-time check ("are solar
battery prices lower than last month?") or a recurring scheduled run. The workflow is the
same either way; the difference is just whether you're comparing against a stored baseline
or a baseline provided in the conversation.

Boundary with consumer-purchase-research: this skill tracks known products against a
known baseline over time. If the user is still deciding *what* to buy (comparing options,
no baseline exists), that's consumer-purchase-research; it can hand off here once a
watch-list exists.

---

## The Core Pattern

**Define → Fetch → Compare → Report → Recommend**

---

## The Baseline File

Recurring monitoring needs one canonical place to load from and save to. Convention:
a single markdown file per watch-list, stored in the relevant project folder and named
`price-baseline-[topic].md` (e.g. `price-baseline-solar-battery.md`). Agree the exact
path with the user on first setup and record it in the scheduled task's prompt if one
exists.

```markdown
# Price Baseline — [topic]

**Threshold:** [e.g. ≥5% drop → ACTION]
**Report destination:** [message only | saved report file at path | both]
**Last updated:** YYYY-MM-DD

| Product (exact model/spec) | Baseline $ | Baseline date | Source URL | Notes |
|---|---|---|---|---|
```

- **Load:** at the start of every run, read this file. If it doesn't exist and the user
  hasn't supplied a baseline in-conversation, treat the run as first-time setup: confirm
  the product list, capture current prices as the initial baseline, and create the file.
- **Save:** after each run, offer to update the baselines to today's prices (see Saving
  the Baseline below). Update `Last updated` whenever the file changes.
- Product rows must name the exact model/spec — "solar battery" is not a product;
  "EcoFlow DELTA Pro 3.6kWh" is.

---

## Phase 1: Define the Product List and Baselines

At the start of a monitoring run, establish:

- **Product list** — the specific products or product categories to check
- **Baseline prices** — the reference prices to compare against (last check, purchase
  target, or historical average)
- **Alert thresholds** — what counts as a meaningful price movement. If the user has
  never set one, use **≥5% drop from baseline → ACTION** as the default, state that
  you're using it, and record it in the baseline file so future runs are consistent.
- **Context** — any known factors that affect prices (seasonal patterns, supply constraints,
  recent market events)

**Rows with no baseline.** A product added to the list between runs has no baseline row at
all. Never write a baseline-less product's cells as if they were observed values (an em dash
or explicit "not yet checked" — never a zero, a blank, or a carried-over figure): "nobody has
looked" and "the price didn't move" are opposite findings, and collapsing them makes the
report silently confident about something it has never measured. A baseline-less row is a
**first check that always reports**, exempt from the only-report-changes filter, and the run
log notes which rows were first checks.

**Criteria go stale too, not just prices.** The baseline file records the requirement set
that produced the product list. On a revisit, re-read it: if the user's budget and the
must-have list are in conflict, check whether the must-have list is negotiable before
reporting that nothing qualifies. Re-examining the requirement set is
consumer-purchase-research's Phase 1 job — hand off rather than deciding it here — but this
skill is usually the one that notices, because it is the one re-reading the baseline.

If a baseline file or prior check exists, load it. If this is a new run, ask the user to
provide or confirm the list before fetching prices (in an unattended scheduled run with no
baseline file, stop and report that setup is needed — don't invent a baseline).

---

## Phase 2: Fetch Current Prices

For each product, attempt a **direct fetch** of the retailer/product page first
(`web_fetch`) — this gets the actual current page rather than a search snippet, and is
the more reliable source when it works. Fall back to web search only if the domain is
egress-blocked or the fetch otherwise fails. Don't assume fetching is blocked by
default — availability can vary across sessions, so check each time rather than
skipping straight to search. Note which path was used for each product (direct fetch
vs. search fallback); this affects how much to trust the figure and is worth recording
in the report.

Either way, prices change frequently and training data will be stale — never rely on
memorized prices for either path.

**Ordered fallback chain — and a floor.** Try in order; do not improvise a further
option mid-run: (1) direct `web_fetch` of the product page; (2) web search for the
price; (3) **STOP** and report what was and was not checked. A fetch failure is not a
price. If a product could not be reached, its row reads NO DATA / could-not-check — never
the previous baseline price, never a memorized price, never omission from the table.
Before blaming a retailer, fetch a control URL (e.g. `https://example.com`): if that also
fails, the fetch layer is down — say which it was.

**"Could not check" and "price unchanged" are opposite findings that look identical.**
An unreachable product silently carried forward at its baseline price reads as a stable
price and suppresses an alert that may have been due. Render the two differently in every
report, and list unchecked products explicitly rather than letting them fall out of the
change filter.

**For each product, capture:**
- Current price (and retailer/source)
- Date of the price (confirm it's current, not cached)
- Which fetch path was used (direct fetch or search fallback)
- Any relevant context: sale/promo flags, stock availability, model changes

**Discontinued or superseded products:** if a tracked product is no longer sold, mark it
NO DATA — never silently substitute a different SKU's price into its row (a successor
model's price against the old model's baseline is a meaningless delta). Note the apparent
successor model and its price in the report's context section, and suggest updating the
baseline file to track the successor with a fresh baseline.

For commodity-like products (batteries, panels, materials), also note:
- Market context — are prices trending up/down/flat?
- Any known supply or demand factors that explain the current level

---

## Phase 3: Compare Against Baseline

For each product, calculate:
- **Delta (absolute):** Current price − Baseline price
- **Delta (%):** (Current − Baseline) / Baseline × 100
- **Status:** Flag as ACTION (threshold crossed), WATCH (movement but below threshold),
  or HOLD (within normal range)

Threshold logic:
- If delta % ≤ −threshold → **ACTION** (price dropped enough to buy)
- If delta % is between −threshold and 0 → **WATCH** (moving in the right direction)
- If delta % > 0 → **HOLD** (price is up from baseline)
- If data is unavailable → **NO DATA** (note the gap explicitly)

**Alert dedup — fire on entry into a tier, not on residence in it.** Every alert-emitting
action (a calendar ping, a notification, an interrupt-worthy callout) needs an explicit
"was this product already in this tier last run?" check against the immediately previous
run's history rows: fire on the run where it *crosses in*, then suppress while it sits
there. Without this, a product parked below threshold at an unchanged price re-fires the
same alert every run indefinitely — the report says the identical thing each week and the
alert stops carrying information. Apply the check to **every** tier that emits an alert;
a dedup rule written for one tier and not the others is the common failure. Re-fire only
when the product exits and re-crosses, when the delta worsens materially past the level
that triggered the last alert, or after a stated cooldown. If repeat alerts on an
unchanged price are genuinely wanted for some tier, say so explicitly in the rule text so
a later session doesn't read it as an oversight and 'fix' it.

---

## Phase 4: Build the Report

Deliver results as a structured table, then a narrative summary.

**Report table columns:**
| Product | Baseline | Current | Δ$ | Δ% | Status |
|---------|----------|---------|----|----|--------|

Sort by status priority: ACTION first, then WATCH, then HOLD, then NO DATA.

**After the table, include:**
- **Market context:** 2–4 sentences on broader price trends or relevant news
- **Data gaps:** Any products where current pricing couldn't be verified, and any
  discontinued/superseded products with the suggested successor
- **Threshold note:** Confirm what threshold was used for ACTION/WATCH/HOLD classification

---

## Phase 5: Give a Buy/Hold Recommendation

Synthesize the report into a clear recommendation:

- **Buy now:** If any items are at ACTION status — name them and explain why the threshold
  matters (e.g., this price hasn't been seen in 6 months)
- **Watch:** If items are trending down but haven't crossed the threshold — note whether
  the trend suggests waiting a short period could be worthwhile
- **Hold:** If prices are flat or elevated — say so directly and note what would change
  the recommendation
- **Act on context:** If market factors suggest a window opening or closing (seasonal,
  supply shock, etc.), flag it even if no threshold is crossed

**Tone:** Be direct. The user is running this check because they want to know whether to
act. Give them a clear answer.

---

## Saving the Baseline for Future Runs

After completing a monitoring run, offer to save the current prices as the new baseline
in the baseline file — comparing against last month beats comparing against six months
ago. Two caveats:

- In an **interactive** run, update only with the user's OK.
- In a **scheduled** run, follow whatever the baseline file's own notes say; if silent,
  leave baselines unchanged and note in the report that they're aging (rebaselining
  automatically would silently move the goalposts an ACTION threshold is measured
  against).

---

## Output Format

For a full monitoring run:
1. Report table (sorted by status)
2. Market context paragraph
3. Buy/Hold/Watch recommendation

For a quick one-product check ("has the Powerwall dropped?"):
Skip the table, give a direct answer with current price, delta, and recommendation.

**When the headline finding is an absence**, say what the search covered. A NO DATA
row, "could not verify current pricing for X", or a whole run that reached nothing is
a claim about the *check*, not about the world — and it is only as trustworthy as the
check that produced it. Name three things: (1) the sources actually fetched and read;
(2) the sources blocked or unreachable, **named individually, with what they would
have told us** — "the manufacturer's own page timed out; that is where the list price
lives"; (3) evidence classes deliberately not checked — "retailer listings only; the
manufacturer's direct store was not queried". The third is the one that gets skipped
and the one most likely to hide the real answer.

Phrase it as "I could not reach a current price for X", never "X is unavailable" or
"the price has not changed" — those are different findings that look identical in a
table (see the data-gap rules above).

Scale this to the finding. One SKU showing NO DATA in an otherwise-normal run needs a
clause on that row, not a section — "NO DATA: retailer page 403'd, manufacturer site
not checked". A run where most products could not be reached, or a one-product check
that came back empty, needs the three buckets written out properly, because there the
absence *is* the result being delivered.

**Destination:** interactive runs deliver in-conversation. Scheduled runs deliver
according to the baseline file's `Report destination` line; if unset, put the full
report in the run's final message and lead with the single most actionable line
("ACTION: [product] down 8% to $X" or "No buy signals this run"). If the user wants
an archive across runs, offer an Excel file (the xlsx skill handles this) and record
that choice in the baseline file.

---

## Running as a Scheduled Task

This skill is designed to work as a recurring scheduled check. When setting up a
scheduled run:

1. Create the baseline file (see The Baseline File above) and reference its exact path
   in the task prompt
2. Set a clear alert threshold so results are actionable without manual interpretation
3. Record the output destination in the baseline file — a saved report file, a summary
   message, or both
4. Apply the web_fetch-first / search-fallback rule from Phase 2 on every run. This
   matters more here than in an interactive check: an unattended run has no one watching
   to notice if it silently defaulted to stale search snippets instead of attempting a
   direct fetch. Record which path was used for each product in the saved report so a
   later review can spot a fetch path that's degraded over time.

The schedule skill can help set this up if the user wants automated runs.

---

## Common Pitfalls to Avoid

- **Stale prices.** Always get current prices — direct fetch first, search fallback
  second. Never use training data for pricing.
- **Missing market context.** A 10% price drop that reflects a broader market collapse is
  different from a retailer-specific sale — context determines whether the signal is real.
- **Threshold drift.** If the baseline is never updated, the threshold comparisons become
  meaningless over time. Offer to update the baseline after each run (with consent — see
  Saving the Baseline).
- **Substituting SKUs.** A successor model's price is not the tracked product's price —
  mark NO DATA and propose a rebaseline instead.
- **Ambiguous product specs.** "Solar battery" might mean a 10kWh home battery or a
  portable camping unit — confirm the exact product before fetching prices.
- **Burying the recommendation.** Tell the user upfront whether they should act. Don't
  make them decode the table themselves.
- **Assuming fetch is blocked.** Don't skip straight to web search out of habit — try
  the direct fetch first each run, since egress availability can change between sessions.

---

## Related skills

**In this pack:**
- `consumer-purchase-research` — deciding *what* to buy, when there is no baseline yet.

Boundary notes, not requirements — each works standalone.

**Not included here:** a scheduling capability, if you want recurring automated runs, and a spreadsheet skill for exporting price history.

