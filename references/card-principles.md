# Card Principles

The rubric for designing every card. Read during Phase 3 (Build) and
Phase 4 (Review). 

### Source Material
* *Wozniak's *20 Rules of Formulating Knowledge*,
* Matuschak's *How to Write Good Prompts*, 
* Nielsen's *Augmenting Long-Term Memory*,
and the Anki manual, adapted to this workflow.

## Contents

1. Cloze rules
2. Minimum Information Principle
3. The five properties every card must pass + Antipatterns
4. Direction: test production, not only recognition
5. The Context anchor (mandatory)
6. Note type and fields
7. Domain pitfalls
8. The Enhancer gates: image, mnemonic, importance
9. Output format

---

## 1. Cloze rules

All material should be Cloze deletion. 

Cloze cards hide key information in context, helping you recall details naturally. For example:
"The [...] pumps blood throughout the body" tests your knowledge of "heart."

### Best Practices
1. Hide **key concepts** or **relationships**
2. Write clear, focused prompts with enough context
3. **Rephrase in fresh words.** Never cloze a sentence copied verbatim from the
   notes — copied sentences get memorized by their shape, not their meaning (the
   cloze format's single biggest risk). Rebuild the fact as a short sentence of
   your own, then delete.
4. A note may carry 2–3 deletions max (`c1`, `c2`, … — each number becomes its
   own card testing one blank); tend toward one. Never put two facts under the
   same `cN` number.
5. Keep the sentence short (~15–25 words). A three-line cloze is a paragraph,
   not a fact — strongly consider splitting it.
6. Use overlapping cards to connect related ideas. 
7. Use Anki's hint syntax `{{c1::answer::hint}}` to disambiguate — the hint
   shows inside the blank before answering (e.g. `{{c1::445::port}}`, or
   `{{c1::mmWave::5G flavor}}` when several cards share a similar stem).
   Prefer a hint over contorting the sentence or a bracketed `[Domain]` label.
   Keep hints lean — a hint that narrows the field to one option is the answer.

### Mistakes to Avoid
1. Over-deletion: Don't hide too much at once. 
2. Stripping context: Keep enough clues to understand what the question is asking without giving the answer away. 
3. Testing recognition, not recall: Avoid passive phrasing.
4. Overloading cards: Focus on one idea per card.

## 2. Minimum Information Principle

One card tests one atomic fact. The brain reinforces simple, consistent stimuli
reliably; a card that asks five things splits effort five ways and reinforces
none of them. Split compound cards into several atomic ones.

Atomic does not mean exhaustive. Nielsen's threshold: card a fact if knowing it
is worth ~10 minutes of your future time — the measured *cost* of a card is only
4–7 minutes of review spread over 20 years. Skip the rest.

**Compound (bad):** one deletion carrying three facts —
`ARP is {{c1::the layer-2 protocol that maps IP addresses to MAC addresses and is vulnerable to cache poisoning}}.`
**Atomic (good):** three cards —
- `{{c1::ARP}} maps IP addresses to MAC addresses.`
- `ARP operates at OSI layer {{c1::2}}.`
- `ARP's main vulnerability is {{c1::cache poisoning}}.`

## 3. The five properties every card must pass + Antipatterns

- **Focused** — asks exactly one thing.
- **Precise** — has one reasonable correct answer; a future reviewer can't give a
  "technically right but not wanted" answer.
- **Consistent** — lights up the same recall each time; the question doesn't drift.
- **Tractable** — can almost always be answered correctly; if a card keeps
  failing, break it down or add a cue rather than tolerate the lapses. In Anki
  this surfaces later as a **leech** (8 lapses → auto-tagged and suspended):
  treat a leech as proof the card is bad — rewrite or split it, don't grind it.
- **Effortful** — requires retrieval, not pattern-matching the question's shape.

Two anti-patterns to reject:
- **Binary prompts** ("Is XSS client-side? Y/N") — too shallow. Rephrase to force
  production: `XSS executes its payload in {{c1::the victim's}} browser.`
- **Pattern-shape memorization** — long, distinctive question stems get memorized
  by *shape* without engaging content. Keep stems short and generic.

## 4. Direction: test production, not only recognition

Recognition ("does this term mean X?") is weaker than production ("what term means
X?"). For terms the operator must actively produce, **cloze the term itself** so the
card forces recall of the term, not the definition around it. Don't reflexively
test every direction — that doubles review load — but ensure the *direction that
matters* is the one the deletion sits on.

## 5. The Context anchor (mandatory)

Every card carries a Context anchor on the back, distinct from the answer. It is
the memory-anchoring scaffold: it re-situates the atomic fact in the bigger
picture the operator understands, and gives them something to re-read when the fact
has gone fuzzy but not yet forgotten (Wozniak's redundancy / reasoning-clue
principle — extra retrieval paths if the primary one fails).

Context is **read after answering, not recalled.** It is not tested.

Include, where applicable: why the fact matters / what problem it solves; where it
fits in the bigger picture (which layer, phase, system); a concrete example; An
interference warning against a confusable neighbor; a personal anchor.

**Context is NOT:**
- a restatement of the answer, or
- the mnemonic hook. Absurd memory hooks corrupt the anchoring job of this field;
  an approved mnemonic goes on its own labeled line in Back Extra, kept separate
  from the Context wording (§6, §9).

## 6. Note type and fields

Every card is a **cloze deletion** — one note type only. Use Anki's built-in
**Cloze** type, which has two fields: `Text` and `Back Extra`. No Basic/Q-A
cards; anything you'd phrase as question→answer becomes a cloze instead (§4,
§7.5). Do not create a note type per topic; topical organization is done with tags.

- **`Text`** — the cloze sentence (`{{c1::...}}`).
- **`Back Extra`** — everything shown under the answer, packed into this one
  field because the built-in Cloze type has no dedicated Context/Source fields:
  - the mandatory **Context** anchor (§5),
  - the **Source**, and a **Date** (YYYY-MM-DD),
  - an approved **Mnemonic**, if any, on its own labeled line (never merged into
    the Context wording).

  Render these as HTML (e.g. `<b>Context:</b> … <br><span ...>Source · date</span>`)
  so they read cleanly on the card back (see §9).

If the operator already has their own cloze-capable note type with real
Context/Source fields, map to those instead of folding into Back Extra — keep to
their structure.

## 7. Domain pitfalls

These recur across technical and medical material. Watch for them:

1. **Acronym overload.** Don't stop at "SAML = Security Assertion Markup Language"
   (that's syntax). Also card what problem it solves, what it's compared against,
   the actors, a concrete flow — across several cards.
2. **Number/port sets.** Never card "what ports does SMB use? → 137,138,139,445"
   (a set; sets resist memory). One card per (thing, value) pair, each grounded:
   `SMB direct over TCP uses port {{c1::445}}.`
3. **Vendor/product interference.** Splunk vs Elastic vs QRadar; two drugs in a
   class. Card the *differences* explicitly with disambiguating Context; don't
   card each in isolation and hope the brain sorts them out — it won't.
4. **Closely related concepts.** XSS vs CSRF; preload vs afterload; ARP vs DNS
   spoofing. Make explicit comparison cards that state the key distinction.
5. **Command/procedure soup.** Card intent → flag/step, not the whole command in
   one card — as a cloze: `The nmap flag for OS detection is {{c1::-O}}.`
6. **Ordered enumerations.** OSI layers, kill chain, ATT&CK tactics, pathways —
   use overlapping clozes (§1.5), never one mega-card listing all items.
7. **"It depends" answers.** If the answer depends on context, put the context in
   the question. An unanswerable card erodes confidence.

## 8. The Enhancer gates: image, mnemonic, importance

These three are *flagged as candidates* in Phase 2 and acted on here. Each is
gated; over-applying any of them is itself a failure.

### Image occlusion
Gate: the fact has inherent **visual/spatial** structure — anatomy, network
topology, packet/header layouts, diagrams (blank part of a picture; Wozniak's
graphic deletion). Don't force images onto purely verbal facts. Image occlusion
uses Anki's dedicated **Image Occlusion** note type, which is outside this skill's
cloze-TSV delivery (§9) — so *flag* these candidates for the operator to build
separately rather than emitting them in the TSV. More broadly: text import never
transports media at all (image files must be pre-placed in `collection.media`),
so image-rich card backs — however valuable — are outside this pipeline; flag,
don't fake them.

### Mnemonic (apply rarely and deliberately)
Gate: the fact is **arbitrary** — no logical hook to understanding (port numbers,
RFC numbers, dates, key sizes). Do **not** mnemonicize concepts that should be
understood; a mnemonic there short-circuits the understanding the card exists to
preserve. The primary literature says you'll consciously need mnemonics for only
~1–5% of items; popular guides tend to over-recommend them, so stay conservative.

Rules when a mnemonic is warranted:
- Put it on its own labeled `Mnemonic:` line in Back Extra, **never** woven into
  the Context wording.
- It is a **candidate for the operator's approval**, not an automatic addition.
- It must **bridge both the cue and the target.** "21 Savage" anchors the number
  21 but not *FTP*; a one-sided hook fails. Encode both sides.
- Keep them **rare.** The bizarreness effect works by distinctiveness; if every
  card is weird, none stands out and the effect cancels.
- The strongest mnemonics come later, from cards that *prove* sticky in review
  (leeches). Creation-time guessing is weaker — prefer flagging over forcing.

### Importance (foundational test) — in-process signal, not a tag
A concept is **foundational** if misunderstanding it would make downstream cards
unanswerable or cause cascading errors. Two signals identify it; use both, and keep
the first primary:

1. **Structural (primary, higher-confidence).** How many other facts in *this batch*
   (or the operator's existing deck) depend on it? This is grounded entirely in the
   operator's own material. To make it concrete and auditable, sketch an explicit
   dependency list — for each candidate concept, note which other cards presuppose
   it — and rank by that in-degree. No tooling required: the batch is small enough
   to reason over directly, and the hard part (judging what depends on what) is work
   only you can do anyway.
2. **Canonical (secondary, lower-confidence).** Is the concept a pillar of the domain
   in its own right — load-bearing across the whole field regardless of how often
   *these particular notes* lean on it (the TCP three-way handshake, the cardiac
   cycle, public-key crypto)? The structural signal alone misses these when the notes
   happen to be thin around a concept. This axis draws on world knowledge, so it is
   the one most prone to hallucination and inflation — treat it as a flag to raise
   for the operator, not a verdict.

Combine them: flag foundational if structural in-degree is high **OR** the concept is
canonically central. Keep structural primary; let canonical catch what the local
graph underweights.

This is a domain-independent test (it works for cardiology and networking alike,
because it's about position in the knowledge graph, not content). Apply it in
Phase 2; carry the flag in-process into Build and Review.

- **Cap it — hard.** Flag a concept foundational only if ~2–3+ other cards depend on
  it, or it is unambiguously a domain pillar. Expect the top ~10–20% of concepts, not
  the majority. If most things look foundational, the signal is broken — re-apply more
  strictly. This cap applies *especially* to the canonical axis, where intro material
  makes everything look like a pillar.
- **Inform, don't override.** Foundational ≠ auto-card — but for the right reasons.
  The human gate exists because the operator owns the scope of what's worth their
  review time, and because the canonical signal is lower-confidence and needs
  confirmation against inflation. It does **not** exist because "the operator already
  knows it": knowing a concept today is the *prerequisite* for carding it (Phase 1),
  not an argument against — the entire job of spaced repetition is to keep
  understanding the operator already has from decaying. A foundational concept they
  currently understand is the highest-value card there is. So importance raises a
  concept for *consideration and a harder look*; when one seems foundational but the
  batch doesn't clearly support it, ask the operator — or for a deck export — and let
  them decide.
- **What it drives:** foundational concepts get *more* cloze formulations
  (redundancy is justified for load-bearing basics) and a *harder* look in the
  Phase-4 adversarial review. They do not get a mnemonic on account of importance.
- **Keep it in-process** by default. Only promote to a `priority::` tag if the
  operator explicitly wants to filter foundational cards for extra review in Anki — a
  persistent importance tag otherwise attracts inflation into the tag tree.

## 9. Output format

There are two representations. Don't conflate them:

### 9a. Review preview (human-readable; NOT importable)
For the Phase-4 review and when presenting to the operator, show each card in this
readable block, one per `---`. This is for reading only — Anki cannot import it.

```
TEXT: Telnet listens on TCP port {{c1::23}}.
CONTEXT: Older cleartext remote-management protocol; on Meow box this was the only open port. Don't confuse with SSH (22).
SOURCE: Notion → CPTC → Meow Box
DATE: 2026-06-02
TAGS: topic::offsec::protocols::telnet type::gotcha
---
```

### 9b. Delivery — a TSV that imports directly (the actual deliverable)
Anki imports **delimited rows**, one note per line. Emit a `.txt`/`.tsv` file that
begins with header directives, then one tab-separated row per card. This imports
into the built-in **Cloze** type with no setup (verified against the Anki manual,
docs.ankiweb.net/importing/text-files.html; headers supported since Anki 2.1.54):

```
#separator:tab
#html:true
#notetype:Cloze
#deck:<Deck Name>
#columns:Text	Back Extra	tags
#tags column:3
```

Then each card is ONE physical line of three tab-separated columns:

1. **Text** — the cloze sentence.
2. **Back Extra** — Context + Source + Date (+ optional Mnemonic) as HTML.
3. **tags** — space-separated, `::` for hierarchy.

Worked example (the 9a preview card → its TSV row; → marks a tab):

```
Telnet listens on TCP port {{c1::23}}. → <b>Context:</b> Older cleartext remote-management protocol; on Meow box this was the only open port. Don't confuse with SSH (22). <br><span style="font-size:12px;color:#888">Notion → CPTC → Meow Box · 2026-06-02</span> → topic::offsec::protocols::telnet type::gotcha
```

Rules:
- Keep each card on **one physical line** (no raw newlines inside a field; use
  `<br>`). `#html:true` makes the markup render.
- `#html:true` also means every field is parsed as markup: entity-escape literal
  `<`, `>`, `&` as `&lt;`, `&gt;`, `&amp;`.
- Never leave a literal `::` inside a cloze answer — Anki reads it as the hint
  separator (`{{c1::std::vector}}` imports as answer "std", hint "vector").
  Reword, or make the hint deliberate.
- Re-import updates are **keyed to the `Text` field**: Anki matches notes on the
  HTML-stripped first field, within the same note type ("Update" is the usual
  resolution, but Anki remembers whatever was last chosen in the import dialog).
  Re-importing therefore safely fixes **Back Extra and tags only** — an edit to
  `Text` breaks the match and imports a **new note** alongside the old one. For
  `Text` fixes, tell the operator to delete the old note in the browser first.
- Volatile cards: keep the date/version inside the `Text` itself, not only in
  Back Extra, so staleness is visible during review.
- Confirm the `#deck:` name is what you intend — Anki auto-creates it on import
  if it doesn't exist.
- Localization caveat: `#notetype:Cloze` and the `Text`/`Back Extra` names match
  only on **English-created collections** (stock note-type and field names are
  localized at collection creation). On a non-English Anki the headers silently
  don't resolve; the operator selects the note type manually in the import dialog.
