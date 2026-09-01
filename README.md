# Claude Skills

A collection of reusable [Claude](https://claude.ai) skills — self-contained methodology files that give an AI assistant a repeatable way to handle a particular kind of work.

**Created by Josie Lagarde, designed with Claude (Cowork)**

---

## What a skill is

A skill is a Markdown file with a name, a description of when to use it, and a body of instructions. When a task matches the description, the assistant loads the skill and follows it. Think of it less as a prompt and more as a written procedure — the kind of thing you'd hand a new colleague who already knows the domain but not how *you* like it done.

The ones here are methodology-only: they encode an approach, not a setup. Nothing depends on a particular machine, filesystem, or account.

## Using them

Each directory holds one `SKILL.md`. Copy the folder into wherever your assistant loads skills from, or read them as plain documents — several are useful as written procedures even without an AI involved.

They're written for Claude, but nothing in them is Claude-specific beyond the file format. The methodology carries over.

**Take one or take all of them.** Every skill here works on its own. Where one references another it is telling you which skill fits a given question — a boundary note, not a dependency. Each file also ends with a **Related skills** section saying exactly what, if anything, it expects alongside it, so a single-file copy still answers the question.

## What's here

| Skill | What it does | Standalone? |
|---|---|---|
| `council-of-agents` | Convenes independent AI agents to debate a genuinely ambiguous, high-stakes question under a hard round cap | Yes — wants a second, non-Claude model |
| `pec` | The Plain-English Challenge Protocol: an 8-test stress-test for plans, architectures, and proposals | Yes |
| `project-foreman` | Runs large multi-batch projects in staged passes with QC checkpoints that stop on ambiguity rather than guessing | Yes |
| `source-hybrid-reconciliation` | For deliverables that deliberately merge two source specifications, and the reconciliation note that has to travel with them | Yes |
| `task-feeds-task-handoff` | Designing recurring tasks that hand data to each other without one failure silently poisoning the next | Yes |
| `task-observer` | A background layer that notices skill-improvement opportunities during real work and logs them for later review | Yes |
| `resumable-web-index-crawl` | Builds a resumable, selectively-retrievable index of a large documentation site without bulk-downloading it | Yes |
| `screenshot-fed-status-tracker` | Tracking a device or service's status over time when there's no legitimate API to read it from | Yes |
| `consumer-purchase-research` | Structured research for a purchase decision or financial-product comparison | Yes |
| `price-monitor` | Recurring price checks against a stored baseline, with explicit buy/hold/watch signals | Yes |
| `critical-thinking-walkthrough` | A six-lens pass over information someone else handed you, ending in a stated position and a smallest next step | Yes |

**The review pair** — `pec` and `critical-thinking-walkthrough` — split by what you're holding: `pec` attacks a plan *you* own and are committed to; `critical-thinking-walkthrough` interrogates a claim, article, or pitch *someone else* handed you before you have a position on it. Either one alone is still complete.

**The shopping pair** — `consumer-purchase-research` and `price-monitor` — cross-reference each other to say which one answers which question: *what should I buy*, versus *has it dropped yet*. Either one alone is still complete.

Some skills mention capabilities this pack doesn't include — a scheduler, a reliable-fetch layer, a spreadsheet writer, a skill-authoring skill. Those are noted as optional pairings in each file. Nothing here breaks without them.

## Not affiliated with Anthropic

This is an independent personal project. It is **not affiliated with, endorsed by, sponsored by, or reviewed by Anthropic**. "Claude" and "Anthropic" are trademarks of Anthropic, PBC, used here only to describe what these skills are written for.

Nothing here is official documentation. For that, see Anthropic's own.

## Scope and support

These are personal working tools, published because the methodology may be useful to others — not a supported product.

- Provided **as-is, without warranty**. Review anything before relying on it, especially where it touches health, money, or files you cannot recreate.
- Updated when the private source library changes, which is irregular. Treat any copy as a **snapshot**.
- Issues describing a genuine gap in a methodology are welcome and do get read, but **there is no guaranteed response time** and no commitment to fix, maintain, or support.
- Some skills reference tools, file layouts, or conventions specific to how they were built. Where a path or setup detail appears, substitute your own.

## Feedback

If a skill's *methodology* has a gap — a step that doesn't generalise, an edge case it mishandles — that's worth raising as an issue. If an assistant simply failed to follow one, that's usually a different problem.

## Licence

Released under [Creative Commons Attribution 4.0 International](https://creativecommons.org/licenses/by/4.0/) (CC BY 4.0). Full text in [`LICENSE`](LICENSE).

You may share and adapt this material for any purpose, including commercially, provided you give appropriate credit, link to the licence, and indicate if changes were made.

**Attribution:** Josie Lagarde — https://github.com/josie2479-maker/jubilant-octo-dollop

## About this repository

This is a **publication mirror**, updated from a private working library. It's one-way: nothing lives only here, and nothing is edited here directly. Pull requests that change a skill's content are unlikely to be merged as-is, since changes have to be made upstream — but issues describing the problem are welcome and do get folded back into the source when they land.

Some skills touch health, mental-health, or financial topics. Those carry their own disclaimers; please read them rather than relying on this page.
