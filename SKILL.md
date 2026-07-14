---
name: deckhand
description: >-
  Turn a user's notes or study material into high-quality Anki cloze-deletion flashcards built for long-term recall, applying spaced-repetition best practices (atomic facts, mandatory context anchor, interference-busting) and an adversarial review pass. Use whenever the user wants to make flashcards, a deck, cloze deletions, or spaced-repetition/active-recall study material from notes, lectures, documentation, or mentions Anki, cloze, "carding," spaced repetition, or memorizing a body of material. Trigger even on a bare "make cards from this." Full methodology loads with the skill.
---

# Overview

Turn the operator's notes into atomic, recall-optimized Anki cards.

## Guidelines

This skill relies on capabilities that exist in **Claude Code or Cowork**:
- **Subagents** for the Phase 4 adversarial review (a fresh reviewer with no
  build context). If subagents are unavailable, do the review inline as a
  best-effort sanity check and tell the operator it is less independent.
- **Web search** for Phase 2 verification and Phase 3 grounding. If unavailable,
  flag every volatile claim as unverified rather than guessing.
- **Strong source material** Cards built on confusion will only encode confusion. 
  If at any point the source material is confusing, scrambled, or it seems like
  the operator does not have a strong grasp on the material, stop immediately and
  say so.
- **Known ceiling of pre-commit review.** No review pass — agent or human — can
  catch the card flaws that only surface through repeated study (wordy, shallow,
  or pattern-matchable-but-plausible cards). Leeches that emerge later are rewrite
  signals about the card, not operator failures — and returning operators can feed
  them back in (Phase 1) as labeled exemplars that sharpen the next batch.

## Reference files

| File | Read it during | Covers |
|---|---|---|
| `references/card-principles.md` | Phase 3 (Build) **and** Phase 4 (Review) | How to design every card: atomicity, the five properties, cloze rules, the Context anchor, the cloze note type, domain pitfalls, the mnemonic/image/importance gates, and the importable TSV output format |
| `references/volatility-and-search.md` | Phase 2 (Pre-flight / Verifier) | What counts as volatile, the forced-search rule, how to flag without editing, the two distinct search purposes, dating and sourcing |
| `references/tag-taxonomy.md` | Phase 3 (Build, tagging step) | The cross-domain tag axes, hygiene rules, the read-existing-tags-first requirement, deck-vs-tag policy |

## Workflow

### Phase 1 — Setup

Before anything else, confirm three things with the operator:
- **Ask for the users current deck** If they have a current deck, it would be best to provide it
  to avoid carding duplicates and for Claude to copy the operators tag scheme and voice. 
  If they're a returning operator, also ask for two quick exports from Anki's
  browser: `tag:leech` (cards that keep failing) and `prop:ivl>=90` (cards that
  stuck). These become labeled bad/good exemplars from their own collection that
  ground the Phase-4 review; every run they supply them, the review gets sharper.
- **Ask what the material is for.** One line — the exam, cert, rotation, or goal it
  serves. This anchors the Phase-2 synthesis gate and scoping decisions throughout:
  what's worth carding is defined by the goal, not by the notes alone. If the
  material is standard content for a major exam (USMLE steps, popular certs), say
  so honestly: a maintained community deck (e.g. AnKing for Step) may already cover
  it, and this skill's value is the personal, lecture-specific, and weak-spot
  material those decks don't carry.
- **Ask if the notes are complete.** It's best to card a finished body of material, not a
  work in progress — otherwise you build cards you'll have to revise or discard.

### Phase 2 — Pre-flight 

Do these in order; This is NOT preference.

1. **Load carding best practices** Read `references/card-principles.md` first.
   You cannot judge what is volatile, what deserves a mnemonic, or how to deck/tag 
   until this is in context.
2. **Read the source notes end-to-end** and build a picture of the whole. No
   carding mid-paragraph. If the operator supplied an export, look through those cards
   to paint a better picture of what they're hoping to learn or understand.
   Then pass the **synthesis gate** before any verification or carding:
   - **Write the big picture back to the operator** — 3–6 sentences: what the
     material covers, its main through-line, and how the key concepts depend on
     one another. Ask the operator to confirm it matches their own understanding.
     Do not proceed to the Verifier until they confirm. If the synthesis surprises
     them, stop and resolve the mismatch first — that gap is exactly what cards
     must not be built on.
   - **Coherence check.** While writing the synthesis, judge whether the notes
     actually cohere. Trouble signals: internal contradictions, orphan fragments
     that connect to nothing, the same term used inconsistently, raw copy-paste
     with no sign of processing. If you cannot write a coherent synthesis, that
     *is* the confusing/scrambled-source case from the Guidelines — stop
     immediately and say so rather than encoding the confusion into cards.
3. **Verifier.** Read `references/volatility-and-search.md`. Identify volatile or
   checkable claims; You **MUST** verify them by a **FORCED** web search with reputable
   sources appropriate for the domain. For example, if the notes are medical, search for
   widely accepted and highly reputable medical material first. If a claim is factually 
   **wrong**, flag it and don't build a card on the disputed claim. If a claim is merely 
   **outdated/superseded but still relevant** (often the entire point in security, medicine, 
   law, and many other fields), do NOT "correct," demote, or replace it — surface it as an 
   optional enrichment alongside the current state and let the operator decide. **Never** edit their notes; 
   the operator **always** decides. 
4. **Enhancer.** Identify and *flag as candidates* (do not write yet):
   - **Image-occlusion candidates** — facts with inherent visual/spatial
     structure (anatomy, network topology, packet diagrams).
   - **Mnemonic device candidates** — facts that are hard to hold on their own
    because they have no logical hook: random numbers, enumerated/ordered
    lists (OSI layers, kill chain, cranial nerves, pathways), and hard terms, names, or
    foreign vocabulary.
      - Make suggestions on Mnemonic devices, first from popular community suggestions,
        then utilize any personal knowledge of the user if it's applicable.

    Every mnemonic must **bridge both the cue and the target** in one image — e.g. for
    FTP/21, "21 Savage uploading files over FTP" pulls back both the number and the
    protocol; "21 Savage" alone only hooks one end.
   - **Foundational concepts** — apply the importance test in `card-principles.md`,
     using both signals: the **structural** one (how many other cards in this batch
     depend on it — primary, grounded) and the **canonical** one (whether it's a
     pillar of the domain regardless of how often these notes use it — secondary,
     lower-confidence). This is an in-process signal, not a tag. It drives more
     thorough carding and a harder Phase-4 review, but never auto-carding — surface
     it for the operator to decide. Cap it: expect the top ~10–20% of concepts, not
     the majority.
5. **Observe deck and tag structure.** If the operator shared an export, read the
   existing cards/tags for the target scope — to reuse their tagging system and to
   avoid drafting cards that overlap with ones they already have (never to alter
   their cards). Read `references/tag-taxonomy.md`. Determine where cards should go
   (default: an existing deck; propose a new one only under the deck policy in the
   tag file, and confirm with the operator before creating it).

### Phase 3 — Build

Cards flagged for a mnemonic device or as strong image-occlusion candidates are surfaced
to the operator, ask them if they want to still make standard cloze-deletion cards for them
or if they will research and build them on their own.

Read `references/card-principles.md` and `references/tag-taxonomy.md`. Draft each
card as a cloze — the `Text` (cloze sentence), the `Back Extra` (the mandatory
Context anchor plus Source and Date, and an approved Mnemonic on its own labeled
line), and tags — per the rubric. Bias to high value; redundancy on *foundational*
concepts is fine.

Per-card, follow the card principles for: rephrasing in fresh words (never cloze a
sentence copied verbatim from the notes — copied sentences get memorized by shape,
not meaning), cloze atomicity (one deletion tested per card),
priming when a fact could be confused across domains (prefer priming through
wording; then a cloze hint (`{{c1::…::hint}}`); a bracketed label like
`[Networking]` is the last resort, and must never become the only thing
recalled), a grounding search for ~1 sentence of
Context when the notes lack it. Tags are read and reused from existing cards where
applicable, otherwise tags will be created.

### Phase 4 — Adversarial review

Spawn a **fresh subagent** that receives the card principles (`card-principles.md`), 
the drafted cards, the operator's source notes, and — when provided — exemplars from
their collection (existing cards, plus the `tag:leech` / `prop:ivl>=90` exports from
Phase 1) — **but not the reasoning that produced the cards.** It reads as a skeptical stranger holding the same sources the builder had. Its
mandate runs in two lanes: 
- **Grounded fidelity checks (verdicts).** Judged card-by-card against the source
  notes — the lane where agent review is demonstrably strong: is the card on-target
  for the passage it came from; is the claim actually supported by the notes; does
  the surrounding text give the answer away (if the text after the deletion rewords
  the hidden part, rewrite); does the stem carry enough context to be answerable?
  Failures here loop the card back to Phase 3.
- **Construction flags (low-confidence).** Wordiness, shallowness, training the
  question's shape instead of the content. Agent judgment is weak on exactly these
  flaws, so surface them to the operator as flags rather than silently rewriting —
  unless the fix is obvious and mechanical.

For **foundational cards and borderline calls**, judge by labeled placement rather
than bare verdict: generate deliberate degradations of the card (a wordy version, an
answer-leaking version, a two-facts version, an off-target version) and ask where the
real candidate ranks among them — placement among labeled examples is the form of
agent judging that works; unlabeled "pick the best" is not. Foundational cards
additionally get **three independent fidelity judges and a majority vote**: that is
what "the hardest look" means mechanically.

Surface every issue; loop problem cards back to Phase 3.

Once finished, present the cards to the operator for review in the readable preview
format (§9a). The actual deliverable is the **importable TSV** (§9b) — a
tab-separated file with header directives that imports straight into Anki's built-in
Cloze note type (Context/Source/Date in Back Extra). The block/preview format is
NOT importable; never hand it over as the import file.

Before handing the file over, mechanically validate it: every non-header line
has exactly three tab-separated fields, every `Text` contains at least one
well-formed `{{cN::…}}`, no raw newlines or stray tabs inside fields, no literal
`::` inside any cloze answer (Anki parses it as the hint separator —
`{{c1::std::vector}}` imports as answer "std" with hint "vector"), raw `<`, `>`,
`&` entity-escaped (`#html:true` treats every field as markup), and the header
directives match §9b exactly. Check with a quick script or line pass — a
malformed row imports silently wrong, and "silently wrong in Anki" defeats the
whole pipeline.

### Phase 5 — Final response format (mandatory)

```
# Review Summary
Created <N> cards on <one clause: what the material is about — not a section list>,
verified against <sources searched> and adversarially reviewed by an independent
agent. The review found <X>, so I did <Y>.

To Import: Anki -> File -> Import -> Select <full path to file>.

## Findings

* Errors: I found <N> errors in your notes. Your notes were left as they are.
   * <Table with one line per error: the claim, the correct fact, the source>
* Mnemonic Candidates:
   * <Table with one line per candidate: the fact and the suggested hook>
* <anything else worth noting — one line per top-level bullet>
```

Rules:
- **Brevity is the point.** Everything above Findings: four sentences maximum.
  One line per Findings sub-bullet — the operator will ask if they want more.
  The goal is a brief idea of what was created without examining everything.
- **One number, and it's cards.** Report the count of cards the operator will
  review. Never report a separate note count or explain the note/card
  distinction (a multi-deletion note yields one card per deletion number —
  count the cards).
- **Coverage is one clause** ("the mobile and networking sections of your A+
  notes"), never a section-by-section enumeration.
- Do not restate card anatomy (Context, Source, tags) and never list example
  tags — that is the skill's contract, not news.
- **Errors** is always the first Findings bullet and **Mnemonic Candidates**
  always the second — even when empty ("I found 0 errors in your notes. Your
  notes were left as they are.", no sub-bullets).
- "The review found <X>, so I did <Y>" reports the Phase-4 outcome concretely
  and in one sentence. If everything passed, say so.
- If the batch exceeds ~50 cards, add one Findings bullet advising the operator
  to let Anki's new-cards/day limit (default 20) meter them out; if the batch
  contains multi-deletion notes, the same bullet advises enabling "bury siblings"
  so sibling cards don't land on the same day. (FSRS at ~0.90 desired retention
  is the scheduler answer if they ask about settings — don't volunteer more.)
- If the operator provided no deck export or leech/mature exports, add one
  Findings bullet noting that supplying them next run gives the reviewer labeled
  examples from their own collection and sharpens the review.
- The import path names a location the operator can actually reach — never an
  internal/sandbox path.

## Critical rules

- One card tests exactly one fact — one deletion revealed per card. Multi-number
  notes (`c1`/`c2`) are fine; two facts under the same `cN` are not.
- Every card has a filled **Context** anchor (in `Back Extra`) that situates the
  fact in the bigger picture — never a restatement of the answer, never the mnemonic.
- Every card has a **Source** (in `Back Extra`). Volatile cards also carry a date
  and version inside the cloze `Text` itself, so staleness shows during review.
- Never edit or silently "correct" the operator's notes. Flag genuine errors; surface
  outdated-but-still-relevant facts as optional enrichments, never as corrections —
  "outdated" ≠ "wrong" (legacy/superseded facts are often the point). The operator
  always decides. (See `volatility-and-search.md` → "Outdated ≠ wrong".)
- Tags: lowercase, `::` for hierarchy, 1–2 per card default (4 max), reuse existing tag
  strings exactly (read them first), never invent a near-duplicate.
- Decks stay few; topical organization lives in tags.