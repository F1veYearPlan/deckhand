# Card Principles

The rubric for designing every card. Read this during Phase 3 (Build) and
Phase 4 (Review). It encodes Wozniak's *20 Rules of Formulating Knowledge*,
Matuschak's *How to Write Good Prompts*, Nielsen's *Augmenting Long-Term Memory*,
and the Anki manual, adapted to this workflow.

## Contents

1. Minimum Information Principle
2. The four properties every card must pass + Antipatterns
3. Cloze rules
4. Direction: test production, not only recognition
5. The Context field (mandatory)
6. Note type and fields
7. Domain pitfalls
8. The Enhancer gates: image, mnemonic, importance
9. Output format

---

## 1. Minimum Information Principle

One card tests one atomic fact. The brain reinforces simple, consistent stimuli
reliably; a card that asks five things splits effort five ways and reinforces
none. Split compound cards into several atomic ones. Expect to write more cards
than feels natural — but counterbalance against overload (the documented failure
mode): atomic does not mean exhaustive. Card what is worth ~10 minutes of review
spread over years (Nielsen's test); skip the rest.

**Compound (bad):** "What is ARP, what layer of the OSI model does it operate on, and what is its main vulnerability?"
**Atomic (good):** three cards — What is ARP, the layer, the vulnerability.

## 2. The four properties every card must pass + Antipatterns

- **Focused** — asks exactly one thing.
- **Precise** — has one reasonable correct answer; a future-user can't give a
  "technically right but not wanted" answer.
- **Consistent** — lights up the same recall each time; the question doesn't drift.
- **Effortful** — requires retrieval, not pattern-matching the question's shape.

Two anti-patterns to reject:
- **Binary prompts** ("Is XSS client-side? Y/N") — too shallow. Rephrase to force
  production: "XSS executes its payload in whose browser?"
- **Pattern-shape memorization** — long, distinctive question stems get memorized
  by *shape* without engaging content. Keep stems short and generic.

## 3. Cloze rules

Default to cloze for most material. It is fast, enforces atomicity, and keeps
semantic context intact.

1. **One deletion tested per card.** On a multi-blank sentence, generate separate
   cards (`c1`, `c2`, `c3`) so the other blanks remain as scaffolding. Never blank
   everything at once.
2. **Hide the load-bearing word**, not a filler.
3. **Keep the sentence short** (~15–25 words). A three-line cloze is a paragraph,
   not a fact — strongly consider splitting it.
4. **Overlapping clozes for sequences.** For ordered processes (handshakes, kill
   chain, metabolic pathways), make each step its own cloze on one sentence so the
   sequence's shape becomes part of the memory, without ever recalling all steps
   at once.

Example (produces three cards, each blanking one step):
```
The TCP three-way handshake proceeds: client sends {{c1::SYN}},
server replies with {{c2::SYN-ACK}}, client responds with {{c3::ACK}}.
```

## 4. Direction: test production, not only recognition

Recognition ("does this term mean X?") is weaker than production ("what term means
X?"). For terms the user must actively produce, **cloze the term itself** so the
card forces recall of the term, not the definition around it. Don't reflexively
test every direction — that doubles review load — but ensure the *direction that
matters* is the one the deletion sits on.

## 5. The Context field (mandatory)

Every card carries a Context field on the back, distinct from the answer. It is
the memory-anchoring scaffold: it re-situates the atomic fact in the bigger
picture the user understands, and gives them something to re-read when the fact
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
  - the **Source**, and **Date Added** (YYYY-MM-DD),
  - an approved **Mnemonic**, if any, on its own labeled line (never merged into
    the Context wording).

  Render these as HTML (e.g. `<b>Context:</b> … <br><span ...>Source · date</span>`)
  so they read cleanly on the card back (see §9).

If the user already has their own cloze-capable note type with real
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
   use overlapping clozes (§3.5), never one mega-card listing all items.
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
cloze-TSV delivery (§9) — so *flag* these candidates for the user to build
separately rather than emitting them in the TSV.

### Mnemonic (apply rarely and deliberately)
Gate: the fact is **arbitrary** — no logical hook to understanding (port numbers,
RFC numbers, dates, key sizes). Do **not** mnemonicize concepts that should be
understood; a mnemonic there short-circuits the understanding the card exists to
preserve. The primary literature says you'll consciously need mnemonics for only
~1–5% of items; popular guides tend to over-recommend them, so stay conservative.

Rules when a mnemonic is warranted:
- Put it on its own labeled `Mnemonic:` line in Back Extra, **never** woven into
  the Context wording.
- It is a **candidate for the user's approval**, not an automatic addition.
- It must **bridge both the cue and the target.** "21 Savage" anchors the number
  21 but not *FTP*; a one-sided hook fails. Encode both sides.
- Keep them **rare.** The bizarreness effect works by distinctiveness; if every
  card is weird, none stands out and the effect cancels.
- The strongest mnemonics come later, from cards that *prove* sticky in review
  (leeches). Creation-time guessing is weaker — prefer flagging over forcing.

### Importance (foundational test) — in-process signal, not a tag
A concept is **foundational** if other facts in the material *depend on it* — if
misunderstanding it would make downstream cards unanswerable or cause cascading
errors. Measure by prerequisite-load: how many other cards in this batch (or the
existing deck) presuppose it? Ask for an export of the deck or for the operators
input if one is suspected but evidence is not currently supporting it. 

This is a domain-independent test (it works for cardiology and networking alike,
because it's about position in the knowledge graph, not content). Apply it in
Phase 2; carry the flag in-process into Build and Review.

- **Cap it.** Flag a concept foundational only if ~2–3+ other cards depend on it.
  Expect the top ~10–20% of concepts, not the majority. If most things look
  foundational, the signal is broken, re-apply more strictly.
- **What it drives:** foundational concepts get *more* cloze formulations
  (redundancy is justified for load-bearing basics) and a *harder* look in the
  Phase-4 adversarial review. They do not get a mnemonic on account of importance.
- **Keep it in-process** by default. Only promote to a `priority::` tag if the
  user explicitly wants to filter foundational cards for extra review in Anki — a
  persistent importance tag otherwise attracts inflation into the tag tree.

## 9. Output format

There are two representations. Don't conflate them:

### 9a. Review preview (human-readable; NOT importable)
For the Phase-4 review and when presenting to the user, show each card in this
readable block, one per `---`. This is for reading only — Anki cannot import it.

```
TEXT: Telnet listens on TCP port {{c1::23}}.
CONTEXT: Older cleartext remote-management protocol; on Meow box this was the only open port. Don't confuse with SSH (22).
SOURCE: Notion → CPTC → Meow Box
DATE: 2026-06-02
TAGS: topic::offsec::protocols::telnet source::notion::cptc type::number
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
Telnet listens on TCP port {{c1::23}}. → <b>Context:</b> Older cleartext remote-management protocol; on Meow box this was the only open port. Don't confuse with SSH (22). <br><span style="font-size:12px;color:#888">Notion → CPTC → Meow Box · 2026-06-02</span> → topic::offsec::protocols::telnet source::notion::cptc type::number
```

Rules:
- Keep each card on **one physical line** (no raw newlines inside a field; use
  `<br>`). `#html:true` makes the markup render.
- Re-importing the same file **updates** matching notes (Anki matches on the first
  field, `Text`) instead of duplicating — so fix-and-reimport is safe.
- Volatile cards: keep the date/version inside the `Text` itself, not only in
  Back Extra, so staleness is visible during review.
- Confirm the `#deck:` name is what you intend (create the deck first if needed).
