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

## Feedback

If a skill's *methodology* has a gap — a step that doesn't generalise, an edge case it mishandles — that's worth raising as an issue. If an assistant simply failed to follow one, that's usually a different problem.

## Licence

Released under [Creative Commons Attribution 4.0 International](https://creativecommons.org/licenses/by/4.0/) (CC BY 4.0). Full text in [`LICENSE`](LICENSE).

You may share and adapt this material for any purpose, including commercially, provided you give appropriate credit, link to the licence, and indicate if changes were made.

**Attribution:** Josie Lagarde — https://github.com/josie2479-maker/jubilant-octo-dollop

## About this repository

This is a **publication mirror**, updated on a monthly cadence from a private working library. It's one-way: nothing lives only here, and nothing is edited here directly. Pull requests that change a skill's content are unlikely to be merged as-is, but issues describing the problem are welcome and do get folded back into the source.
