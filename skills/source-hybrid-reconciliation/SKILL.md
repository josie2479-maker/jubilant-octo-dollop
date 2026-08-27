---
name: "source-hybrid-reconciliation"
description: "Use whenever a task deliberately merges two source specifications into one deliverable — crossing two recipes, blending two config files, adapting one migration plan onto another's structure, combining two financial models, or porting a component out of one spec into another's procedure. Triggers on 'take the method from this one and the flavor/settings/assumptions from that one', 'combine these two', 'cross these', 'adapt X to fit Y', 'merge these configs/models/plans', or any brief that names a source A structure plus a source B element to carry into it. Returns the merged spec plus an explicit reconciliation note listing every carried element that was rescaled, that changed functional role, or that is an untested starting point. Exception: not for choosing between two options (that is a comparison or decision task), and not for reconciling two sources that are supposed to agree and don't (that is a data-conflict verification task)."
---

# Source Hybrid Reconciliation

**Created by Josie Lagarde, designed with Claude (Cowork)** · Created: 2026-08-17

**License:** Released under CC BY 4.0 — free to use, adapt, and share with
attribution.

Use this when the deliverable is a **hybrid**: one source supplies the
structure or procedure (call it **A**), and one or more elements are carried
in from a second source (**B**). Recipes, configs, financial models,
migration plans, policy documents, build scripts — the domain does not
matter. The failure mode is the same in all of them.

---

## The failure mode this exists to prevent

The dangerous elements in a merge are **not** the ones that obviously
conflict. Those get noticed and adjudicated. The dangerous ones are the
elements that look directly transferable because they share a name and a
unit across both sources — and are therefore copied across without thought.

Two sources can use the same component, in the same units, to do
structurally different jobs. Comparing quantities without comparing the
**roles** those quantities play produces a merge that is arithmetically
defensible and functionally broken.

A brief that already warns you about one non-transferable quantity is not
evidence that the others were checked. Treat every carried element as
suspect, including the ones the brief explicitly told you to carry.

---

## The three-question check

Run this for **every** element carried from B into A's structure — not just
the ones flagged as risky.

**1. Is this an absolute value, or a ratio to B's base?**
Most quantities in a specification are implicitly proportional to that
source's base quantity (batch size, headcount, node count, principal,
serving count). If B's base differs from A's, the raw figure means nothing
until it is rescaled: `A_value = B_value × (A_base ÷ B_base)`. State the
scaling factor you used and the two bases it came from.

**2. Does the element play the same functional role in both sources?**
Locate the step that *consumes* the element in A and the step that consumes
it in B, and compare them. An element that is an **input** in one source and
a **finish** in the other cannot be transferred by quantity alone — the role
shift is invisible from the number and only appears by reading both
procedures end to end. This is the check that gets skipped.

**3. Does A's receiving step have the capacity to accept it?**
Even correctly rescaled and role-matched, the amount has to fit the step
that receives it. Ask what that step's absorption limit is — the receiving
system's remaining capacity, the config key's valid range, the budget line's
headroom. A transfer fails not because the number is large but because the
receiving step cannot take it.

---

## Procedure

1. **Name A and B explicitly** at the top of your working notes: which one
   supplies structure/procedure, which one supplies carried elements. If the
   brief is ambiguous about this, resolve it before drafting anything.
2. **Read both sources end to end** before transferring anything. The role
   shift in question 2 is not detectable from an extract.
3. **List every element being carried** — including ones the brief named as
   safe. Build a small table: element, B value, B's base, B's consuming step,
   A's consuming step, A's base.
4. **Run the three questions per row.** Record the answer, not just the
   resulting number.
5. **Draft the merged spec** using the reconciled values.
6. **Write the reconciliation note** (below) into the deliverable itself.

---

## Output contract

The deliverable must carry a short, visible reconciliation section — not
private notes. It states, for each carried element:

- the original value and which source it came from;
- whether it was rescaled, and by what factor (with both bases shown);
- whether its functional role differs between the sources, and what was done
  about it;
- whether the final number is **derived** (confidently computed) or an
  **untested starting point** (a judgment call under uncertainty).

Where two sources genuinely pull in different directions and there is no
clean resolution, **state the tension explicitly and mark the chosen figure
as an untested starting point**. Do not silently adopt one source's number
and present it with the same confidence as the rest of the document. The
reader is entitled to know which figures are load-bearing guesses.

---

## Worked example (the case this came from)

A recipe hybrid: method and proportions from source A, flavor profile from
source B. The brief said to carry B's heavy cream (2 cups) into A's
procedure, and separately warned that B's stock volume must **not** be
carried because A's process has different evaporation behavior.

The cream had the same defect and had not been flagged:

- **Question 1:** B's 2 cups is a ratio to B's base, which is ~1.7× A's base.
  The figure needed proportional rescaling before it meant anything.
- **Question 2:** the cream plays a different role in each source. In B it is
  part of the cooking liquid, added incrementally and absorbed. In A it is a
  small off-heat enrichment added after the medium is already fully
  saturated.
- **Question 3:** A's receiving step cannot absorb B's volume at all — the
  medium is saturated by the time the step is reached. The failure is not the
  size of the number; it is the capacity of the step.

The warning in the brief covered one quantity. The check has to cover all of
them.

---

## Before delivering — verification step

Re-read this skill's three questions and check the draft against them:

1. Is there a row in the reconciliation table for **every** element carried
   from B, including the ones the brief called safe?
2. For each row, is there an explicit ratio-vs-absolute answer with the two
   bases named?
3. For each row, are both consuming steps named (A's and B's), and is any
   role shift stated?
4. Is every figure in the deliverable marked as derived or as an untested
   starting point?
5. Does the deliverable contain the reconciliation section visibly, rather
   than only your working notes?

If any answer is no, fix it before delivering.

---

**Provenance:** Created 2026-08-17 from an observation logged 2026-08-15.

---

## Related skills

This skill is self-contained — nothing else in this pack is required.

