---
name: "council-of-agents"
description: "Convene a small council of independent AI agents to resolve a genuinely ambiguous or high-stakes question through structured, hard-capped debate. Use for decisions with real consequences where reasonable analysis could land in different places — an architecture/technology choice, a financial model with material consequences, or an unresolved factual/policy question (e.g. \"does X actually control Y, or is that just an assumption\"). Runs a hard-capped 3-round process — independent Round 1 positions, Round 2 rebuttal, Round 3 synthesis — then returns a SITREP-style report showing agreement/disagreement, even if it never converges. Defaults to a mixed-model council (one Gemini seat) because an all-Claude council's independence is partly illusory. Do NOT use for routine tasks or anything with a clearly correct answer. Trigger on: \"get a council on this,\" \"run a consensus check,\" \"have a few agents debate this.\""
---

# Council of Agents

**Created by Josie Lagarde, designed with Claude (Cowork)** · Created: ~2026-08 (estimated; in use by 2026-08-17)

**License:** Released under CC BY 4.0 — free to use, adapt, and share with
attribution.

A structured way to get a second, third, and fourth opinion — from independent agents, not from yourself — on a question genuinely worth debating. The value of this skill comes entirely from the agents starting independent and only being shown each other's reasoning after they've committed to an initial position. If you write all the positions yourself, or let the agents see each other's work in round 1, you get one perspective wearing three hats, not a real council.

## Read this first: "independent" is weaker than it sounds

Not showing council members each other's work in Round 1 gives you **procedural** independence. It does not give you **statistical** independence, and the difference is the single most important thing to understand about this skill.

Agents differ from each other in only three ways: their context, their scaffolding, and the model underneath them. Hold all three constant — which is exactly what this skill's identical-framing rule does — and the members will tend to produce the same answer, not because it's right, but because they're samples from the same distribution. Anthropic's multiagent research (Aug 2026) found agents given identical prompts converging on identical arbitrary choices at startling rates: 18 of 30 independently picking the same git branch name, agents in a writing workshop independently choosing the same story title, over half of a swarm asked to "build something impressive" building ray tracers or self-hosting compilers.

Two consequences, both load-bearing:

**1. Convergence in a model-homogeneous council is weak evidence.** It is the expected result of sampling one distribution three times. Do not report it as though three minds checked each other. **Divergence is the informative outcome** — something had to genuinely pull the members apart to produce it, so a split council is telling you more than a unanimous one.

**2. Model diversity is the only real fix.** Varying the framing helps a little; varying the model helps a lot. This is why an outside-provider seat is now the default rather than an opt-in (see below).

Never let a council's unanimity substitute for a source of truth. A council cannot out-vote a fact, and three agents agreeing is not three confirmations.

## When to use this (and when not to)

This skill is for questions where **a thoughtful, well-informed person could reasonably land in more than one place**, and where **being wrong is costly enough to justify the extra time and tokens**. Good fits:

- A technology or architecture decision with real tradeoffs and no obviously dominant option (Kafka vs. SQS, monolith vs. services, build vs. buy).
- A financial model or forecast whose assumptions materially change the recommendation, and where different reasonable modeling choices could point different ways.
- A genuinely unclear factual, contractual, or policy question — especially fact-vs-supposition questions like "does X actually control Y here, or is that just something we've been assuming?"

**Anti-patterns — do not use this skill for:**

- Anything with a single verifiable correct answer (a lookup, a calculation, a "what does the docs say" question). Just answer it, or check the source. A council can't out-vote a fact.
- Routine work: writing a status update, formatting a document, standard code review, everyday debugging. These have a normal path; forcing a 3-agent debate onto them is theater, not rigor.
- Brainstorming or idea generation. That wants divergence and volume, not convergence — use a brainstorming approach instead.
- Anything where you already have a source of truth to check against (a spec, a contract, a config file). Read the source first. Only reach for a council if the source itself is ambiguous or silent.
- Low-stakes decisions where being wrong costs little. The whole justification for spending 3 rounds × several agents is that the decision matters.

If you're not sure it qualifies, ask yourself: "if I just picked one answer and moved on, would anyone actually push back, and would it matter if they were right?" If no, skip the council and just answer.

## The structure, and why it's capped at 3 rounds

Run exactly three rounds. Not two, not five — three, every time, regardless of how the debate is going. This is a structural rule, not a suggestion: left open-ended, multiple agents iterating on each other's reasoning will keep "converging" round after round, but what's often happening past round 3 is social conformity (agents updating toward the group because disagreement feels effortful) rather than genuine persuasion. Three rounds is enough for real updating to happen if it's going to, and capping it forces the process to end in an honest state — either real convergence, or a clearly-articulated real disagreement — rather than a manufactured one.

### Round 1 — Independent positions

Spawn the council members in parallel, in a single message with multiple Agent tool calls (or your environment's equivalent). Every member gets the **identical** framing of the question: full context, constraints, and what a good answer needs to address. None of them should see that other members exist yet, and none should be told what to conclude.

Identical framing is deliberate — it keeps the comparison fair — but be aware it also maximizes correlation between members (see "Read this first" above). The identical-framing rule is what makes a split meaningful; it is also why agreement means less than it appears to.

Do not assign members artificial personas or forced opposing sides (e.g. "you argue for X, you argue for Y") by default — that manufactures disagreement rather than revealing it, and defeats the purpose of finding out whether independent reasoning actually converges. The only reason to assign distinct lenses is if the question genuinely has separate stakeholder perspectives that need explicit representation (e.g. "evaluate this purely from a security standpoint" as one member and "evaluate this purely from a cost standpoint" as another) — and if you do this, say so plainly in the final report so the reader knows the disagreement may be structural rather than a genuine split in judgment.

Each Round 1 prompt should ask the member to produce: its position/recommendation, its reasoning, its key assumptions, and what would change its mind. Save each member's output.

### Round 2 — Rebuttal with visibility

Spawn the council members again, in parallel. Each one now receives: the original question, its own Round 1 output, and every other member's Round 1 output (labeled Council Member A/B/C, not by whose is whose beyond their own). Ask each member to state where it agrees, where it disagrees, and whether it's updating its position — with reasoning either way.

Be explicit in this prompt that **updating toward another position because you find its reasoning more compelling is good; updating just to converge or avoid disagreement is not.** Ask each member to say plainly if it is holding its position despite the others disagreeing, and why.

**Required question — the unique-information prompt.** Every Round 2 prompt must additionally ask, in these terms:

> Is there any consideration, fact, risk, or assumption that you raised or are aware of which **no other member mentioned**? State it explicitly, even if it seems minor, and even if the others appear to have already reached consensus without it. If the group is converging and you hold something unshared, say so — do not let it drop because the discussion has moved on.

This is not optional politeness. It targets a specific documented failure: in "hidden profile" tasks — where the decisive evidence is distributed across members rather than shared — agent groups scored 17–36% against a near-100% solo ceiling. They failed not from lack of intelligence but because unshared facts were never volunteered, or were dropped once an apparent consensus formed. Round 2 is structurally a hidden-profile setup. Telling members that holding ground is *allowed* is passive; asking them directly for their unshared information is active, and it is the difference between the two failure rates.

If a member surfaces a unique consideration in Round 2, carry it explicitly into every Round 3 prompt so it cannot quietly disappear in the final round.

### Round 3 — Final synthesis attempt

Spawn the council members one final time with all Round 2 outputs. Ask each for a **final** position, explicitly classified as: converged with the group, converged with some but not all, or still in disagreement — plus the specific remaining point of difference if any. This is the last round. Do not spawn a fourth round under any circumstances, even if the members report they're "close" or ask for more discussion. Close-but-not-converged is itself a valid, useful result.

After collecting Round 3 outputs, **you** (not another subagent) synthesize the final report — see format below. Determine the outcome:

- **Converged**: all members land on the same recommendation/answer, even if their reasoning paths differed, with no member reporting an unresolved objection. **In a model-homogeneous council, treat this as weak evidence and say so in the report** — see the interpretation rule below.
- **Partially converged**: a majority agree but at least one member maintains a substantive, reasoned dissent. Report this as partial — do not round up to "converged" and bury the dissent. A lone reasoned dissenter in an otherwise-correlated council is a high-signal event; give its reasoning real space in the report rather than a footnote.
- **Did not converge**: no majority, or the remaining positions are fundamentally incompatible (they can't both be true / recommend opposite actions).

**Interpretation rule — mandatory.** Whenever the outcome is CONVERGED and every seat was filled by the same model, the outcome line must carry the qualifier: *"model-homogeneous council — convergence is weak evidence."* Never present bare unanimity from a single-model council as though it were corroboration.

## How many council members

Default to **3**. It's the smallest number that avoids a straight tie, keeps cost reasonable, and still gives enough independent signal to tell a real disagreement from one agent having an off day.

- Use **2** only for a strict either/or comparison where a third perspective wouldn't add a genuinely different angle — but know that a 2-way split has no built-in tiebreaker, so a non-convergent result is more likely and that's fine; report it as such.
- Use **4** only when the question has more than three genuinely distinct angles that deserve independent representation (e.g., the decision has separate cost, security, reliability, and velocity implications that could each plausibly point different directions). Don't go beyond 4 — more members adds noise and cost without a proportional increase in signal, and makes the final synthesis harder to read cleanly.

Use `general-purpose` as the default agent type for council members. If the question is specifically an architecture/system-design decision and a more specialized planning agent type is available in your environment, that can be a reasonable substitute for one or more members — but keep every member's framing identical regardless of which agent type executes it.

## Mixed-model council — now the default

**Default: 2 Claude seats + 1 seat filled by a model from a different provider, in a 3-member council.** This changed from opt-in to default deliberately. A council whose seats all run the same model overstates its own confidence — its members share the same blind spots, so their agreement is substantially an artifact of shared architecture rather than corroboration. One seat filled by a genuinely different model is the cheapest available correction, and the cost is a single API call.

**The requirement is model diversity, not any particular vendor.** Gemini is the default here because it is what this skill was built and tested against, so the operational notes below are concrete about it. Any model from a different provider serves the same purpose — ChatGPT, DeepSeek, Mistral, a locally-hosted open-weights model, whatever you can reach. Substitute freely; what matters is that the outside seat does not share an architecture with the Claude seats. Name whichever model you used in the report.

**The outside seat is not zero-setup.** Reaching any of these needs a provider API key and some way to call it from your assistant — an MCP server, a CLI, or a direct HTTP call. Gemini specifically needs a Google AI Studio API key and a configured MCP server or equivalent. Budget for that before assuming the default is available.

Run an **all-Claude council only when** no outside model is reachable, or the user explicitly asks for all-Claude. In either case the report must say so and must carry the weak-evidence qualifier on any converged outcome.

**How it works:** the 3-round structure above is completely unchanged — same Round 1 independence, Round 2 rebuttal-with-visibility (including the required unique-information question), Round 3 synthesis, same hard cap of 3 rounds, same identical-framing rule, same prohibition on forced personas. The only difference is mechanical: for the outside seat(s), instead of spawning a Claude subagent via the Agent tool, call that model directly — via its MCP tool, CLI, or API — with that round's exact prompt — the same framing every Claude seat receives (question, context, constraints in Round 1; its own prior output plus the other members' labeled Council Member A/B/C in Rounds 2 and 3). The outside model's response becomes that seat's output for the round, saved and fed forward into the next round exactly like a Claude member's — it never sees another member's identity, only their labeled output, same as every other seat.

**More outside seats:** for especially high-stakes or contested questions, 2 outside + 2 Claude in a 4-member council is reasonable — and two *different* outside providers is better than two seats on the same one. A council with no Claude seats, or with every seat on one provider, defeats the point of a *council* (there's nothing to compare against); if the user asks for that, say plainly it is no longer a mixed council, just that one model's opinion, and confirm that is really what they want before running it.

**Watch for this:** when a council splits cleanly along model lines — all Claude seats one way, the outside seat the other — that is not automatically a 2-against-1 to be resolved by majority. It may be exactly the architectural blind spot the mixed council exists to surface. Report a model-aligned split explicitly as such, and do not let seat-counting decide it.

**In the report:** the Council Composition section must name which model filled which seat — not just "an outside model", the actual model — because this is load-bearing information for reading the rest of the report, not a footnote.

**If the outside seat errors, read the status class before concluding it is dead.** The notes below are Gemini-specific (verified 2026-08-15) because that is the default this skill was tested against; the general lesson — that an error class tells you whether retrying can possibly help — applies to any provider:

- **404 NOT_FOUND** = stale model name. Client packages hardcode model defaults that outlive the models themselves; one published months ago can name a model the provider has since shut down. Fix by pinning a current model in whatever your integration reads its configuration from — a config file, a CLI flag, or an environment setting — checked against the provider's own deprecations page. Restart the integration afterwards; most read config only at startup.
- **429 with a stated limit of 0** = an entitlement problem, not a rate problem — no retry schedule fixes it, because the quota is not exhausted, it was never granted. As of 2026-08 a free-tier Gemini key had **zero pro-class quota** while flash-class models worked. The resolution is to point the higher-tier slot at a model the key can actually reach, so the seat degrades instead of erroring. Read the limit from the error payload rather than trusting any number written here — the response states the current quota, and these change.
- **429 with a non-zero limit** = genuine quota exhaustion; retry later or use the flash seat.
- Verify any fix with a direct API call using the same credentials — don't infer from the MCP tool's error text alone.

**Weight the seat accordingly:** on a free key the Gemini seat is flash-class, so treat its output as a fast *independent* perspective — which is the whole point of the seat — not a frontier-pro one, and don't burn retries on pro 429s whose limit is 0. The same caution applies to any outside model run on a free or rate-limited tier: independence is the value, not raw capability.


## Output format

Always deliver the final result in this structure, whether or not the council converged. Do not just paste each round's raw output — synthesize it.

```markdown
# Council of Agents: [Question / Decision Title]

## Objective
[1-2 sentences: what decision is being made, and why it warranted a council rather than a direct answer.]

## Council Composition
[Number of members, agent type(s)/model(s) used — explicitly naming which model filled each outside seat — and whether any were assigned a distinct lens; if so, name it. If the council was model-homogeneous, say so here and say why.]

## Round-by-Round Summary
- **Round 1 (independent):** [one line per member — initial position]
- **Round 2 (rebuttal):** [what shifted, if anything, and who held ground]
- **Round 3 (final):** [each member's final position]

## Outcome: CONVERGED / PARTIALLY CONVERGED / DID NOT CONVERGE
[If CONVERGED and model-homogeneous, append: "model-homogeneous council — convergence is weak evidence."]

## Unshared Considerations Surfaced
[Anything a single member raised that no other member did, from the Round 2 unique-information prompt, and what happened to it — adopted by others, rejected with reasoning, or never addressed. "None surfaced" is a valid entry, but state it; silence here should be a deliberate finding rather than an omission.]

## Points of Agreement
[Bulleted, specific — not vague "everyone thinks this is important."]

## Points of Disagreement
[Bulleted, specific — name which member(s) hold which position. Flag explicitly if the split falls along model lines. If converged, this section can say "None" briefly rather than being omitted.]

## Root Cause of Disagreement
[Only when not fully converged. Is it a factual/data gap that more information would resolve? A genuine values/priority tradeoff (e.g. cost vs. reliability) where more debate won't help? Differing assumptions that were never reconciled? A model-architecture difference? Name the actual mechanism, not just "they disagreed."]

## Recommendation / Next Step
[If converged or partially converged: state the consensus recommendation plainly, and flag any dissent from a partial convergence.
If it did not converge: do NOT pick a winner yourself or split the difference. State clearly what the user needs to weigh or what additional information would resolve it, so they can make the final call with full visibility into the disagreement — that disagreement, cleanly presented, is the deliverable.]
```

## A note on honesty over neatness

The temptation, once you're synthesizing three rounds of output, is to smooth things into a tidy consensus because that reads better. Resist it. If the council didn't converge, a clean report of *why* is more useful to the user than a false sense of resolution — they're the one who has to live with the decision, and they can only make a good final call if they can see the actual shape of the disagreement, not a papered-over version of it.

The corollary, given everything above: do not treat a unanimous council as a strong result just because it is easy to write up. Unanimity among correlated agents is the cheapest outcome to produce and the least informative one to receive. The dissent, the model-line split, and the unshared consideration nobody picked up are where the actual value of running a council lives.

---

## Related skills

**In this pack:** `pec` — the Plain-English Challenge Protocol. The two are complementary, not interchangeable. PEC stress-tests **one** plan or document against a fixed set of questions, and can optionally pull in a single outside model for a second read. This skill is the **escalation** past that: a multi-round debate across several members when a question is genuinely unresolved, or high-stakes enough that one careful review is not enough assurance. Reach for PEC first — it is far cheaper. Convene a council when PEC has run and the disagreement or the stakes survived it.

Neither requires the other. Each works on its own.

**Needs:** the ability to call a second, non-Claude model (the default council seats one). Without that, it degrades to an all-Claude council, which the skill itself flags as weaker evidence.

