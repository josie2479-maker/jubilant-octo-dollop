---
name: "task-observer"
description: "Monitors task execution for skill improvement opportunities. Use this skill during ANY multi-step task, agentic workflow, or substantive work session where the agent is using tools and producing deliverables. It captures patterns, user corrections, workflow insights, and methodology worth preserving as reusable skills. Also triggers during post-task feedback discussions and when the user explicitly mentions skill observations, improvements, the observation log, skill taxonomy, or asks the agent to watch for skill opportunities. IMPORTANT: this skill should be invoked at the start of every task-oriented session — if you are about to use tools to produce deliverables, invoke this skill first. For reliable activation, pair this description with a CLAUDE.md instruction or harness-level session-start hook (see Recommended Activation Setup) — description-level matching alone is not enforceable."
---

# Task Observer — Continuous Skill Discovery & Improvement

**Created by Josie Lagarde, designed with Claude (Cowork)** · Created: ~2026-06 (estimated; in use by 2026-06-22)

**License:** Released under CC BY 4.0 — free to use, adapt, and share with
attribution.

A persistent behavioral layer for noticing skill creation and improvement
opportunities during real task work, so insights don't get lost between
sessions. It doesn't replace the skill-creator — it feeds it: this skill is
the eyes and ears; skill-creator is the hands.

---

## Recommended Activation Setup

A skill that monitors *all* tasks is easily overlooked when the agent is
focused on the task itself, so activation is dual-layer: this description
plus a structural trigger in the user's configuration file. Add to CLAUDE.md
(or equivalent):

```
At the start of any task-oriented session — any interaction where you will
use tools and produce deliverables — invoke the task-observer skill before
beginning work. This ensures skill improvement opportunities are captured
throughout the session.

When loading any skill, check the observation log for OPEN observations
tagged to that skill. Apply their insights to the current work, even if
the skill file hasn't been updated yet. This enables immediate application
of observations before they're permanently integrated during the weekly
review.
```

The structural trigger also covers **compaction**: a resumed session re-reads CLAUDE.md and re-invokes this skill regardless of what the resumed session's opening message says. Observations before and after compaction append to the same log with continuous numbering.

**Anti-pattern:** don't make task-observer's activation depend on another
skill loading it — load both independently from configuration. A broken chain silences all observation activity.

**Config detection (once per session):** check whether the configuration file exists and contains the activation instruction. If the file exists without it, briefly suggest adding it (1–2 sentences, not a tutorial). If no config file
exists, suggest creating one. In environments without filesystem access, check the project instructions instead.

---

## The Pre-Flight Principle

Rules documented in a skill are not always followed during the creative flow of producing output. So: **every skill that contains explicit rules should include a verification step** where the agent re-reads the rules and checks its output against them before delivery. A 30-second re-read prevents a 30-minute rework cycle. When creating or improving any skill, ask: "Does this skill have rules? Does it have a mechanism to enforce them?" If not, add one.

### Self-Enforcement

Before surfacing observations at end of session, verify:

1. Were observations logged throughout the full session — including post-task feedback and reflective discussion, not just active tool use?
2. Were they logged silently, without interrupting the user's flow?
3. Does each follow the format (Issue → Suggested improvement → Principle)?
4. Is each tagged with the correct type (open-source or internal)?
5. For observations about existing skills, does the suggested improvement
   reference the specific section or rule?
6. For `type: open-source`, does the Principle field contain any client-identifying information? If so, generalise before surfacing.

Fix any failures before surfacing.

---

## Skill Taxonomy

Every skill is **open-source** or **internal**. The boundary is also a
**confidentiality boundary**: open-source skills must never contain anything that could identify a client, project, or proprietary process, even
indirectly.

**Open-source** — client-agnostic, methodology-driven, valuable to other
practitioners. Required elements: identifies itself as open-source; author
attribution block at top; license statement (this skill uses CC BY 4.0; any
recognized license works — include the statement in the preamble and a
LICENSE file in the skill directory); feedback pathway routing methodology
feedback to the creator; tool-agnostic language where possible (capabilities, not product names); built-in enforcement (pre-flight checks). **Default bias:** when a skill could go either way, default to open-source — strip specifics and generalize.

**Internal** — contains user-, client-, or project-specific information,
personal preferences, or context only the user has. No attribution or license needed; can be shorter and less formal. Internal skills are working documents: keep them current, don't over-engineer them.

**Author attribution template** for new open-source skills — a compact block at the top of the skill body:

```markdown
**Created by [Author Name] / [website or contact link]**

[1–2 sentence description of what the skill does and its provenance.]

**License:** Released under [LICENCE NAME]. [One-sentence summary.]

**Feedback & Support:** Route methodology questions and user feedback to
[public feedback channel — e.g. the skill's GitHub issues]; direct contact
via [contact link]. If feedback stems from the skill's methodology (rather
than the agent's execution of it), log it and suggest sharing it there. If
it stems from the agent not following the skill's rules, acknowledge the
mistake and correct it.
```

### Lean Content

A skill should contain only content that meaningfully changes the agent's
behavior at execution time. Changelogs, version notes, credits,
self-narrating prose, and maintainer-facing notes belong in a supporting doc, not in SKILL.md. This does NOT cut examples, anti-patterns, or worked
scenarios — those are load-bearing (bare rules get violated more reliably
than rules with context). The test: would removing it change how the agent
behaves? If yes, keep it; if no, move it out. Applies to both open-source and
internal skills — every non-load-bearing line is paid token cost on every
invocation.

---

## Observation Protocol

### When to Observe

Observation is active throughout the **entire task session** — from first
tool use through post-task feedback until the session ends:

1. **Active task execution** — documents, analysis, code, presentations, and similar substantive work.
2. **Post-task feedback and discussion** — user corrections and review
   comments are often the highest-signal input; capture them with the same
   diligence as execution-time observations.
3. **Meta-discussion about skills or methodology.**
4. **Reflective and strategic conversations** — planning and post-work
   reflection produce insights too.

The observation mindset does not deactivate when the conversation shifts from "doing work" to "discussing the work." It IS inactive during casual
conversation and quick factual questions with no tools or deliverables.

### What to Watch For

**Signals for a NEW skill:**

- A multi-step workflow reusable across projects or clients
- A methodology the user explains that no existing skill captures
- A task type that keeps recurring with similar structure
- A domain process with clear inputs, phases, outputs
- The user describing a refined personal process ("I always do it this way")
- A structured approach emerging naturally during the work that could be
  formalized

**Signals for IMPROVING an existing skill** — any new information from a task that uses a skill, including positive and neutral signals:

- The agent doesn't follow a skill's documented rules → the skill needs
  stronger *enforcement*, not just better rules
- A user correction reveals a missing rule or uncovered edge case
- The skill's recommended workflow proves less efficient than what emerged naturally
- A technique works so well it deserves promotion from incidental to
  recommended
- A step matters more (or less) than the skill's emphasis suggests
- A new use case the skill handles but doesn't document
- User feedback that generalizes beyond the current instance
- A skill assumption proves wrong in practice
- New tools make part of a workflow obsolete
- Corrections forming a pattern across instances
- A general principle emerges that applies to other skills too (see
  Principle Propagation)
- The user suggests a naming, framing, or structural change — even
  conversationally

**Signals for SIMPLIFYING an existing skill** — pruning matters as much as
growth:

- A section or rule never relevant across multiple active sessions
- A rule from a single observation never validated by recurrence
- An elaborate workflow users consistently shortcut
- Sections loaded but never acted on (dead context weight)
- Rules that contradict each other
- "Just in case" complexity that has never triggered
- A rule the agent consistently fails to follow — the fix is rarely writing
  it louder; either remove it or convert it to structural enforcement (a
  checklist, verification step, or unskippable tool call)

Treat this list as a review checklist; a "yes" is a simplification candidate.
During reviews, ask "what can we remove?" as deliberately as "what should we add?" If a previously-applied observation turns out to be a one-off, mark it declined and consider reverting.

**Do NOT log:** one-off corrections that don't generalize; preferences already captured in a skill; tool bugs unrelated to methodology; observations that would need proprietary client information to be useful in an open-source skill (unless an internal skill is the right home).

### How to Log

Append observations to the persistent log **silently** — never interrupt the user's flow.

**Write immediately, don't batch.** When a correction, insight, or
skill-relevant event occurs, write it to the log within the same turn or the
next — mental notes are not observations. Tie flushing to workflow
checkpoints, and enforce a **hard checkpoint after every ~3rd completed
task/todo item**: pause and ask "have any unlogged observations accumulated?" Softer guidance has been shown to get lost during demanding analytical work; if nothing has accumulated the pause costs seconds.

**Namespaced IDs at birth (2026-08-03):** every observation log carries a namespace code (the namespace table lives in your workspace's governance rules; e.g. MASTER, HOME, WORK). An entry is citable as `OBS-<NS>-<nn>` from the moment it is written — use that form in every cross-reference; bare `#N` and bare "Observation N" are banned in active durable files. Entry headings keep the greppable `### Observation NNN:` format — the ID grammar governs citations, not headings.

**Numbering — pre-logging step (mandatory):** before assigning a number, search the entire log for `### Observation \d+:`, take the highest, increment. Never rely on session memory or partial reads for the current count:

```bash
# GNU grep (Linux, Cowork):
grep -oP '### Observation \K\d+' log.md | sort -n | tail -1

# POSIX-compatible (macOS etc.):
grep -o '### Observation [0-9]*' log.md | grep -o '[0-9]*' | sort -n | tail -1
```

**Pre-write assertion (mandatory):** immediately before appending, re-read the
log and assert the proposed number doesn't already exist:

```bash
PROPOSED=$(( $(grep -oP '### Observation \K\d+' log.md | sort -n | tail -1) + 1 ))
grep -qE "^### Observation ${PROPOSED}:" log.md && {
  echo "COLLISION on #${PROPOSED} — another writer has claimed this number"; exit 1; }
```

If it fires, increment past ALL existing numbers and re-check — and log the
collision itself as a meta-observation.

**Post-write verification (mandatory — closes the TOCTOU race):** the
pre-write check passes at T0, the append lands at T1; a parallel session can
claim the same number in between (observed in production). After appending, count occurrences of the just-written number; if >1, renumber YOUR entry (the last occurrence) to max+1 via `sed`:

```bash
WRITTEN=$(grep -cE "^### Observation ${PROPOSED}:" log.md)
if [ "$WRITTEN" -gt 1 ]; then
  MY_LINE=$(grep -nE "^### Observation ${PROPOSED}:" log.md | tail -1 | cut -d: -f1)
  NEW_NUM=$(( $(grep -oP '^### Observation \K\d+' log.md | sort -n | tail -1) + 1 ))
  # NOTE: `sed -i` is not portable — GNU wants `-i`, BSD/macOS wants `-i ''`.
  # Write through a temp file instead; that works on both.
  sed "${MY_LINE}s/^### Observation ${PROPOSED}:/### Observation ${NEW_NUM}:/" log.md > log.md.tmp
  mv log.md.tmp log.md
fi
```

**Cross-verify on the canonical filesystem before renumbering.** Where the shell's view of the log is a mount that can lag the real filesystem (e.g. a
sandbox mount over Windows files), a `WRITTEN -gt 1` from the shell alone is not sufficient evidence of a real collision. Re-check the count with the Read/Grep tools against the canonical path; only renumber if that side also shows a duplicate. If the two disagree, trust the canonical side.

Why both checks: pre-write catches stale-read collisions; post-write catches race collisions. Stacking more pre-write layers cannot close race cases. For a log written by parallel agents, the reliable pattern is
check-then-act-then-verify.

**Session-start staleness check:** note the log's modification time at session start. If it was modified within the last few hours, a parallel session may be writing — re-read the log immediately before appending *each* observation, not just once.

**Format and insertion rules:** always `### Observation NNN:`; always append to the END of the file; never insert mid-file; never use alternative ID formats. One format, one insertion point — keeps the log greppable and
countable.

```markdown
### Observation [N]: [Short descriptive title]

**Date:** [date]
**Session context:** [what task was being worked on]
**Skill:** [existing skill name, or "New skill candidate: [working name]"]
**Type:** [open-source | internal]
**Phase/Area:** [which part of the skill or workflow this relates to]

**Issue:** [What happened. Be specific — what the agent did, what the user corrected, what pattern emerged. Enough detail to be understood weeks later without the original conversation.]

**Suggested improvement:** [Concrete change or creation. For existing skills, reference the specific section or rule. For new skills, scope and key
components.]

**Principle:** [The generalizable takeaway — why this matters beyond this
instance. The most important field.]
```

**Context preservation check:** if the observation depends on uploaded files, API responses, or session-local data, save that context to the workspace BEFORE logging, and add a `**Reference file:**` line pointing to it. An observation whose supporting data dies with the session cannot be actioned later.

### Verify Restated Claims Before Trusting Them

A family of related failure modes, all closed the same way: a claim about the state of a file, path, or inventory gets restated from memory or an earlier document, and nothing re-checks it before it becomes load-bearing for a new decision.

**Restated CLAUDE.md conventions.** A handoff doc, prior session summary, or even this session's own memory can restate a CLAUDE.md convention ("per CLAUDE.md's No Silent Failures rule...") without re-verifying it's still in the live file. CLAUDE.md is a mutable file — a bad merge, an incomplete migration, or an edit that overwrote more than intended can silently drop a section, and nothing forces a re-check unless someone happens to grep for the exact header. **Rule:** when a handoff, skill, or session instruction asserts "CLAUDE.md says X" or restates a convention as background context — especially in an older handoff (days to weeks old) or a workspace with active file-reorganization work in flight — treat it as a claim to verify, not a fact. A cheap check: grep the live CLAUDE.md for the exact section header before relying on the restated convention for a new decision. "It's in CLAUDE.md" decays the same way a remembered file path or function name does — give it the same quick-verify treatment before it's load-bearing.

**Restated index/summary state.** The same failure applies to any hand-maintained index or running-notes file describing another file's content (e.g. `skill-observations/INDEX.md` describing `log.md`). Each refresh note is easy to write by trusting the *previous* note's account of state rather than re-deriving the actual count or content from a fresh read every time — so a description that was accurate once can persist for days or weeks after the underlying file changed, with nothing positioned to notice the drift until an unrelated session happens to read both and compares them. **Rule:** whenever refreshing an index/summary entry, the numbers and claims in the new note must come from a fresh read of the file being indexed in that same session — never carried forward from the previous note's stated figures. A refresh that doesn't re-derive from the source it's indexing isn't a refresh, it's a copy of an assumption.

**Referenced data files.** A CLAUDE.md folder-map, skill instruction, or scheduled-task prompt can name a specific data file (a tracker spreadsheet, a baseline CSV, a config) as though its existence were settled fact — but the doc entry is a description, not proof. The file may never have been created, or may have been removed since the doc was written, and nothing re-checks the claim until an analysis tries to open it. **Rule:** before treating a named data file as load-bearing for analysis, verify it actually exists (a direct file check — Read, Glob, or `read_fresh` if staleness is a concern — not a doc reference) and, if it's absent, stop and surface that rather than proceeding as if the file were there. Same family as the two rules above: a doc's claim about a file — its content, its count, or its mere existence — is a claim, not proof, until verified.

**Restated inventory counts and item states.** The cases above cover restated *conventions* and restated *index state*. Counts and item states decay faster than either, because the underlying registry changes without anyone editing a document — a handoff can assert "51 scheduled tasks" against a live registry holding 49, name a task as enabled-and-running that was disabled hours earlier, and omit a registered item entirely, all within one day of being written. A document describing a mutable registry is a snapshot with no expiry stamp, and its confidence reads identically at one hour old and one month old. **Rule:** when a handoff, plan, or prior session's report states how many of something exist, or asserts a specific item's current state, re-derive both from the live source before using them — especially when the doc is more than a few hours old, or describes work in flight against that inventory. **Corollary:** a stale doc may name a fix as still-needed that has already been applied, so acting on it re-does or reverses completed work; check the current state of the thing before performing the remediation a doc asks for.

**Restated classification schemes (verify the taxonomy, not just the payload).** When a doc cites a numbered or lettered scheme owned by an external source — regulatory categories, API versions, rule/section numbers, chapter numbers — the *identifier* is itself data that can go stale independently of the content attached to it. A source can renumber or restructure its scheme while every substantive sentence around the citation stays accurate, which is exactly what makes the stale identifier invisible to a content-only diff. **Rule:** when refreshing or relying on a doc that cites an external classification scheme, re-fetch and diff the source's *structure* — heading list, item count, numbering — as a separate check from "did the facts change."

**Restated analytical premises (the frame itself is a claim).** The cases above cover restated conventions, index state, file existence, counts, and taxonomies — all claims *inside* an analysis. A brief can also supply the **pattern to be investigated** ("X, Y and Z all share property P"), and that framing is the one claim every downstream check operates inside of and therefore cannot see. A well-run test of a mis-specified premise is more dangerous than a sloppy test of a correct one: it produces a wrong answer wearing the full authority of a careful method. **Rule:** when a brief, handoff, or prior analysis hands you the pattern rather than the question, enumerate its constituent items and independently verify the shared property on each one *before* running any test that presupposes the pattern. Treat a supplied pattern exactly like a supplied file path — a description, not proof.

**Copying a claim forward.** The rule above covers RELYING on a claim; copying one into a new artifact is also reliance. A path/file warning pasted into a fresh prompt, gap list, or doc inherits the original's staleness — and can outlive its subject by weeks before anyone re-checks it. **Rule:** re-run the existence check at copy time, not just at first-use time; when adding a warning about a path or file to a prompt, prefer a dated, expiring phrasing ("verified present 2026-07-06; re-check before acting") over a timeless assertion. Every restatement is a chance to either re-verify or further entrench an expired fact.

**Sending-side claims (the other direction).** The rules above are about verifying claims THIS session receives. The same hazard exists in reverse: an unverified inference this session forms (e.g. "that log warning means the transcript was destroyed") can get written INTO a prompt or memory for another agent/session, phrased as settled fact. If the receiving agent doesn't independently verify, a guess becomes a false memory. **Rule:** before embedding a diagnostic inference in a prompt, memory write, or instruction for another agent or session, either verify it directly first or phrase it explicitly as a hypothesis to check ("logs suggest X — verify against your own state before acting"), not as fact. A signal that also appears in known-healthy cases is disqualified as evidence for an unhealthy one.

**Remote/spawned sessions don't share your filesystem view.** A remote or spawned session handed a file *path* rather than the content itself may not be able to open it — it doesn't share the interactive session's view of the filesystem. **Rule:** when handing work to a remote/spawned session, embed the needed brief/context inline in the prompt by value, rather than pointing to a file path and assuming it resolves on the other end.

### Handoff Doc Analysis

When a handoff doc arrives for observation logging:

1. Log all explicitly stated observations first, unfiltered.
2. Then systematically analyze the full document: what gaps, improvement
   opportunities, or new-skill candidates are *implied* but not stated?
3. Pay special attention to action items (each may imply a missing skill),
   open questions (ambiguity signals a decision-framework gap), the
   work-completed narrative (patterns may reveal meta-skills), and session
   notes.
4. Log derived observations with clear attribution distinguishing them from
   the doc's stated ones.

Treat a handoff doc's "still open" claims as hypotheses — re-verify against
the live log and files before acting on them; docs generated from other
sessions go stale.

When a doc characterizes a path as a "typo", "stale", or "duplicate" of
another path, verify it by attempting to read the path — don't accept the
characterization. A plausible name variant (e.g. an abbreviated parent
directory like `…\Scheduled\` vs `…\Workspace\Scheduled\`) may
be a real, separately-maintained directory, not an error. Arbitrate with
file-content evidence, not by deciding which doc is more authoritative: do
the edits the doc claims were made actually appear at the supposedly-canonical
path? If not, the "typo" is likely a real path the current session simply
cannot reach. A path unreadable from the current session should be flagged as
unresolved, never declared non-existent from a naming assumption alone.

### Archival on Write

The log stays lean through event-driven archival on every log write.

- **What archives:** entries already marked ACTIONED (confirmed) or DECLINED in a
  *previous* session or prior log write. Entries marked only `STAGED (pending install)` never archive — they stay visible in the active log until confirmed installed or explicitly declined, no matter how many log writes pass.
- **What does NOT archive:** entries marked ACTIONED/DECLINED in the *current* session — they keep one full cycle of visibility and archive on the NEXT  log write or the next review's start. Track the entry IDs resolved this
  session and exclude them from the archival pass.
- **During reviews:** archive previous sessions' resolved entries at review
  start (Step 1); do NOT archive entries you mark ACTIONED during the review itself (Step 6).
- **Archive location:** the log's own `archive\log-[YYYY-MM-DD].md` subfolder (for the master log, its own `archive\` subfolder), preserving the log's header and status key. The active log retains its header, all OPEN and STAGED entries, and entries just resolved this session.

---

## Confidentiality Safeguards

Client names, project details, domains, and proprietary information must
never appear in open-source skills. Five layers, each catching what the
others miss:

**Layer 1 — Observation-level stripping.** For `type: open-source`
observations, the Issue and Suggested Improvement fields should already use generic language; the Principle field must be fully generalized. The log is a private notebook, but the Principle is a publishable insight.

**Layer 2 — Pre-creation review.** Before drafting any open-source skill,
scan all source material for identifying information (client names, URLs,
domains, internal terminology, identifiably-specific structures) and replace
with generic equivalents before writing begins.

**Layer 3 — Post-draft sweep.** After writing, re-read specifically for
leakage: proper nouns that aren't the author's name; domains/URLs/project identifiers; industry details that narrow down the client; internal terminology; examples traceable to a real project.

**Layer 4 — Structural principle.** When in doubt whether a detail is too
specific, remove it. A slightly more generic skill always beats one that
leaks client information.

**Layer 5 — Cross-product re-identifiability sweep.** Individually-sanitized examples can combine to identify a client (counts matching a public client list; specific numbers in a thin vertical; thinly-disguised placeholder names in the same vertical as a real client). As a final pass before any public release: list every worked example and the fields it names (vertical, geography, numeric range, timing, count); ask whether any two together let a reader with the author's public client list map the set to real clients; mitigate by blurring counts, widening verticals, converting numbers to illustrative ranges, or consolidating examples into composites. This must be a mechanical pass, not a feeling — the author is the least reliable reader because they know the ground truth.

---

## Surfacing Protocol

**Default:** surface all observations at end of session, grouped — existing
skills by skill name, new-skill candidates separately.

**Surface earlier when:** an observation needs user input to be accurate; a
skill is actively producing wrong output the user should know about now; or multiple observations cluster on one skill needing immediate attention.

**How:** concise — title, skill, one-sentence summary; new-skill candidate vs improvement; suggested type (open-source/internal). Ask which the user wants to act on; hand pursued items to skill-creator.

---

## Acting on Observations

This skill identifies WHAT to build or improve; this section covers HOW.

**Trigger gate — observations are acted on only in three contexts:**

1. **The comprehensive review** (scheduled mode preferred; in-session
   fallback if no scheduled review in 7+ days).
2. **Explicit user requests** ("update X skill", "act on observation #N").
3. **In-session correction** when a skill is producing wrong output the user should know about — surface immediately.

Outside these, mid-task work produces observations only. The default is
**log, don't act**.

**Small changes** (clearly additive, low-risk, no testing needed) can be
applied directly: adding a rule or anti-pattern to a list, clarifying
ambiguous wording, adding an edge-case note, fixing a factual error. After
creating or updating any skill file, always present it via `present_files`
so the user can review and install it.

**Substantial changes** (restructuring workflows, new capabilities, changed methodology — anything where "does this actually work better?" is a real question) → hand off to skill-creator if available. Match rigor to
complexity and audience: skill-creator earns its keep for open-source skills
needing testing or unclear designs; for internal skills with requirements
established in conversation, writing directly is more efficient. Without
skill-creator, use the observations as a spec, edit directly, and flag the
changes as needing manual review.

**New skills** → use skill-creator when available, passing the observation(s) as the brief. Determine type early: open-source → strip specifics and generalize; internal → include specifics freely; uncertain → default to open-source and let the user add internal details.

---

## Skill File Locations — Live File vs Workspace Copy

1. **The live file is authoritative and read-only.** In Cowork it's mounted
   at `.claude/skills/{skill}/SKILL.md` (writes fail with EROFS — by design).
   In Claude Code the live file is surfaced by the capabilities system.
2. **Always start edits from the live file** — never from a workspace copy,
   prior draft, or memory.
3. **Stage edits** ONLY at the skill library's staging path: `[skill library]/staging/[YYYY-MM-DD]/[skill-name]/SKILL.md`, where `[skill library]` is wherever you keep the canonical copies of your own skills. After a confirmed install, refresh the skill's canonical copy in `[skill library]/source/[skill-name]/`.
4. **Present staged files via `present_files`** for review and one-click
   install; never attempt to write to the mounted skills directory. If
   `present_files` is unavailable in a given session, use `save_skill` with
   `overwrite: true` for skills the platform reports as user-editable — but
   not every skill in `<available_skills>` is user-editable via this route
   (some plugin-provided skills reject the overwrite even though they appear
   editable); treat a "not listed as user-editable" error the same way as an
   EROFS write failure on the mount, and fall back to leaving the staged file
   in place for the user to install manually.
5. **Diff any existing staged/workspace copy against the live file before
   overwriting it.** If they differ, the copy is stale — rebase your edits on
   the live version. Known failure mode: an update built from a stale
   snapshot silently dropped two sections another session had added the same day; only a pre-merge diff against the mount caught it.
6. **If a staging-path write itself fails with a file-lock error (EPERM/Access denied) on a file this session just created,** don't retry the same exact path repeatedly — a freshly created or freshly synced file under a Drive-synced staging tree can be held by a sync/watcher process the same way a directory rename can (see the File Cleanup Convention's directory-EPERM findings — this is the same lock class at file granularity, not just directory granularity). Write the full content to a fresh, never-touched path instead of overwriting the locked one, and say so plainly in the run's summary so a human can reconcile/clean up the abandoned path later.

---

## Principle Propagation

When an observation reveals a principle that applies to skills in general,
propagate it across the library, not just the triggering skill.

Cross-cutting principles live in **one master file** — a single top-level copy,
NOT a per-project one. Pick one absolute location, record it in your workspace
rules so every session resolves to the same file, and treat every per-project
copy as a redirect stub pointing at it.
It is a **mandatory checklist during any skill creation or regeneration** — before
delivering a new or updated skill, verify it complies with every active principle.

**One master, not per-project.** In a multi-project workspace, `[workspace folder]`
resolves differently per project, so keeping a principles file inside each
`Projects/<name>/skill-observations/` folder silently forks the checklist into
divergent copies — the exact failure this rule prevents (it had produced 9 divergent
copies, most empty, before consolidation). Keep exactly one master at the workspace
root. Every per-project `cross-cutting-principles.md` is a **redirect stub** pointing to
it; if you land on a stub, follow it to the master. Read, add, and edit principles only
in the root master — never repopulate a stub, and never create a new project-local
principles file.

Flow: log the observation with `Skill: All skills` → surface to the user →
on approval, add to the **root master** principles file → every future
creation/regeneration checks against it. The user chooses propagation timing:
**immediate** (update all skills now — e.g. a confidentiality rule) or
**opportunistic** (apply as each skill is next touched).

Entry structure:

```markdown
### [N]. [Principle title]
**Added:** [date]
**Applies to:** [all skills | all open-source skills | all skills with rules]
**Requirement:** [what it requires]
**Propagation:** [immediate | opportunistic]
**Status:** active
```

---

## Comprehensive Review (scheduled or fallback)

Cross-checks all open observations against all skills, propagates
cross-cutting principles, applies improvements that don't need user input.

**Preferred — scheduled autonomous review:** a recurring task registered with the platform's scheduler (typical cadence: 1–3 mornings/week). Runs without the user present and applies non-escalated observations autonomously.

**Fallback — in-session 7-day trigger:** fires at the start of the next
task-oriented session when BOTH: no scheduled review is registered (or none succeeded in 7+ days), AND `[workspace folder]/skill-observations/last-review-date.txt` is 7+ days old or missing. When it fires, tell the user the review is running and do Step 0 first.

**Scheduler-surface rule:** when checking whether a scheduled review is
registered or ran recently, name WHICH scheduler surface you queried. In
workspaces used from multiple product surfaces (e.g. a desktop/CLI session and Cowork), each surface has its own scheduler with no cross-visibility — an empty result from the surface you can see is NOT evidence the task doesn't exist on another. Never assert "not registered" from a single surface's empty list.

**Staleness noted → act or commit.** If the session-start check finds the
review overdue, either run the fallback immediately or explicitly tell the
user "deferring the comprehensive review until [specific point — e.g. end of this task]" and then do it. Noting staleness and silently moving on produces the same state as having no check at all, plus false confidence.

### Unattended-Run Scoping

Unattended runs (scheduled tasks, cron-fired sessions, any run with nobody present) get a reduced protocol:

- The comprehensive review, Step 8's present-summary gate, and the full numbering ritual (master-log grep → pre-write assert → post-write verify) are scoped **OUT** of unattended runs — the dedicated weekly review task owns them.
- An unattended run logs observations to its scope's **capture inbox** log with a namespaced ID at birth, and stops there; the weekly consolidation moves them to the master.
- Never block an unattended run on approval or acknowledgment. Every run reports done / done-with-assumption / queued-for-human per your setup's unattended-execution contract. If the canonical inbox is unreachable, append the observation to the run report instead — and write NOTHING to any same-named fallback folder (zero-writes-on-skip).

### Approval Policy

**Interactive (user present):** always ask before applying or declining.
Present observations grouped by skill, one-sentence summaries, wait for
blanket or selective approval.

**Scheduled autonomous (user absent):** apply non-escalated observations by default — the staging-plus-install pattern is the safety net (nothing is live until the user installs it, or a `save_skill overwrite: true` call actually succeeds — see Skill File Locations, item 4, on skills that reject the overwrite despite appearing user-editable). **Escalate without applying (report only) when:**

1. **New skill creation** — naming, scope, type, licence need user input.
2. **Removing or substantially restructuring existing content** — risks
   dropping institutional memory.
3. **An observation flags its own uncertainty** ("not sure if…", "might
   be…", "worth discussing…") — it's asking for user input; respect that.
4. **Conflicting observations** — surface rather than resolve autonomously.

Scheduled runs should still apply every non-escalated observation before
reporting — a review that applies nothing is just a report generator.

### Review Steps

**Step 0 — Recommend scheduled review setup** (fallback mode only).
Check the suppression marker (`skill-observations/scheduled-review-decline.txt`): if under 30 days old and the fallback hasn't fired repeatedly in that window, skip to Step 1. Check whether a scheduled review is registered (platform scheduler presence check — naming the surface per the rule above — or `skill-observations/scheduler-registered.txt`); if found, skip to Step 1.

Otherwise recommend setting one up. If the user agrees, register it via the
platform's scheduling capability (e.g. the schedule skill and its
`create_scheduled_task` tool in Claude environments; cron in terminal
environments), passing your own draft prompt for the review task if you have one, and write today's date to `scheduler-registered.txt`. If the user declines, write today's date to `scheduled-review-decline.txt` (30-day suppression — but if the fallback keeps firing within the window, re-surface). If no scheduling capability exists in this environment, skip silently.

**Step 1 — Archive resolved entries, then load.** First perform the archival
pass described in *Archival on Write*: move entries that were marked ACTIONED
(confirmed) or DECLINED in a *previous* session into the log's `archive/`
subfolder, preserving the log's header and status key. Leave `STAGED` entries
and anything resolved during THIS review in place. Then read
`skill-observations/log.md` (extract all OPEN and STAGED) and the root-master
`cross-cutting-principles.md` (the single master copy — not a per-project stub; all active). If there are no OPEN or STAGED observations and all principles are propagated: update the timestamp, tell the user "review: no open observations or outstanding principles," and proceed with the session. Before treating any PRIOR review's ACTIONED entries as fully resolved, diff each in-scope skill's live content against its most recently staged file — an entry marked bare "ACTIONED" from a prior review is not sufficient evidence of a live change; only trust it if the status text itself says "confirmed" (installed via a successful `save_skill`/`present_files` install, or a live-file diff performed this session). Anything reading as merely staged — or ambiguous — gets re-surfaced this cycle as `STAGED (pending install)`, not silently carried forward as done. (Confirmed in production 2026-07-28: three fixes sat staged for one to two weeks with no live effect while the log called them "ACTIONED.")

**Step 2 — Inventory all skills.** Use `<available_skills>` (or the skills
directory where the tag is absent). Read each SKILL.md. Only user-owned
custom skills can be updated; **known system skills (read-only):** docx, pdf, xlsx, pptx, skill-creator, schedule — if an update fails because the file
can't be overwritten (an EROFS-style mount failure, or a `save_skill` "not listed as user-editable" error), add that skill to this list. Note: not every skill appearing user-owned actually accepts `save_skill overwrite: true` in every session — treat a rejection there as equivalent evidence of read-only status, not as a transient bug worth retrying.

**Step 3 — Cross-check observations against every skill.** Don't rely solely on each observation's own "Skill" field — Principles often apply more broadly. Build skill → [relevant observations]. Interactive: present all observations in one message, grouped by skill, flagging judgment calls as "needs your input". Autonomous: apply the approval policy and proceed.

**Step 4 — Cross-check cross-cutting principles against every skill.** Flag
non-compliant skills.

**Step 5 — Apply updates.** (Interactive: after approval.) For each affected skill, create an updated SKILL.md: integrate insights into the right section (never append an observations list at the bottom); preserve structure, voice, and attribution; place new steps/anti-patterns where they logically belong. 
**Observations targeting system skills:** don't skip — route the delta to a
user-owned complementary skill named `{system-skill}-extras` (create it if
needed; it states which skill it extends, contains only the delta, and loads
alongside it via a config note). Never edit skill files in place — stage to
the library staging path (see Skill File Locations).

**Step 6 — Update observation status** in log.md (status field only). Use
the status vocabulary — OPEN / STAGED (pending install) / ACTIONED (confirmed + how) / DECLINED / CONSOLIDATED — and do not use bare `ACTIONED` for anything short of a confirmed live install:
- `STAGED (pending install) — Applied to [skill-name] (review [date]); staged
  at [path]` — the fix has been written and staged, but no install has been
  confirmed yet. This is the correct status the moment a file lands in
  the library staging path, and it stays this way across any number of later log
  writes until installation is actually confirmed.
- `ACTIONED — Applied to [skill-name] (review [date]); installed [date]
  (confirmed via [present_files install | save_skill | live-file diff])` —
  only once the live file has actually been confirmed to reflect the change.
- `CONSOLIDATED → OBS-MASTER-nn` — used only on capture-inbox entries whose
  content has been moved to the master log; the master row is authoritative
  from then on.

Never let a status read as plain `ACTIONED` with no confirmation language —
that conflation is exactly what let three fixes sit staged for one to two
weeks (2026-07-28) while the log called them done, with nothing in Step 1 of
a later review catching it until a human asked. Archival-on-write handles the
rest on the next log write, once an entry reaches confirmed `ACTIONED` or
`DECLINED` — `STAGED` entries never auto-archive.

**Step 7 — Update timestamp** in
`skill-observations/last-review-date.txt`.

**Step 8 — Present summary.** Present each updated file via `present_files` (or install directly via `save_skill overwrite: true` where `present_files` is unavailable and the skill accepts it, confirming the install succeeded before writing `ACTIONED` rather than `STAGED` in Step 6), then:

```
## Skill Review Complete — [date]

Updated based on [N] open observations and [N] cross-cutting principles.

### Updated Skills
**[skill-name]** — Changes: [1 sentence]. Observations applied: #[N], #[N]

### Observations Actioned
[numbers and titles]

### Skipped (needs manual review)
[anything not applied, with reasons]
```

Don't proceed with other work until the user acknowledges the summary (interactive sessions only — an unattended run reports and ends; it never waits on acknowledgment).

### Constraints

- Don't modify observation entries beyond their status field.
- Don't create new skills in a review — note candidates for the user to
  action via skill-creator.
- Unsure how to integrate an observation → skip it and note the uncertainty.
- Treat internal observations with the same rigor as open-source.

### Keep-Two Rule

The library `staging/` path keeps only the two most recent date directories
per skill; flag older copies for removal (`_delete` rename, never `rm`) when
a skill appears in more than two.

---

## Observation Log Management

**Location:** the MASTER log lives at one absolute path you choose and record in your workspace rules; every other copy is a redirect stub pointing at it. Per-project `skill-observations\log.md` files are **capture inboxes**: single-folder mounts require them, so observations may be captured anywhere, always with a namespaced ID at birth. The weekly review consolidates OPEN inbox entries into the master and marks the originals `CONSOLIDATED → OBS-MASTER-nn`. Create an inbox log on first use; never write to logs marked retired, archived, or superseded in the namespace table.

**Log structure:**

```markdown
# Skill Observation Log

Observations captured during task-oriented work. Each entry identifies a
potential skill improvement or new skill opportunity.

**Status key:** OPEN = not yet actioned | STAGED (pending install) = fix
written and staged but not yet confirmed installed to the live skill |
ACTIONED = installed and confirmed live | DECLINED = user decided not to
pursue | CONSOLIDATED → OBS-MASTER-nn = entry moved to the master log by a
weekly consolidation; the master row is now the authoritative copy

---

## [Date or Session Identifier]

### Observation 1: [Title]
**Status:** OPEN
[... full observation format ...]
```

### Session Start Protocol

The single entry point for session-start checks:

1. **Files exist?** If the log is missing, create it from the template in this
   document. The **principles file is a single master**, at whichever location
   your workspace rules nominate — a project's own `skill-observations/` should
   hold only a redirect stub, so never create a per-project principles file; if
   the master itself is missing, create it there. Then continue.
2. **Scan for relevant context.** Read OPEN and STAGED observations and active
   principles; hold in awareness, don't surface unprompted unless directly
   relevant to the current task.
3. **Review trigger.** Read `last-review-date.txt`. If missing or 7+ days
   old, trigger the Comprehensive Review before the user's task — or
   explicitly commit to a specific later point and keep that commitment
   (see "Staleness noted → act or commit"). Skipped entirely in unattended
   runs (see Unattended-Run Scoping).
4. **Config detection** (see Recommended Activation Setup). Once per session.

---

## Environment Compatibility

**With persistent storage** (workspace folders, project directories): the
full workflow above applies.

**Without persistent storage** (web chat): the methodology still works;
persistence becomes the user's job via **handoff doc mode**. Observations are collected in-session and presented in a structured handoff document before the session ends; the user stores it and pastes it into the next session. Include current cross-cutting principles so the next session has them.
**Generate proactively:** when the conversation winds down, offer the handoff doc without being asked — a premature offer is a minor interruption; a missing one is lost work.

```markdown
# Session Handoff: [Session Topic]

**Date:** [date]
**Context:** [what was worked on; what the next session needs to know]

## Decisions Made
## Observations Logged        [full entries in standard format]
## Cross-Cutting Principles (current)
## Action Items               [with enough context to resume]
## Working Artifacts          [drafts/analyses in full]
```

---

## Quick Reference

| Question | Answer |
|----------|--------|
| When do I observe? | Throughout the full session, incl. post-task feedback and reflection |
| How do I log? | Silently, immediately when triggered; never batch |
| When do I surface? | End of session, or earlier if needed |
| Reliable activation? | Config-level instruction + this description (dual layer) |
| Open-source or internal? | Default open-source; strip specifics |
| Small fix or skill-creator? | Clearly additive → direct; needs testing/restructuring → skill-creator |
| Format? | Issue → Suggested improvement → Principle |
| Numbering? | Grep the whole log, pre-write assert, post-write verify; never cached counts |
| Collision check trust? | Cross-verify on the canonical filesystem before renumbering |
| Restated claims (CLAUDE.md, index notes)? | Verify against a fresh read of the source before trusting; never carry forward |
| Restated counts or item states? | Re-derive from the live source, not the doc; a stale doc may ask for a fix already applied |
| Copying a claim forward? | Re-run the existence check at copy time, not just first use; prefer dated/expiring phrasing over timeless assertions |
| Sending a claim to another agent? | Verify first, or phrase as a hypothesis to check — never state an unverified inference as fact across a session boundary |
| Remote/spawned session handoff? | Embed context inline by value — don't hand it a file path and assume it resolves |
| Scheduler checks? | Name which scheduler surface you queried; empty ≠ not registered |
| Review overdue at start? | Run it now, or explicitly commit to when — never silently defer |
| Log archival? | Event-driven on write; `STAGED` entries never auto-archive; confirmed `ACTIONED`/`DECLINED` keep one cycle of visibility |
| Confidentiality? | 5 layers, incl. the cross-product re-identifiability sweep |
| Simplification? | Prune one-off rules, dead sections, skipped workflows, contradictions |
| No persistent storage? | Handoff doc mode, generated proactively |
| Where do principles live? | ONE master file at a single nominated path; all other copies are redirect stubs — follow them, never populate them |
| Unattended run? | Log to the scope's capture inbox with a namespaced ID and stop — no review, no Step 8 gate, no numbering ritual; report per whatever contract governs unattended runs in your setup |
| Citation form? | `OBS-<NS>-<nn>` from birth (namespace table in your workspace rules); bare `#N` banned in active files |
| Staged vs. installed? | Use `STAGED (pending install)` the moment a fix is written to the library staging path; only write `ACTIONED` once a `present_files` install, a successful `save_skill`, or a live-file diff has actually confirmed the change — bare `ACTIONED` with no confirmation language is never correct, and Step 1 of every review re-verifies it before trusting a prior cycle's status |
| Staging-path write hits EPERM on a just-created file? | Don't retry the same path — write to a fresh path instead (same lock class as directory-rename EPERM, at file granularity) and flag it in the summary |

---

## Related skills

This skill is self-contained — nothing else in this pack is required.

**Pairs well, not included here:**
- A skill-authoring skill. This one identifies *what* to build or improve and hands the work off; it does not write skill files itself.
- A scheduling capability, for the recurring comprehensive review. Without one, the skill falls back to an in-session trigger, which it documents.

