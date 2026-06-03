# 🃏 Deckhand

> A [Claude](https://claude.com/claude-code) skill that turns your study notes into high-quality, cloze-only [Anki](https://apps.ankiweb.net/) flashcards based on industry best practices and delivered as an easily importable TSV.

## What does it do?

Point Claude at your notes and ask for Anki cards. Deckhand produces atomic, **cloze-deletion** cards, every one carrying a **Context** anchor (the bigger picture you re-read when a fact goes fuzzy), a **source**, and **tags**. It hands you a ready-to-import `.tsv` file. That's it.

## Four phase Approach

1. **Setup** — Asks you which deck the cards belong in to reuse your existing tag scheme. It may ask for an export of your current deck to help paint the full picture and reuse tag schemes, say no if you don't want it to do this and it will build a brand new deck. 
2. **Pre-flight** — Reads how to make cards, then your notes end-to-end. It then fact-checks every volatile or checkable claim with a forced web search. If something is clearly *wrong* it flags it for you instead of touching your notes; Similarly, if something's *outdated* it will surface it for you to decide what to do, but doesn't assume that it's wrong. It also flags candidates for mnemonic devices and image-occlusion to enhance recall, while identifying foundational concepts based on specific criteria that it will review more rigorously.
3. **Build** — Drafts the cards: one tested fact per card with the load-bearing word hidden, short sentences, explicit comparison cards for things that are easy to confuse.
4. **Adversarial review** — A *fresh* Claude subagent that never saw the build reasoning attacks every card as a skeptical third-party and compares it against best practices, whether surrounding context gives away the answer too easily, and whether it's training content or just pattern-matching. Cards found to be 'foundational' get the hardest look, anything that fails gets looped back to phase 3.

## Built on

Deckhand encodes established spaced-repetition practice and active recall techniques based on:

- **Piotr Wozniak** — *[20 Rules of Formulating Knowledge](https://supermemo.guru/wiki/20_rules_of_knowledge_formulation)*
- **Andy Matuschak** — *[How to Write Good Prompts](https://andymatuschak.org/prompts/)*
- **Michael Nielsen** — *[Augmenting Long-Term Memory](https://augmentingcognition.com/ltm.html)*
- **The Anki manual** — note types, cloze syntax, and text-import format
- Scheduling is left to Anki's **FSRS** algorithm — Deckhand only authors cards.

## Best practices

- **Learn it first.** You need to understand the material before making flash cards, this doesn't skip that part for you, sorry.
- **Expand your notes before carding them.** This is actually really huge, when I was testing it, I had it create cards where I had expanded on them and even added my own personal context. This skill wove that context into the card which makes it that much more personalized and memorable. When identifying information for mnemonic devices this is particularly helpful and it may suggest things from its memory. Remember, garbage going in is garbage forever.
- **Review the flags.** When Deckhand surfaces a suspect fact or a mnemonic or image occlusion candidate, weigh in. Learning is most effective when it's multi-modal.

## Installation

Deckhand is a Claude Code / Cowork skill and lives in a folder under your skills
directory, which lives at `~/.claude/skills/` (on Windows,
`%USERPROFILE%\.claude\skills\`). The skill itself ends up at
`~/.claude/skills/deckhand/`.

If you don't have a skills folder yet, it's because you haven't added any. The
`git clone` command below creates the path for you. If you're just downloading the
files, you'll have to make the `skills` folder yourself.

**Clone straight into your skills folder** — paste the command as-is, there's
nothing to fill in:

```bash
# macOS / Linux
git clone https://github.com/F1veYearPlan/deckhand.git ~/.claude/skills/deckhand
```

```powershell
# Windows (PowerShell)
git clone https://github.com/F1veYearPlan/deckhand.git "$env:USERPROFILE\.claude\skills\deckhand"
```

The result should be `…/.claude/skills/deckhand/SKILL.md`. Restart Claude Code (or reload Cowork) so it picks up the new skill. Confirm it's loaded by asking Claude to "make some Anki cards" and it should invoke Deckhand.

> Don't have git? Download the repo as a ZIP from GitHub and extract it into
> `~/.claude/skills/deckhand/`. Make sure the folder is named exactly `deckhand` and
> that `SKILL.md` sits directly inside it — not in a nested `deckhand-main/` subfolder,
> as some ZIPs unpack.

## How to Use

1. **Install the skill** (above).
2. **Give Claude your notes.** Any of:
   - **Notion** — connect the Notion integration and point Claude at the page ("card the *Networking* page under *Academia*").
   - **Paste** them as plaintext in the chat.
   - **Upload a file** — PDF, `.txt`, `.md`, `.docx`, `.pptx`, or even **photos/screenshots** of handwritten or printed notes.
   - **A local file path** (in Claude Code) or any other connected source (Google Drive, etc.).
3. **Ask for cards** — e.g. *"Make Anki flashcards from these notes."* Deckhand triggers automatically.
4. **Work through the phases with Claude** — Pick a deck, and review the facts and mnemonics it flags. The more you engage here, the better the deck.
5. **Get the `.tsv`** — Claude delivers a single tab-separated file (cloze-only, with header directives baked in for the deck name and note type).
6. **Import into Anki:**
   1. Open the **Anki desktop app**.
   2. **File → Import** and select the `.tsv`.
   3. The file's header directives preset the **deck** and the **Cloze** note type — confirm the preview looks right and click **Import**. (Anki creates the deck if it doesn't exist.)
   4. Sanity-check one card: the cloze should be on the front, and the **Context** box should render on the back.
7. **Study.** Anki (via FSRS) schedules your reviews — just show up daily. Re-importing an updated `.tsv` later *updates* matching cards instead of duplicating them.

## Requirements

- **Claude Code** or **Cowork** (the skill relies on subagents for the review pass and web search for fact-checking).
- **Anki** desktop (2.1.54+ — the version that introduced the text-import header directives).
- Optional: the **Notion** connector, if you keep your notes there.

## License

[MIT](LICENSE) 
