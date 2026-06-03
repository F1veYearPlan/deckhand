# Volatility and Search

Read this during Phase 2 (the Verifier step). It governs what to verify, how to
verify it, and the hard rule that you flag but never edit the operator's notes.

## The honest limits, stated to the operator

State these plainly during Phase 1/2:
- You can check the **claims as written**. You **cannot** verify the operator's
  understanding or mental model.
- LLM fact-checking is fallible, and most fallible on exactly the volatile
  material below. Treat your output as a **confidence-graded flag, not a verdict.**
  Tell the operator to verify anything important independently.

## Stable vs volatile

**Stable** (card confidently; rarely needs re-checking): OSI/TCP-IP fundamentals,
cryptographic primitives, classical attacks, RFC numbers, core OS behavior,
established anatomy and physiology, settled mechanisms.

**Volatile** (verify, date, and version): specific tool flags and versions,
vendor product names/features, current CVE details, "best practice" guidance,
cloud-provider specifics, dosing/guideline figures, anything that changes with
releases or revisions.

## The forced-search rule

For any claim that is volatile **or** independently checkable and load-bearing,
run a web search before a card is built on it. Do not rely on training data for
volatile facts. If web search is unavailable, mark the claim **unverified** and
flag it rather than guessing.

## Flag, do not edit

If a claim is factually wrong, disputed, or missing context needed to be correct:
- **Do not edit the operator's notes.**
- **Do not build a card from the disputed claim.**
- Surface it to the operator with what you found and the source, and let them decide
  whether to research further, expand the note, or drop it.

This preserves the operator as the author and avoids encoding a "correction" that may
itself be wrong.

(For claims that are merely *outdated* rather than wrong, see the next section —
the handling is deliberately different.)

## Outdated ≠ wrong — never "correct" a still-relevant legacy fact

A fact being *no longer the current default* is not the same as it being *false* —
and in many domains the older fact is exactly what the operator means to retain.
Separate the two cases and act differently:

- **Factually wrong** (false in its own context, a misread, a transcription slip):
  handle it under "Flag, do not edit" above.
- **Outdated / superseded / legacy but still meaningful:** do **not** relabel it an
  error, demote it, strip it, or swap in the modern answer. The "old" fact often
  carries real weight. Domain examples (illustrative, NOT a closed list — this
  applies to any field):
  - **Security:** legacy or misconfigured services, ports, and protocols are
    frequently the *whole point* — the way in.
  - **Medicine:** superseded drugs, dosages, or guidelines still matter for older
    patients, history-taking, exams, and recognizing what others prescribed.
  - **Law / standards / engineering:** repealed statutes, prior code versions, and
    deprecated specs still govern old cases, systems, and audits.

When a claim is outdated-but-relevant, **enrich, don't overwrite:**
- Add the current state *alongside* the operator's fact (what's current, since when,
  why it changed) — never in place of it.
- Surface it as an explicit, optional enhancement: "Your note reflects X; the
  current default is Y. Want both, or keep the focus on X?"
- Let the operator decide what to card. They own the goal and know why X is in there.
- If you do card both, date and source each (see "Dating and sourcing" below).

**The failure mode to avoid:** silently rewriting the operator's fact to the "modern"
answer and clozing that — which can train them *away* from knowledge they
deliberately captured. Before flagging anything as an error, ask: "Is this
*false*, or just *not-the-default-anymore*?" Only the first is a correction; the
second is, at most, an addition the operator approves.

## Two distinct search purposes — don't conflate them

1. **Verification search (Phase 2):** confirm a claim is true/current. Output is a
   flag to the operator.
2. **Grounding search (Phase 3):** fetch ~1 sentence of Context to anchor a fact
   when the notes don't supply it. Output is a Context line. Keep it bounded — one
   sentence of grounding, not a research essay.

## Dating and sourcing volatile facts

- Put the **version/year in the card text** itself: "As of <tool> <version>, the
  flag is…". This makes staleness visible during review.
- Add a `Date` (YYYY-MM-DD) and a real `Source` to Back Extra.
- Tag volatile cards `type::volatile` so they can be reviewed-and-updated on a
  cadence. Be willing to delete and rewrite — not just edit — when the world moves.
