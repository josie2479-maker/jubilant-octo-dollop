---
name: "task-feeds-task-handoff"
description: "Use when designing, splitting, or auditing recurring or scheduled tasks that hand data to each other — deciding whether a fragile, slow, or network-dependent step should be split out of a task that also does other valuable work, and defining the status file the two tasks share. Triggers on 'should this be its own task or part of that one', 'split this out', 'have this task feed that one', 'the Friday task should pick this up', 'set up a handoff between these two tasks', 'this step keeps breaking the whole run', or any recurring-task design where one task's output becomes another task's input. Returns the split decision, a status-file contract (Date + RunStatus + payload fields), and consumer-side rules that treat a missing or stale file as nothing-to-report rather than an error. Exception: creating, rescheduling, or registering the task itself belongs to the scheduler skill — this covers the architecture between tasks, not their registration."
---

# Task-Feeds-Task Handoff

**Created by Josie Lagarde, designed with Claude (Cowork)** · Created: 2026-08-17

**License:** Released under CC BY 4.0 — free to use, adapt, and share with
attribution.

Specific file names, folders, and schedules are per-setup — fill in your own
at design time.

A named shape for the recurring situation where one scheduled task produces
data that another scheduled task consumes. Without a shared methodology this
shape gets hand-rebuilt inside individual task prompts every time, and each
rebuild drops a different one of the three safety properties.

---

## When to split a step into its own task

Split when **both** are true:

- The step is **fragile, slow, or externally dependent** — browser
  automation, an email scrape, a third-party API, anything that can hang or
  return nothing through no fault of the logic; **and**
- The task it currently sits in **has other independent value** — work the
  user still wants delivered on schedule even when the fragile step fails.

Do **not** split when:

- The consumer task has no purpose without the data (splitting adds a failure
  point and buys nothing — the consumer just fails one step later).
- The step is cheap, local, and deterministic (date math, reading a local
  file). Local file/date work belongs inline; it cannot take a task down.
- The two would run at the same time anyway with no ordering guarantee. A
  handoff needs the producer to have run **before** the consumer, on a
  cadence that makes that reliably true.

---

## The three properties that make a split actually safe

A split is not safe because the data moved into a separate file. It is safe
only with all three of these. Missing any one reduces the design to "two
tasks that happen to share a file," which delivers none of the intended
benefit.

**1. Genuine failure isolation.**
A crash, hang, or empty result in the producer must not block, corrupt, or
delay the consumer's independent work. Test this by asking: if the producer
never ran at all this week, what does the consumer deliver? The answer must
be "everything except this one optional section."

**2. Freshness-checked consumption.**
The consumer must distrust old data by default — not merely check that the
file exists. A status file with no freshness check is worse than no file,
because stale data reads exactly like current data and gets reported with the
same confidence.

**3. No duplicated expensive operation.**
If the producer already performs an expensive or fragile fetch (a browser
pass, an API pull, a mailbox scrape), the consumer piggybacks on that
existing work rather than running a second one. Two independent scrapes of
the same source doubles the fragility the split was meant to contain. Where a
new need overlaps an existing task's fetch, extend that task with one step
rather than building a second fetch.

---

## The status-file contract

The producer writes exactly one small file. The consumer reads it and nothing
else from the producer.

```markdown
# <producer-task-name> — status

Date: YYYY-MM-DD          <- the run date, ISO, always absolute
RunStatus: OK | PARTIAL | FAILED
Note: <one line; on PARTIAL/FAILED, what was missing and why>

## Payload
| <field> | <field> | <field> |
|---|---|---|
| ... | ... | ... |

(If the run produced nothing to report, write RunStatus: OK with an empty
payload table and a Note saying so. Empty is a real, valid result.)
```

Contract rules:

- **`Date` is mandatory and absolute** (`YYYY-MM-DD`). Never "yesterday,"
  never a weekday name. Relative dates are meaningless to the cold-starting
  session that reads them later.
- **`RunStatus` is mandatory** and distinguishes "ran and found nothing"
  (`OK`, empty payload) from "could not run" (`FAILED`). Those must never
  collapse into the same file state.
- **The producer overwrites the file each run.** It is current status, not a
  history log. If history is wanted, that is a separate append-only file the
  producer maintains, and the consumer still reads only the status file.
- **The producer writes the file even when it fails**, with
  `RunStatus: FAILED`. Any failure the producer can catch — a caught exception,
  a step that returned nothing, an API that refused — must still produce a file.
  Do not let a handled error exit silently.
- **A missing file is ambiguous, and the contract cannot make it otherwise.**
  The rule above covers failures the producer survives long enough to report. It
  cannot cover a hard kill: a crashed process, an OOM, a machine reboot, a
  network drop mid-write, or a scheduler that never fired leaves no file at all.
  So "no file" means *either* "never ran" *or* "died before it could tell you" —
  never assume the first. This is why the consumer treats a missing file as a
  degraded-but-survivable state rather than proof of anything, and why an
  investigation into a missing file starts with the producer's own logs, not
  with the status file's absence.

---

## Consumer-side rules

The consumer treats the producer's output as an **optional source**:

1. Read the status file at its full absolute path.
2. **If the file does not exist:** note it in one line and continue. Do not
   error out, do not retry, do not attempt the producer's work inline.
3. **If `Date` is older than the staleness window** (default: older than the
   consumer's own cadence — a weekly consumer rejects data more than 7 days
   old), treat it as nothing to report and say so in one line.
4. **If `RunStatus` is `FAILED`,** treat it as nothing to report and mention
   the producer's `Note` in one line so the failure is visible somewhere.
5. **If `RunStatus` is `PARTIAL`,** use the payload and pass the `Note`
   through to the output.
6. In every case above, the consumer's own independent work completes
   normally. The handoff section is additive.

Say the staleness window out loud in the consumer's prompt. "Stale" is not
self-evident and every consumer has a different tolerance.

---

## Writing the two prompts (cold-start requirements)

Both tasks run with zero memory of any prior session. Each prompt must
independently state:

- **Absolute paths** for the status file, resolved for the surface the task
  actually runs on. A Windows `C:\` path is correct only on surfaces that can
  see the Windows filesystem; a cloud session sees a different mount. Resolve
  every path through your setup's surface path key — the mapping that records,
  per surface, which filesystem root that surface can actually see — before
  writing it into a prompt. If no such mapping exists yet, write one first;
  guessing a path per-task is how cross-surface tasks silently break.
- **Failure handling on every file read** — "if this file doesn't exist, note
  it and skip; do not error out."
- **Every source named explicitly.** Never "read the relevant file."
- **A verification step** — the producer reads back the first lines of the
  file it just wrote and confirms `Date` and `RunStatus` landed.
- **Which task is the counterpart**, by name, in one line — so a future
  session editing one prompt can find the other.

---

## Auditing an existing fleet for candidates

Given a set of recurring tasks, flag any task that:

- bundles a browser/API/mailbox step into a run that also produces
  independent output (split candidate);
- performs a fetch that another task already performs against the same source
  (consolidation candidate — extend the existing fetch instead);
- reads another task's file with no `Date` check (freshness gap — property 2
  missing);
- errors or halts when a companion task's file is absent (isolation gap —
  property 1 missing).

Report these as findings for the user to approve. Do not restructure a
running task fleet unprompted.

---

## Before delivering — verification step

Re-read this skill's three properties and check the design against them:

1. If the producer never runs, does the consumer still deliver its
   independent output in full? Name what it would deliver.
2. Does the consumer have an explicit staleness window, stated as a number of
   days, and does it treat stale as empty rather than as an error?
3. Does the producer write its status file on failure too, with
   `RunStatus: FAILED`?
4. Does any expensive fetch appear in more than one task prompt?
5. Are all paths absolute and correct for the surface each task runs on?
6. Does each prompt name its counterpart task?

If any answer is no, fix it before delivering.

---

**Provenance:** Created 2026-08-17 from an observation logged 2026-07-21.

---

## Related skills

This skill is self-contained — nothing else in this pack is required.

**Related in this pack:** `task-observer` — if you keep an observation log, that skill governs how entries get written.

