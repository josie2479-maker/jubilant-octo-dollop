---
name: "project-foreman"
description: "Plan and run large, multi-batch projects — digital organization work (migrations, cleanups, deduplication, reorganization) as well as multi-session greenfield builds — using a foreman/execution/QC structure: a foreman that stages the plan, execution passes that work in small paced batches, and a QC pass that checks each batch and flags-and-stops on anything ambiguous rather than auto-resolving it. Use whenever a project is too large or risky for one continuous pass — many items, an external API/rate limit, irreversible actions (posting, deleting, moving files), or a track record of needing careful human review. Also covers from-scratch builds needing per-item confidence classification and human checkpoints (e.g. recipe extraction with an allergen hard-filter). Trigger on phrases like \"big cleanup project,\" \"migrate/organize all my X,\" \"go through my whole library,\" or any digital organization or multi-batch build task with hundreds+ of items."
---

# Project Foreman

**Created by Josie Lagarde, designed with Claude (Cowork)** · Created: 2026-07-02

**License:** Released under CC BY 4.0 — free to use, adapt, and share with
attribution.

A workflow skill for running large digital-organization projects safely, in stages, without losing track of what's been done or silently making a wrong call on ambiguous items.

This generalizes the pattern proven across the Spotify Playlist Migration project (1,700+ tracks, many playlists, a flaky external API) into a reusable shape for *other* big digital-organization projects — file cleanup, photo dedup, email/inbox reorganization, bookmark migration, and similar. It is informed by that project's lessons, not locked to its specifics.

It has also proven to work well for multi-session greenfield builds (not just migrations/cleanups) when items need per-item confidence classification and human checkpoints — e.g. a from-scratch "kitchen inventory → meal suggestion" build with a safety-critical allergen hard-filter, where the AUTO/WEAK/FLAG tiers mapped cleanly onto extraction confidence and "flag-and-stop on ambiguity" mapped onto allergen-touching items.

## The Three Roles

**Foreman** — plans, doesn't execute. Breaks the project into discrete, independently-resumable units of work (one per playlist, one per folder, one per batch of N items). Orders them — smallest/lowest-risk first unless the user says otherwise. Decides batch/chunk size up front and states it explicitly rather than assuming. Maintains the running plan and status log.

**Execution** — does the work, in small paced batches, never in one continuous unsupervised pass once the project is large enough to risk real damage from a bad assumption. Rate-limits itself proactively (don't wait to get throttled to add pacing). Sub-batches destructive or hard-to-reverse actions (e.g., 10 items at a time, not 200).

**Quality Control** — checks execution's output before it's treated as final. Classifies each item into confidence tiers (e.g., AUTO / WEAK / FLAG) rather than a single pass/fail. **On anything in the ambiguous tier: flag and stop. Do not auto-resolve.** This is a deliberate, explicit choice — the cost of pausing to ask is much lower than the cost of rework after a wrong silent guess propagates through hundreds of items.

### The confidence tiers

QC assigns every item exactly one tier. The tiers are about **how the answer was
reached**, not how the reviewer feels about it — the same item can be AUTO in one
project and WEAK in another depending on what evidence was available.

**AUTO — accept without human review.** The match or transformation rests on evidence
that is *decisive by construction*: a unique identifier, a content hash, an exact
authoritative-field equality, a checksum that agrees. If a reasonable person could
look at the evidence and reach a different conclusion, it is not AUTO. AUTO items are
counted, logged, and not individually inspected.

**WEAK — accept provisionally, sample it.** The evidence is *suggestive but not
decisive*: fuzzy string similarity above a threshold, a filename-plus-size match with
no content check, a single scoring heuristic's top pick, a plausible-looking match with
no corroborating field. WEAK items proceed, but the batch's WEAK set gets spot-checked
before the next batch starts, and the sample size goes up if the first sample finds a
bad one.

**FLAG — stop, do not resolve it yourself.** Either the evidence is *contradictory*
(two candidates score nearly identically; fields agree on one axis and disagree on
another), or it is *absent* (nothing matched at all), or the action is
**irreversible and the evidence is not decisive**. FLAG items are handed to a human
with the competing options and what distinguishes them — never auto-resolved by
picking the highest score.

**Three rules that make the tiers work:**

- **Irreversibility promotes the tier.** Anything WEAK that would delete, overwrite,
  send, or publish becomes FLAG. Reversibility is part of the tier decision, not a
  separate consideration.
- **Absence is not AUTO.** "No match found" is a claim about the search, and a flaky
  wrapper around an external API produces the same empty result as a genuine absence.
  Confirm a negative before tiering it, or FLAG it.
- **Record the basis, not just the tier.** A log line reading `AUTO` is unreviewable
  six weeks later; `AUTO (sha256 match)` and `WEAK (title similarity 0.91, no
  identifier)` can both be audited and both can be argued with. Tier plus basis, always.

If a project needs different tier names or a fourth tier, rename freely — what matters
is that there are at least three, that one of them means *stop and ask*, and that the
boundary between them is stated in terms of evidence rather than confidence.

## Workflow

1. **Stage the input.** Get the full scope of the project into an inspectable, structured form (a JSON file, a list, a spreadsheet) before touching anything live. This is the foreman's first deliverable — it's also the natural resume point if the project spans multiple sessions.

   - **Before starting a fresh inventory/scan, check the target folder (and its parent) for a prior survey-style doc** (naming patterns like `*CLEANUP*SURVEY*`, `*dedup*`, `*audit*`). If one exists, read it and carry forward its "no action needed" / "deferred" conclusions rather than re-deriving them from scratch — note in the new report what's changed since. Skipping this risks re-flagging already-resolved items as new findings, or missing that the real ask is a previously-deferred decision, not a fresh scan.
   - **Capture identifiers alongside names, and re-verify the pairing live before acting on it.** Survey notes and checkpoint docs drift from ground truth in multi-session projects: a recorded folder/playlist/album ID can end up attributed to the wrong name. Log ID + name together at capture time, and before any execution batch that targets a container by name — especially a destructive or move operation — re-verify the name↔ID pairing against a fresh live listing rather than trusting the notes.
   - **A batch scope quoted as a number must come from a de-duplicated, one-row-per-item manifest.** Never quote a batch size derived by adding separately-generated candidate lists — lists built by different scripts against the same population overlap, and the summed figure hardens into a "fact" the moment it lands in a plan doc. Materialize the candidate population as a single one-row-per-item manifest and report the union count, the per-source counts, and the overlap. Corollary: a candidate list stored in grouped/aggregated form (one row per group, items joined into one cell) is a staging artifact, not an execution artifact — expand it to one row per item and re-derive per-item attributes from the source of truth before any consumer reads it, or downstream logic will silently misroute the items whose attributes got collapsed away.

2. **Plan the batches.** Decide chunk size and ordering. State both explicitly to the user before starting — don't silently assume a chunk size, since "how big" depends on risk tolerance, API limits, and how much rework a bad batch would cause. For very large projects (multiple orders of magnitude bigger than anything done so far in this project), prefer smaller chunks even if pacing has otherwise been validated — size and pace are separate dials.

   - **Ask the destination question before locking scope: "when this is done, what will you do with the result?"** This is distinct from "what problem does this solve?" — users answer the second in terms of the work items; the first reveals whether the work items are even necessary. A stated scope is downstream of an often-unstated destination, and eliciting the destination can collapse most of a project's risky work (e.g. items that turn out not to need modification at all because the destination system already covers them). This matters doubly when the user is risk-averse.
   - **Before scope locks, route any "best guess" or unverified feasibility assumption to independent verification rather than carrying it forward as settled.** This mirrors a general verification-independence principle (a different model checks judgment calls) worth adopting in your own workspace's cross-cutting rules — the mechanics aren't restated here.
   - **Before designing backup/rollback machinery for in-place writes, check whether the deliverable can be produced entirely on copies in a staging area, leaving originals untouched.** Copy only the subset needing modification into a staging folder (outside any auto-sync boundary), do ALL writes on the copies, and point the downstream consumer at the staging folder. Structural risk elimination beats compensating controls (backups, snapshots, dry runs): the safest write is the one that never happens to the original. Prefer this especially when the user has expressed loss anxiety or the originals live inside a cloud-synced folder, where in-place writes are riskier than they look.
   - **For copy-then-delete or move-heavy projects (photo/music libraries), check destination disk space before starting a batch**, not just before starting the project. Long-running cleanups can run for days/weeks across many batches; available space can change between sessions (other processes, other cleanups, the user themselves). A batch that runs out of space mid-write is a bad failure mode — check headroom against the batch's total size before committing to it.
   - **When a resource the plan depends on turns out unavailable or scarce at execution time — a specific model, an API window, a person's input — scaffold the deliverable rather than either blocking the whole run or silently substituting something the user didn't ask for.** Build the deliverable as far as it can go without the missing resource, and mark each hole it can't fill yet as an explicitly labeled, greppable PENDING slot (e.g. `PENDING: [what it's waiting on] — [what would fill it]`) — record what it's waiting on and what would resolve it. Keep the rest of the deliverable shippable as-is; don't let one missing input block work that doesn't depend on it. Define when/how the retry happens (a deadline, a check-back trigger, an explicit "ping me when X is available again") rather than leaving the PENDING slot open-ended. Surface the blocker and offer cost-aware options (wait and retry, substitute with the user's explicit sign-off, proceed without that piece) instead of quietly picking one.

3. **Execute one batch.** Apply whatever transformation/matching/move logic the project needs. Keep external calls paced conservatively — when in doubt, slower. Log results as you go (per-item status, not just a final summary), so a failure mid-batch doesn't lose what already succeeded.

   - **Before acting on any item, check whether it's already in the target state and skip it if so.** A batch interrupted partway through (crash, power loss, manual stop) and then resumed or re-run should not re-move, re-copy, or re-flag items it already finished. This matters more for multi-day/multi-week projects than short ones, since the odds of an interruption between sessions are much higher.
   - **A move is not access-neutral — check for access-widening before any move into a shared container.** Before moving an item, determine whether the destination folder is shared and whether the item is currently more private than the destination. If the move would widen who can see the item, treat it as an access-control change and escalate for explicit per-item confirmation, regardless of any blanket "reorg approved" the user gave earlier — approval to organize is not approval to expose.
   - **Design shared-state writes so a timed-out call's stragglers can't corrupt them.** Tools with a hard per-call timeout (browser automation, shell process wrappers) can time out while the underlying async work keeps running to completion in the background — and a trailing bulk statement (e.g. appending a whole results array after the main work) can fire minutes later, merging into state a subsequent call has already reset and repopulated. So: (1) never place state-mutating statements after the main work in the same script — have each unit of work persist its own result immediately; (2) size batches to reliably finish within the tool's timeout; (3) after every batch, check the shared state's length and per-item duplicate count before proceeding — a normal-looking total is not proof there are no merged-in duplicates.
   - **A tool-call timeout is not evidence the work failed.** "The call timed out" and "the underlying work failed" are different claims for any tool that spawns a background process (browser tab, shell, subprocess) — the process can finish and write complete, correct output after the tool layer gave up waiting. Before concluding a long-running command failed or needs re-running, check the actual output's state (existence, size, mtime, row count) against the live system rather than trusting the call's own timeout/error status. Re-running on a false failure is how duplicates and double-moves happen.

4. **QC the batch.** Before moving to the next batch, review what execution produced:
   - Confident matches/results → proceed.
   - Weak-but-likely results → proceed, but log them distinctly so they're auditable later.
   - Ambiguous or conflicting results → **flag and stop.** Surface the specific item and why it's ambiguous; wait for a human call rather than guessing. Once the human resolves one case, check whether it sets a standing rule for future identical cases (e.g., "always resolve X this way") so the same question isn't asked repeatedly.
   - **Before treating a flagged item as genuinely ambiguous or absent, rule out tool flakiness.** Matching pipelines that wrap an external API can produce transient false negatives under tight, rapid sequential calls — a re-run in isolation (slower, standalone) can return a clean match for the exact same input that just failed moments earlier. Re-test every FLAG/no-match result once, on its own, before trusting it; only treat it as a true non-match if it still fails on retry.
   - **Don't stop at the top-ranked candidate.** A scoring heuristic can pick a technically-passing match that's clearly worse than another option sitting in the same result set (e.g., picking a "Live Extended Mix" at roughly twice the target length over a plain single a couple seconds off). On anything outside the AUTO tier, glance at the full candidate list, not just whichever one the scorer ranked first — the better match is sometimes already there.
   - **Watch for title-stylization gaps that look like genuine non-matches but aren't.** A text-similarity gate can fail on superficial differences that have nothing to do with whether the item is a real match: source uses an abbreviation where the canonical title is spelled out (e.g. "Pt. 2" vs. "Part II"), source is missing a space that splits one word in the canonical title into a single unbroken token (e.g. "Nothingwrong" vs. "Nothing Wrong"), or the canonical title is itself censored/abbreviated by the platform in a way the source isn't (e.g. an explicit word starred out, or a long descriptive remix credit shortened to "- Remix"). These are real matches that a literal-text gate will wrongly flag — confirm via duration/album/artist before concluding it's a true non-match, don't let the title mismatch alone settle it.
   - **When several flagged items cluster on the same album/EP, go straight to an album-level lookup instead of retrying item-by-item search.** If 2+ flagged or weak items share a source album, query the catalog's album-search endpoint for that release, then pull its full tracklist by track number — this resolves the whole cluster in one or two calls with authoritative titles/durations, and is far more reliable than repeatedly retrying free-text search on each item individually (which is exactly the kind of search that's failing in the first place, e.g. due to censored or non-canonical official titles).
   - **For dedup/identity-matching work specifically, tier the match basis, not just the match confidence.** Filename and/or size match alone is WEAK at best (different files can share both). A content hash match (or, for media, matching duration + codec + a hash/checksum) is the bar for AUTO. Anything resting on filename, folder location, or metadata tags alone — with no hash check — is at most WEAK and should be logged distinctly so a human can spot-check before any delete is final.
   - **Duplicate confirmation before a delete must be content-based — and metadata disagreement is not proof of non-duplication either.** "Duplicate" is a property of content, not metadata. Format conversions and re-saves break size and byte-hash equality while preserving meaning (a PDF and a document-format conversion of the same publication have different sizes, types, and hashes but identical text), so a size/type heuristic produces false NON-duplicates just as filename matching produces false duplicates. Where formats differ, compare the extracted content (text, media fingerprint) — only a content comparison safely authorizes a deletion, in either direction.

   **When a QC pass is itself a scripted validation run or experiment (parsing a tool's output in batch to measure success), check the instrument before spending a run on it:**
   - **Confirm the instrument can register partial movement.** If the tool's failure path emits no gradient signal (no score, distance, or ranked-candidate detail — just a bare skip/fail line), the run can only ever say "all pass" or "all fail," which cannot guide improvement, only detect eventual success. Before the run, ask what the result would look like if an intervention worked *slightly* — if the answer is "identical to complete failure," switch to an interface that exposes the gradient (machine-readable mode, higher verbosity, per-candidate dump) or explicitly record that the run measures only the binary outcome and cannot support tuning decisions.
   - **Establish the ceiling before interpreting the floor.** Before concluding that a *domain* input (data quality, context availability, item difficulty) drives a negative result, verify the tool's own configuration cannot produce that result on its own — compute the best achievable score under current settings and confirm a known-perfect input would clear the acceptance threshold. If a perfect input cannot pass, the experiment is measuring configuration (a default penalty, a threshold interaction), not the domain. Defaults are a silent participant in every experiment: nobody set them, so nobody suspects them, and they can bound the outcome so tightly the measurement can only return one value.
   - **When a comparison's two arms come from different tool modes, confirm both numbers are the same quantity from the same code path** before treating the difference as a finding — one mode printing a per-item success line that another mode never prints can manufacture an apparent difference between two arms that are actually identical.

5. **Verify before moving on.** Where possible, confirm the batch's actual effect against the live system (read back what was written, not just trust that the write call returned success) before starting the next batch.

6. **Document and checkpoint.** Update a running status log (what's done, what's flagged, what's left, any standing rules established) after each batch. This is what makes a multi-session project resumable without re-deriving context — a future session (or a different agent) should be able to read the status log and know exactly where things stand.

   - **Keep the standing-rules log as its own file, not just prose buried in the status log.** For a project spanning many sessions, a future session needs to be able to grep/scan rules directly rather than re-reading a long, growing narrative log to find them. See "Standing rules format" below.

7. **Check in at natural boundaries, not just at the end.** Especially before starting a unit of work that's much bigger or riskier than anything done so far in the project — that's the moment to confirm pacing/chunking with the user rather than assume the same approach scales.

8. **Resuming a session: verify the log before trusting it.** When a new session picks up a multi-day/multi-week project, don't treat the last status-log entry as ground truth for "what's actually done" — re-check the live state of the last batch (file actually moved, item actually deleted, etc.) before continuing to the next one. This guards against two distinct failure modes: a real interruption that happened between sessions (see the skip-if-already-done note in step 3), and a read environment that served a stale/incomplete view of a file or directory without anything having actually gone wrong (if you're on a sandboxed or mounted filesystem, a "missing" or "incomplete" result can be the read layer, not reality — confirm with a fresh read or, if available, a direct check from the live system before concluding work was lost or never happened).

   - **Check for supersession across the project's document set before presenting any "still outstanding" list.** A plan/recommendations doc's own status header is only as fresh as its last edit — and dated execution records are often written into a *different* file (the project tracking doc) than the plan they executed, with nothing forcing the plan's header to update in lockstep. Before building an action list from any doc's self-reported status, grep the project's tracking doc(s) for later dated updates referencing the same file or topic. Reading one doc more carefully doesn't catch this; only comparing across the sibling docs does.
   - **When a session gets stuck on an infrastructure/auth/environment issue, grep the project's own history for the symptom before re-diagnosing from scratch.** Search the status log / runbook for the error symptom or component name (a port number, "OAuth", "callback") before concluding something is broken or missing. A recurring problem already solved 2+ times in the same file is a strong signal the current diagnosis is wrong, not that the environment changed. And once a recurring non-bug is identified, encode it as runnable, reusable tooling (a script or checklist with the gotchas in its own docstring) rather than leaving it as prose in a long history file — prose can be skimmed past or distrusted under time pressure, but a script's existence itself is the answer.

## Anti-patterns

- **Auto-resolving ambiguity to keep momentum.** Speed is not the goal here — correctness without rework is. If QC can't confidently classify something, it stops, every time.
- **One giant unsupervised pass on a large project.** Even if pacing has been clean so far, a 10x-or-more jump in batch size warrants smaller chunks and a check-in, not just "the same approach, more of it."
- **Treating a successful API call as a successful write.** Read back and confirm, especially for anything hard to reverse.
- **Treating a tool-call timeout as a failed run — or as a stopped process.** The background work can outlive the call, finish successfully, and even mutate shared state later. Verify actual output state before re-running, and never leave trailing bulk writes that a straggler can fire after the fact.
- **Assuming a "verify by reading back" call returns the same response shape as the call that wrote the data.** When an external API has multiple endpoint aliases or versions for the same resource (a deprecated path and its replacement, a v1/v2 split), the response schema can differ even when the underlying data doesn't — e.g. one nests the payload under a field named `track`, the read replacement nests the identical payload under `item`. Check the actual response shape (log a raw sample, don't assume from memory or from the other endpoint's docs) before writing verification code against it, or a real successful write can look like 100% mismatches on readback.
- **Losing the standing-rules log.** If the user resolves an ambiguous case once, that resolution should be remembered and applied automatically to future identical cases — don't re-ask the same question.
- **Trusting a FLAG without re-verifying it wasn't transient.** A "no match" from a flaky wrapper around an external API looks identical to a genuine absence. Treating the first failure as final risks silently dropping items that actually exist — re-run flagged items standalone before accepting them as real.
- **Accepting the scorer's top pick without a sanity check.** A passing score isn't the same as the best available option. For anything outside the AUTO tier, a quick look at the rest of the candidate list can catch a heuristic miss the score alone wouldn't surface.
- **Re-running or re-flagging already-finished items after an interruption.** If a batch was cut short, resuming should pick up where it left off, not redo work — check target state before acting.
- **Calling a filename/size match "AUTO" for dedup work.** Without a hash or equivalent content check, that's a WEAK match at best — log it for spot-checking, don't delete on it alone.
- **Inferring duplicate status from metadata in either direction.** Same name/size doesn't prove duplication, and different size/type doesn't prove non-duplication (format conversions preserve content while changing both). Content comparison authorizes deletions; metadata only nominates candidates.
- **Treating a duration-only coincidence as license to call something a duplicate row.** The project's no-dedupe rule (post legitimate duplicate-titled rows independently) only applies when the title itself supports it. A source title that's corrupted/uninformative (e.g. the album name leaking into the track-title field) paired with a near-identical duration to an already-matched track is suggestive, not conclusive — flag it to the user rather than silently assuming it's the same song wearing a bad tag.
- **Trusting a stale read as proof of data loss (or proof of success).** On sandboxed/mounted environments, a Read tool or shell command can serve a frozen snapshot that doesn't reflect the real current file. Before concluding a batch's work is missing, corrupted, or done, re-verify against the live system rather than acting on a single read.
- **Starting a fresh inventory/scan without checking for a prior survey doc.** Point-in-time folder inventories go stale the moment a prior cleanup pass runs — re-scanning cold risks re-flagging already-resolved items or missing that the real ask is a previously-deferred decision, not new duplicates.
- **Quoting a batch size produced by summing separately-generated lists.** That figure is an upper bound on a set, not its cardinality — and it hardens into a "fact" once written into a plan doc. Materialize the de-duplicated union first; counting is a step to execute and verify, not an arithmetic aside.
- **Building an action list from one doc's own status header.** In a multi-document project, the execution record often lives in a sibling doc's dated log entry — check for supersession across the document set before presenting "still outstanding" work.
- **Moving items into a shared destination under a blanket reorg approval.** An access-widening move is a permission change in disguise; re-confirm those individually.
- **Blocking the whole run, or silently substituting a costlier or different resource, when a plan-critical resource turns out scarce or unavailable.** Scaffold the deliverable instead — build what doesn't depend on the missing piece, mark the rest with explicit PENDING slots and a retry protocol, and let the user choose among cost-aware options rather than have one picked for them.
- **Carrying a foreman's "best guess" feasibility assumption into a locked scope without independent verification.** Best-guess flags are exactly the kind of judgment call that needs an independent check before they harden into scope — see the verification-independence pointer in Workflow step 2.

## Status log format

A simple running log works better than a single end-of-project report. Suggested shape per project:

```
## [Unit of work name]
- Status: [not started / in progress / done / blocked]
- Plan: [chunk size, ordering, any constraints]
- Results: [counts by confidence tier]
- Flagged: [specific items needing a human call, with reasons]
- Standing rules established: [pointer to standing-rules file — see below — not the rules themselves]
```

## Standing rules format

Keep a separate, flat file per project (e.g. `standing-rules.md`) so a session can scan it directly instead of mining the status log's prose history. Suggested shape:

```
## Standing rules
- [Trigger/condition] → [resolution] (established [date], from: [unit of work / item that prompted it])
- e.g. "Duplicate JPEGs differing only in filename casing" → keep the one in the dated folder, delete the other (established 2026-06-12, from: Photo Library batch 4)
```

Append-only; once a rule is added, future QC passes should apply it automatically and never re-flag the same situation as ambiguous.

## Notes

- This is a first draft, written to formalize a pattern that's already worked well in practice (Spotify Playlist Migration) rather than from scratch. Expect to iterate on chunk-size defaults, QC tier definitions, and the status log format as it gets used on more real projects — including non-migration greenfield builds, which it has now also handled successfully.
- This skill is about *process*, not about any specific domain's matching/transformation logic — the actual "how do I match track A to track B" or "how do I dedupe these photos" logic is project-specific and lives outside this skill.

---

## Related skills

This skill is self-contained — nothing else in this pack is required.

