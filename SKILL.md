---
name: deckhand
description: >-
  Turn a user's notes or study material into high-quality Anki cloze-deletion flashcards built for long-term recall, applying spaced-repetition best practices (atomic facts, mandatory context anchor, interference-busting) and an adversarial review pass. Use whenever the user wants to make flashcards, a deck, cloze deletions, or spaced-repetition/active-recall study material from notes, lectures, documentation, or mentions Anki, cloze, "carding," spaced repetition, or memorizing a body of material. Trigger even on a bare "make cards from this." Full methodology loads with the skill.
---

# Anki Flash Cards

Turn the operator's notes into atomic, recall-optimized Anki cards. 

## Read this first: what this skill is and is not

Anki utilizes spaced-repetition learning to prevent forgetting of understanding the operator already has, it does not create understanding.

- **Bias toward fewer, higher-value cards.** A bad card is worse than no card: It costs
review time and builds false confidence. Create cards that cover the material while
placing emphasis on foundational knowledge.
- **Encode only what is understood.** Cards built on confusion encode confusion.
If the operator does not yet understand the material, stop and say so (Phase 1).

If at any point the source material is genuinely confusing or scrambled, 
surface the gap to the operator instead of papering over it with cards.

## Environment

This skill relies on capabilities that exist in **Claude Code or Cowork**:
- **Subagents** for the Phase 4 adversarial review (a fresh reviewer with no
  build context). If subagents are unavailable, do the review inline as a
  best-effort sanity check and tell the operator it is less independent.
- **Web search** for Phase 2 verification and Phase 3 grounding. If unavailable,
  flag every volatile claim as unverified rather than guessing.
- **No other dependencies.** Delivery is a plain TSV (§9b) that imports natively —
  no Python, genanki, or `.apkg` tooling required.

## Reference files — read the right one at the right phase

| File | Read it during | Covers |
|---|---|---|
| `references/card-principles.md` | Phase 3 (Build) **and** Phase 4 (Review) | How to design every card: atomicity, the four properties, cloze rules, the Context anchor, the cloze note type, domain pitfalls, the mnemonic/image/importance gates, and the importable TSV output format |
| `references/volatility-and-search.md` | Phase 2 (Pre-flight / Verifier) | What counts as volatile, the forced-search rule, how to flag without editing, the two distinct search purposes, dating and sourcing |
| `references/tag-taxonomy.md` | Phase 3 (Build, tagging step) | The cross-domain tag axes, hygiene rules, the read-existing-tags-first requirement, deck-vs-tag policy |

## Workflow

### Phase 1 — Setup

Before anything else, confirm two things with the operator:
- **The notes are complete.** It's best to card a finished body of material, not a
  work in progress — otherwise you build cards you'll have to revise or discard.
- **They've actually learned it.** Spaced repetition reinforces existing
  understanding; it does not create it. You can verify the claims as written, but
  you cannot verify the operator's mental model — say so plainly.

If the notes are unfinished or the operator hasn't learned the material yet, advise
them to handle that first; building on a shaky foundation cheapens every card. Do
not proceed until they confirm.

Then ask whether they have a current deck the cards should join. If yes, ask them to
export it — **not so we change anything in it (we never touch their existing cards),**
but so we can read it to build a fuller picture of what they're learning, reuse their
tagging system, and avoid drafting cards that overlap with ones they already have. If
they'd rather not, or seem unsure, a new deck is fine.

### Phase 2 — Pre-flight (gather context, build the big picture, and flag information; touch nothing yet)

Do these in order; the order is a dependency, NOT a preference.

1. **Load carding best practices** Read `references/card-principles.md` first.
   You cannot judge what is volatile, what deserves a mnemonic, or how to deck/tag 
   until this is in context.
2. **Read the source notes end-to-end** and build a picture of the whole. No
   carding mid-paragraph. If the operator supplied an export, look through those cards
   to paint a better picture of what they're hoping to learn or understand.
3. **Verifier.** Read `references/volatility-and-search.md`. Identify volatile or
   checkable claims; You **MUST** verify them by a **FORCED** web search with reputable
   sources appropriate for the domain. For example, if the notes are medical, search for
   widely accepted and highly reputable medical material first. If a claim is factually 
   **wrong**, flag it and don't build a card on the disputed claim. If a claim is merely 
   **outdated/superseded but still relevant** (often the entire point in security, medicine, 
   law, and many other fields), do NOT "correct," demote, or replace it — surface it as an 
   optional enrichment alongside the current state and let the operator decide. **Never** edit their notes; 
   the operator **always** decides. (See the reference's "Outdated ≠ wrong" section.)
4. **Enhancer.** Identify and *flag as candidates* (do not write yet):
   - **Image-occlusion candidates** — facts with inherent visual/spatial
     structure (anatomy, network topology, packet diagrams).
   - **Mnemonic device candidates** — flag facts that are hard to hold on their own
    because they have no logical hook: random numbers, enumerated/ordered
    lists (OSI layers, kill chain, cranial nerves, pathways), and hard terms, names, or
    foreign vocabulary. Match the device to the fact type:
      - Numbers/ports → an absurd image, or the Major System for many numbers.
      - Ordered lists → a first-letter acronym/acrostic, or method of loci for long ones.
      - Hard terms/names → the keyword method (similar-sounding word + vivid image).

    Every mnemonic must **bridge both the cue and the target** in one image — e.g. for
    FTP/21, "21 Savage uploading files over FTP" pulls back both the number and the
    protocol; "21 Savage" alone only hooks one end.
    **Personalize, don't impose:** mnemonics stick best when built from the operator's own
    associations. When surfacing a candidate, advise them of this and ask them to supply
    the personal hook or suggest one from their memory rather than handing over a generic one. 
    Offer to web-search for an established/canonical mnemonic as a starting point (many exist for OSI, cranial
    nerves, etc.), noting a personalized variant usually beats the canonical one.
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

Per-card, follow the card principles for: cloze atomicity (one deletion tested per card),
priming when a fact could be confused across domains (prefer priming through
wording; a bracketed label like `[Networking]` is a backup, and must never
become the only thing recalled), a grounding search for ~1 sentence of
Context when the notes lack it. Tags are read and reused from existing cards where
applicable, otherwise tags will be created.

### Phase 4 — Adversarial review

Spawn a **fresh subagent** that receives the card principles (`card-principles.md`), 
the drafted cards — **but not the reasoning that produced them.** It reads as a skeptical stranger. 
It checks each card against best practices and against *what the card actually trains*
versus what it was meant to train (e.g., is it training the content, or just
pattern-matching the question's shape?). The subagent will also check to ensure that
the cards answer is not given away freely by the surrounding context, I.E. if the text after the
cloze deletion rewords the hidden part, that card should be rewritten. Foundational cards get the hardest look.
Surface every issue; loop problem cards back to Phase 3.

Once finished, present the cards to the operator for review in the readable preview
format (§9a). The actual deliverable is the **importable TSV** (§9b) — a
tab-separated file with header directives that imports straight into Anki's built-in
Cloze note type (Context/Source/Date in Back Extra). The block/preview format is
NOT importable; never hand it over as the import file.

### Phase 5 — Final response format (mandatory)

The final message to the operator MUST follow this exact structure. Fill the
`<placeholders>`; do not reorder, rename, or drop sections.

```
# Review Summary
Created <number of> cloze cards covering <what was covered>, all verified against
<source material / sources searched>, adversarially reviewed by an independent agent
for <what the review checked, e.g. atomicity, context leakage, pattern-matching>.

The independent review found <X>, so I did <Y>.

Each card carries a context and source field in Back Extra, and tags like
<2–3 real examples of tags used>.

To Import: Anki -> File -> Import -> Select the file from <path to file>

## Findings

* Errors: I found <X> errors in your notes. Your notes were left as they are.
   * <Error, the correct information, and its source>
   * <...one bullet per error>
* Mnemonic Candidates:
   * <Candidate, why it's a good candidate, and any suggestion or community example>
   * <...one bullet per candidate>
* <Anything else worth noting, as additional top-level bullets>
```

Rules:
- **Errors** is always the first Findings bullet and **Mnemonic Candidates** always
  the second — even when empty. With zero errors, still write "I found 0 errors in
  your notes. Your notes were left as they are." with no sub-bullets. With zero
  mnemonic candidates, state that under the Mnemonic Candidates bullet.
- "The independent review found X, so I did Y" reports the Phase-4 outcome
  concretely (e.g. "found 2 cards whose surrounding text gave away the answer, so I
  rewrote them"). If the review passed everything, say so.
- The import path in "To Import" names the actual saved file location the operator
  can reach — never an internal/sandbox path.

## Critical rules

- One card tests exactly one fact. One cloze deletion per card.
- Every card has a filled **Context** anchor (in `Back Extra`) that situates the
  fact in the bigger picture — never a restatement of the answer, never the mnemonic.
- Every card has a **Source** (in `Back Extra`). Volatile cards also carry a date
  and version inside the cloze `Text` itself, so staleness shows during review.
- Never edit or silently "correct" the operator's notes. Flag genuine errors; surface
  outdated-but-still-relevant facts as optional enrichments, never as corrections —
  "outdated" ≠ "wrong" (legacy/superseded facts are often the point). The operator
  always decides. (See `volatility-and-search.md` → "Outdated ≠ wrong".)
- Tags: lowercase, `::` for hierarchy, 2–4 per card max, reuse existing tag
  strings exactly (read them first), never invent a near-duplicate.
- Decks stay few; topical organization lives in tags.
