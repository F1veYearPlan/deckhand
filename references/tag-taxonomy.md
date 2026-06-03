# Tag Taxonomy and Deck Policy

Read this during Phase 3 (tagging) and Phase 2 (when observing existing
structure). Tags do topical organization; decks do scheduling. This taxonomy
generalizes across domains (medicine, networking, security, etc.) by holding the
**axes** fixed and varying the **sub-trees** per field.

## The single most important rule for an automated tagger

**Read the operator's existing tag list first, and reuse exact strings.** Inconsistent
naming silently forks the tree: "cardio", "Cardiology", "cardiac", and "heart"
become four separate tags. Tags are case-insensitive in search but case-sensitive
in display. Before inventing any tag, check whether an equivalent already exists
and reuse it verbatim. Map onto the existing hierarchy; do not mint a fresh
taxonomy each run.

## The axes

```
topic::<area>::<sub-area>     # what the card is about (varies by domain)
source::<origin>             # where the knowledge came from (exactly one)
type::<knowledge-kind>       # what kind of knowledge it is (one or more)
context::<scoping>           # situational scope, e.g. exam/cert/rotation (optional)
```

Optional `status::` exists, but prefer Anki's built-in **flags** for transient
states (needs-review, leech) to keep the tag tree clean.

## Sub-trees vary by domain (same axes)

```
# Medicine
topic::cardiology::arrhythmias        source::first-aid::ch12     type::concept     context::exam::step1
topic::pharmacology::beta-blockers    source::sketchy::pharm      type::comparison  context::rotation::im

# Networking
topic::networking::tcp-ip             source::notion::tcp-notes   type::procedure
topic::networking::dns                source::rfc-1035            type::number

# Security
topic::offsec::web::xss               source::notion::web-notes   type::gotcha      context::cert::oscp
```

`type::` vocabulary (reuse, don't expand casually): `definition`, `concept`,
`procedure`, `command`, `number`, `comparison`, `gotcha`, `volatile`.

## Hygiene rules

1. **Lowercase, kebab-case.** `topic::offsec::active-directory`, never mixed case.
2. **`::` for hierarchy.** Never spaces or slashes. Anki auto-creates parent levels.
3. **2–4 tags per card, maximum.** Eight tags is noise, not organization. Be
   aggressive about *covering the axes*, disciplined about *count*. Trim
   `context::` when it isn't load-bearing.
4. **Never a tag where a field/flag is better.** Difficulty → flag. Review date →
   Anki's scheduling / date metadata. Importance → in-process signal (see
   card-principles §8).
5. **A tag must group.** If only one card would carry it, it's a note, not a tag.
6. **Tag at creation,** never as a later pass — retro-tagging thousands of cards
   is far harder.
7. **Prune with the source.** When the operator revises a source note, find its cards
   via the `source::` tag and update them together.

## Deck policy: keep decks few

Default to one main deck per domain (a given learner is usually one domain), with
at most one or two sub-decks. Topical splitting belongs in tags, not decks —
fragmenting into a deck-per-topic hides cards and adds no pedagogical value.

A small deck count also produces **interleaving** (mixing topics within a review
session), which has empirical support for retention — so the conservative deck
strategy is a learning benefit, not just tidiness.

**Default to the existing deck.** Propose a *new* deck only when one of these
holds, and confirm with the operator before creating it:
- the material needs a genuinely different review schedule (e.g., cram for a dated
  exam, then suspend), or
- it has a clear endpoint after which it will be retired, or
- it's a fundamentally different domain the operator doesn't want mixed into daily
  review.

A new topic, a new source, a different difficulty, or a different cert is **not** a
reason for a new deck — it's a tag (or, for an actively-grinded cert, at most a
sub-deck).
