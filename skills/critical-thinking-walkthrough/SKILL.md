---
name: "critical-thinking-walkthrough"
description: "A six-lens walkthrough for interrogating new information someone else put in front of you — an article, a claim, a vendor pitch, a policy change, a proposal, a statistic, a piece of advice — before you form a position on it. Runs Who / What / Where / When / Why / How selectively, picking only the questions that actually bite. Defaults to GUIDED mode: asks the user one question at a time and waits, so the thinking is theirs; switches to ANALYSIS mode on request and delivers the finished pass instead. Use when the user shares something they've read or been told and wants help thinking about it: \"help me think this through\", \"what am I missing here\", \"is this legit\", \"poke at this\", \"someone told me X — is that right\", \"what should I make of this\", \"walk me through this\", \"ask me questions about this\", or pastes an article, claim, or offer and asks what to make of it. Not for stress-testing a plan the user owns (that is a plan-review protocol), not for resolving a decision they are already split on (that is a debate protocol), and not for teaching a concept they want to understand (that is a learning skill)."
---

# Critical Thinking Walkthrough

**Created by Josie Lagarde, designed with Claude (Cowork)** · Created: 2026-09-01

**License:** Released under CC BY 4.0 — free to use, adapt, and share with
attribution.

**Provenance:** the six-lens Who / What / Where / When / Why / How framing is
adapted from the widely-circulated "Ultimate Cheatsheet for Critical Thinking"
question poster published by the Global Digital Citizen Foundation. The lens
structure is theirs; the question banks, selection rule, facilitation rules,
and output formats below are original to this skill. The closing "smallest
step" question is a handwritten addition to the original poster and is treated
here as the required last move, not an optional extra.

---

## What this is for

Someone hands the user information they did not generate — an article, a
statistic, a vendor claim, a policy change, a piece of advice, a proposal, a
headline — and they do not yet have a position on it. This skill builds one.

**The default is that the user builds it.** The original artifact is a set of
questions to *ask*, not answers to receive, and the thinking only transfers if
they do it. Handing over a finished analysis is the fallback, not the default.

It is a **thinking pass, not a fact-check.** Verification is one move inside
it (the Where lens), not the whole job.

### When NOT to use it

- **The user owns the plan and wants it attacked** → that is a plan-review /
  red-team protocol. Different job: there the goal is finding failure modes in
  something already committed to.
- **The user is already split between two known options** → that is a
  structured-debate or decision protocol. This skill is for *before* there are
  positions to weigh.
- **The user wants to understand how something works** → that is a learning
  skill. Curiosity is not scepticism.
- **A single factual lookup** ("is this number right?") → just look it up. Do
  not wrap a one-minute check in a six-lens walkthrough.

If the request is genuinely two of these at once, do this pass first, then say
plainly which other protocol the follow-on needs.

---

## Two modes

**GUIDED (default).** One question at a time, addressed to the user, then
stop and wait for a real answer. The user reaches the position; you facilitate
and add what they missed at the end. Runs about five to eight exchanges.

**ANALYSIS.** You run the whole pass and deliver it finished. Use only when
the user asks for it — "just tell me", "I don't have time for this", "give me
the short version" — or when they are clearly mid-task and using this as a
step in something larger rather than as the task itself.

**Choosing:** default to GUIDED without announcing a mode choice or asking
permission to begin. Just start (see Step 0). A one-line offer of the exit
belongs in the first message — "say *just tell me* any time and I'll take it
from here" — and then never again.

**Switching:** switch to ANALYSIS the moment the user asks, mid-pass, with no
friction and no "are you sure". Carry their answers so far into it — they have
already done part of the work and should not have it discarded. Switching back
is equally cheap.

**Read the room over the default.** Terse replies, "I don't know" twice
running, or answers getting shorter each turn mean the questions are not
landing. Say so plainly, offer the analysis, and let them take it.

---

## Step 0 — Fix the claim

Before any lens, the thing being examined gets written as a single declarative
sentence in plain language, hedges stripped.

This step does more work than it looks like. Most weak claims fail here: they
cannot survive being written down without their qualifiers, or they turn out
to be three claims wearing one coat. If that happens, split them and settle
which one is being examined.

**In GUIDED mode:** draft the sentence yourself and hand it to the user to
correct — "Here's what I think it boils down to: *[sentence]*. Is that the
claim, or have I bent it?" A draft to react to is far easier to engage with
than a blank prompt, and their correction is itself the first real finding.

Then establish, briefly:

- **Source** — who is saying this, and how it reached them.
- **Stake** — what changes for them depending on whether it is true. If
  nothing changes, say so and stop; not everything deserves a walkthrough.

In GUIDED mode, **stake is the user's to answer, not yours to assume** — it is
usually the highest-yield question in the whole pass and you cannot know it.

If the input is too thin to state as a claim, do not pad it. Say what is
missing and ask for it.

---

## The Six Lenses

Each lens has a question bank. **These are candidates, not a checklist** — the
bank exists so you can find the sharp question, not so you can ask all of
them. In GUIDED mode, rephrase the chosen question in the user's own terms and
about their actual input; asking a bank question verbatim reads as a form.

### Who
- Who benefits if this is believed and acted on? Who pays?
- Who is most directly affected, and were they consulted or only described?
- Who decides — and is that the same party as the one presenting it?
- Who is *not* in this picture who obviously should be?
- Whose expertise would change the read on it, and have they been heard from?
- Who else is saying this, and are they independent of the original source or
  citing it?

### What
- What is the strongest version of the opposing case?
- What would have to be true for this to hold, and is it?
- What is the best case, the worst case, and the boring likely case?
- What is being left out — and is the omission convenient for someone?
- What is the actual claim versus the implied one? (What it says versus what
  it wants you to conclude.)
- What is the alternative explanation for the same evidence?

### Where
- Where did this come from originally — and is the chain of citation intact,
  or does everyone cite everyone else back to nothing?
- Where would this be observable in the real world, and is it?
- Where does this hold and where does it break? (Context, scale, geography,
  population.)
- Where would you go to disconfirm it, if you wanted to?
- Where are the comparable cases, and how did they turn out?

### When
- When was this true? Is it still?
- What is the time horizon — does it hold next quarter, next decade, both?
- When would you know you were wrong? Name the observable.
- Is the timing of *this being said now* itself informative?
- When does the cost of acting on it become unrecoverable?

### Why
- Why is this being said now, by this source, in this form?
- Why has it been this way for so long — is the status quo evidence, inertia,
  or someone's interest?
- Why would a smart, honest person disagree?
- Why does it feel persuasive? Separate the argument from its delivery.
- Why is this relevant to this user specifically, versus generally
  interesting?

### How
- How would you know the difference between this being true and false?
- How was this measured, and by whom, and what does the method exclude?
- How does this change if the scale changes — ten times bigger, ten times
  smaller?
- How does it get used in practice, by people with their own incentives?
- How is this similar to something that already went well or badly?
- How would acting on it be reversed if it turned out wrong?
- Will this actually be used, by anyone, in the form described?

---

## Selection Rule

The value of this skill is subtraction. A pass that works through thirty
questions has done nobody's thinking.

1. **At most two questions per lens, and skip lenses that do not bite.** A
   well-run pass lands on six to ten questions total.
2. **A skipped lens gets one line saying why** — "Who: single-author blog
   post, nobody stands to gain, skipped." Visible skips keep the pass honest;
   silent ones make it look thorough when it wasn't.
3. **Prefer the question whose answer would change the conclusion.** If either
   answer leaves you in the same place, it is decorative. Cut it.
4. **Every question asked gets answered** — by the user in GUIDED mode, by you
   in ANALYSIS mode. A question nobody can answer is not dropped; it is
   recorded as open, with the specific thing that would resolve it ("unknown —
   the study's sample size, which isn't in the summary; the paper would say").
   Unanswered questions left as rhetorical flourish are the main failure of
   this whole genre.
5. **Verify rather than speculate, where verification is cheap.** If a source,
   date, or number can be checked in the time it takes to write a paragraph
   guessing about it, check it. Say which parts were verified and which were
   reasoned about.
6. **Do not manufacture doubt.** If the thing holds up, the finding is "this
   holds up, here is the one soft spot." Scepticism that always concludes
   "it's complicated" is as useless as credulity.

---

## Facilitation Rules (GUIDED mode)

These are the mode. Without them it degrades into an analysis with question
marks in it.

1. **One question per turn. Then stop.** Not two, not "and also". End the
   message on the question mark — no trailing observation for them to react to
   instead of answering.
2. **Never ask and answer in the same breath.** "Who benefits? Probably the
   vendor, right?" is not a question, it is a conclusion with a tag. If you
   already know the answer and it is worth saying, say it as a statement and
   ask a different question.
3. **Do not grade the answer.** No "great point", no scoring. Reflect it back
   in one line so they know it landed — "so the source is the company that
   sells the thing" — and move.
4. **Follow their answer where it goes.** A user answer that opens a sharper
   line than your planned next question wins. The lens order is not a script;
   the six lenses are coverage, not sequence.
5. **"I don't know" is a valid answer and a normal one.** Do not repeat the
   question louder. Offer two or three concrete possibilities to react against
   — reacting is much easier than generating — or say plainly that it is
   unknowable from here and log it as open.
6. **Contribute facts, withhold conclusions.** If cheap verification changes
   the picture, say the fact ("the 2019 study it cites was retracted in 2021")
   — sitting on it while they reason from a false premise is not facilitation,
   it is a waste of their time. But hand over the *fact*, not what it means.
   What it means is the thing they are here to work out.
7. **Never ask a question you have already answered for them,** or one their
   previous answer covered. Both read as not listening.
8. **Keep your turns short.** A paragraph of framing before the question buries
   it. Context, then the question, then stop.
9. **Watch the length.** Past eight or so exchanges, close it out — offer to
   land it and add what you noticed, rather than continuing to lens six.

---

## Output Format

### GUIDED mode — the close

When the questions are done, close in the user's voice, not yours:

```
**Where you landed:** [their position, assembled from their own answers, in
their words — quote them where they said it well. If their answers point
somewhere they haven't said out loud yet, say so as a question: "reading it
back, it sounds like X — fair?"]

**What I'd add:** [the one or two things they didn't raise, and anything you
verified. Brief. This is a footnote to their thinking, not a replacement pass.
If they covered it, say so and skip the section.]

**Still open:** [each unresolved question, with what would settle it. Omit if
nothing is open.]

**Smallest next step:** [one concrete action, doable this week, that either
tests the claim or acts on it at low cost. Never "do more research" — name the
specific thing to read, ask, measure, or try. Offer it; let them adjust it.]
```

**Do not overwrite their conclusion with yours.** If you genuinely disagree
with where they landed, say so plainly in one line under "What I'd add" and
give the reason — but their position stays as the header. Disagreeing openly
is honest; quietly replacing their conclusion with yours defeats the exercise.

### ANALYSIS mode

```
**The claim:** [one sentence, hedges stripped]
**Source:** [who, and how it reached them]
**Stake:** [what changes depending on the answer]

| Lens | The question that mattered | What I found |
| :--- | :--- | :--- |
| Who | ... | ... |
| What | ... | ... |

*Skipped: [lens — one-line reason each]*

**Where I landed:** [2–4 sentences. An actual position — "this is probably
right, and the reason it feels shakier than it is, is X" or "the core claim
holds but the recommendation attached to it doesn't follow." Not a summary of
the table.]

**Still open:** [unresolved questions, with what would settle each.]

**Smallest next step:** [as above.]
```

Keep table cells to one to three sentences. If a lens surfaced two things, put
the sharper one in the cell and fold the second in as a trailing clause rather
than adding rows.

---

## Pre-flight check

Rules written once at the top of a skill get violated in the flow of producing
output. Check before delivering — in GUIDED mode, check items 1–4 before
*every* turn, not just at the close.

**Every GUIDED turn:**

1. Is there exactly **one** question in this message, and does the message end
   on it?
2. Did you avoid answering it yourself in the same message?
3. Is it phrased in their terms and about their actual input — not lifted
   verbatim from the bank?
4. Have they already answered this, or did you already tell them the answer
   earlier?

**At the close (both modes):**

5. Is the claim **one** sentence, hedges removed — and one claim, not three?
6. At most two questions per lens, with every skipped lens accounted for in
   one line?
7. Is every question asked either answered or under "Still open" with the
   thing that would resolve it?
8. Did you say which parts were **verified** versus **reasoned about**, and
   skip no cheap verification in favour of speculation?
9. GUIDED: is the closing position **theirs**, assembled from their answers,
   with your additions clearly marked as additions?
10. Is the smallest next step concrete and small — a specific thing to read,
    ask, measure, or try — not "do more research"?
11. Did you distinguish what the source **says** from what it **implies** from
    what was **concluded**? Three different things; label them.
12. If the material holds up, did you say so plainly instead of hunting for a
    flaw to justify the exercise?

---

## Anti-patterns

- **Fake-Socratic.** Asking a question you immediately answer, or asking one
  whose "right" answer you are steering toward. If you want to make a point,
  make it.
- **The question barrage.** Three or four questions in one message. The user
  answers the easiest and the rest are lost, and it reads as an interrogation.
- **The question dump.** Reproducing the whole bank. This is the failure the
  Selection Rule exists to prevent.
- **Withholding known facts to preserve the exercise.** Facilitation is not
  letting someone reason from a retracted study because correcting them would
  spoil the flow.
- **Quiz-show energy.** "Good!", "Exactly!", "Not quite — think about who's
  paying." They are not being tested.
- **Scepticism theatre.** Concluding "there are considerations on both sides"
  when the evidence points one way.
- **Attacking the source instead of the claim.** Who benefits is a lens for
  finding what to check, not a substitute for checking it. A biased source can
  be right.
- **Answering a different, easier question.** "Is this study well-designed"
  quietly becoming "do I like this conclusion."
- **Ending without a position.** The user came in without one; if they leave
  without one, the pass failed.

---

## Related skills

**Escalation:** if the walkthrough ends genuinely split — two readings both
survive the six lenses and the stakes are high — hand off to a structured
multi-round debate protocol rather than running this pass again harder.
Running it twice does not break a real tie.

**Boundary:** a plan-review / red-team protocol handles work the user owns and
is committed to; this one handles information they have just received and do
not yet have a position on. If both are wanted, do this first — you cannot
stress-test a plan built on a claim nobody has examined.

Neither is required. This skill works on its own.
