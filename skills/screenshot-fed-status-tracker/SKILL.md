---
name: "screenshot-fed-status-tracker"
description: "Use when an app, device, or vehicle exposes status data with no legitimate API — or where API access would mean storing the user's account credentials in an unofficial library — and the user wants that data tracked over time from screenshots they send periodically. Triggers on 'here's a screenshot from the app', 'track my odometer / oil life / tire pressure', 'log this reading from the app', 'set up a tracker for this app or device', 'what did it say last time', or any request to keep a running record of an app's readings without connecting an account. Returns one markdown tracker file: a Needs Action section at the top, a Baseline block of current values, and an append-only History table — plus a flagged list of anything due, overdue, or out of range. Never books, orders, schedules, or otherwise acts on what it finds. Exception: tracking a product's price against a buy-signal baseline is the price-monitor skill, not this one."
---

# Screenshot-Fed Status Tracker

**Created by Josie Lagarde, designed with Claude (Cowork)** · Created: 2026-08-17

**License:** Released under CC BY 4.0 — free to use, adapt, and share with
attribution.

A pattern for tracking a device's or service's status over time when the only
safe data path is **the user periodically sending a screenshot**. The agent
transcribes what is visible, appends it to a structured log, flags what needs
attention, and does nothing else.

---

## When this is the right pattern

All three conditions, together:

1. **No legitimate API exists** for the app or device (or none the user has
   access to).
2. The only alternative integration would require **storing the user's
   account credentials** — typically in an unofficial, reverse-engineered
   library. That is disqualifying on security grounds; do not propose it, and
   do not accept it if offered.
3. The data is **worth tracking over time** — readings that change slowly and
   have thresholds (mileage, wear percentages, pressures, expiry dates,
   balances, firmware versions, open recalls).

If any of those fail, this is the wrong pattern. If the tracked quantity is a
purchase price being watched for a buy signal, use `price-monitor` instead —
it has threshold and buy-signal logic this skill deliberately lacks.

**Say the reasoning out loud once, in the tracker file itself.** "No API
exists; unofficial libraries would require credential storage; screenshots
are the safe substitute." It is the same decision every time, and recording
it stops a future session from quietly wiring up an unofficial integration.

---

## File structure

One markdown file per tracked device/app, stored wherever makes sense in your
own setup (next to a related project folder, or in a dedicated tracking
folder). Four sections, in this order — the order matters, because the
top of the file is the part that gets read.

```markdown
# <Device / App> Status Tracker

**Source:** <app name> — screenshots supplied by the user. No API access.
**Why screenshots:** no legitimate API; unofficial libraries would require
storing account credentials. Screenshot transcription is the safe substitute.
**Update cadence:** <e.g. monthly, or whenever the user sends a screenshot>
**Last updated:** YYYY-MM-DD

## Needs Action
- <item> — <why, with the number that triggered it> (as of YYYY-MM-DD)
- (If nothing is outstanding, write "Nothing outstanding as of YYYY-MM-DD.")

## Baseline — current values
| Field | Value | As of | Notes |
|---|---|---|---|
| <field> | <value> | YYYY-MM-DD | <threshold, interval, or context> |

## History (append-only)
| Date observed | Field | Value | Source | Notes |
|---|---|---|---|---|
| YYYY-MM-DD | <field> | <value> | screenshot | <anything unusual> |

## Reference
- Service intervals, thresholds, warranty/recall notes, model details.
```

**Needs Action goes above Baseline.** It is the only section that changes
behavior, so it gets read first even when the file is skimmed.

---

## Transcription rules

1. **Transcribe only what is visible.** Never infer a field the screenshot
   does not show. If a value is cut off, blurred, or ambiguous, write
   `unreadable` in History and say so in the reply — do not carry the prior
   value forward as if it were freshly observed.
2. **Date every reading with the date the screenshot reflects**, not the date
   you processed it. If the app shows its own "last synced" timestamp, use
   that and note the lag.
3. **History is append-only.** Never edit or delete a prior History row. A
   correction is a new row that names what it corrects.
4. **Baseline is overwrite-in-place.** It always holds current values, and
   every cell carries its own "as of" date — different fields commonly come
   from different screenshots.
5. **Distinguish "not yet observed" from a real value.** A field that has
   never been captured is `—`, never `0`, never "OK", never blank-implying-
   fine. A tracker that collapses unknown into a benign value becomes
   silently confident about things it never measured.
6. **Note what the screenshot could not tell you.** If the user sent one
   screen and the tracker has fields living on another, list the missing
   fields explicitly so the next screenshot request is specific.

---

## Flagging rules

After transcribing, compare each field against its threshold or interval in
the Reference section and update Needs Action:

- Anything past its threshold or interval → Needs Action, with the number.
- Anything within a stated warning band → Needs Action, marked as
  approaching, with the projected date if it can be computed from the
  History trend.
- Anything newly appearing (a recall, an alert, an error code) → Needs
  Action, quoted verbatim from the screenshot.
- Resolved items → removed from Needs Action, with the resolution recorded as
  a History row.

State the flags in the reply too, not just in the file. The file is the
record; the reply is what the user actually reads.

---

## Hard constraint — never auto-act

This skill **transcribes, logs, and flags. It does nothing else.** It does
not book appointments, place orders, create calendar events, create or modify
scheduled tasks, or contact any third party — even when the flagged item
obviously implies one of those. Surface the recommendation and let the user
decide.

If a recurring reminder is genuinely wanted, that is a separate request the
user makes explicitly, handled by the scheduler skill.

---

## Update cadence

Suggest a cadence proportional to how fast the tracked values move (monthly
for vehicle wear items, quarterly for slow-moving status). Record it in the
header. Do not enforce it — this pattern is human-triggered by design, and a
gap in the History table is data, not a failure.

---

## Before delivering — verification step

Re-read the file you just wrote and confirm:

1. Needs Action is above Baseline, and says "Nothing outstanding as of
   YYYY-MM-DD" rather than sitting empty.
2. Every new History row has a date, and no prior row was edited or removed.
3. Every Baseline cell has its own "as of" date.
4. Any field never observed shows `—`, not a zero or a reassuring word.
5. Any unreadable value was recorded as `unreadable`, not silently guessed.
6. Nothing was booked, ordered, scheduled, or sent.

---

**Reference implementation:** a worked instance of this pattern is a
vehicle-maintenance tracker fed from a manufacturer's app — odometer, tire
pressure, oil life, service intervals, recalls. Point this skill at your own
equivalent file; if it does not exist, note that and carry on. It is an
example, not a dependency.

**Provenance:** Created 2026-08-17 from an observation logged 2026-07-19.

---

## Related skills

**In this pack:** `price-monitor` — use that instead when the value you are tracking is a purchase price being watched for a buy signal.

A boundary note, not a requirement. This skill works standalone.

**Not included here:** a scheduling capability, if you want the tracker prompted on a recurring basis rather than run by hand.

