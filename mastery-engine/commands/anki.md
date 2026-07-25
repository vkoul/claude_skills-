# Anki — Generate Exportable Anki Cards by Topic

Generate high-quality Anki flashcards organized by course topics, and export them as a single `.apkg` file (or `.txt` tab-separated file) with proper deck hierarchy that imports directly into Anki.

## Model Instructions

This skill works with ANY Claude model (Haiku, Sonnet, Opus). Follow this sequence:
1. Read `COURSE.md` for the concept list and tiers.
2. Determine mode: no argument = full/choose, argument with concept name = single concept, "mistakes" = mistakes mode.
3. For each concept, generate 5-20 cards using ONLY these 10 types: definition, why, equation, assumption, comparison, edge_case, application, visual, connection, wrong_intuition.
4. Format each card as one line: `Front\tBack\tTags\tDeck` — no exceptions.
5. The file must start with exactly these 3 header lines: `#separator:tab`, `#html:true`, `#deck column:4`.
6. NEVER generate cards about trivia (dates, names of inventors). NEVER generate cards requiring lists longer than 3 items.
7. Every card must be answerable in under 30 seconds. If not — split it into smaller cards.

## Behavior

1. Read `COURSE.md` for concept list, tiers, and dependencies.
2. Read available course materials (slides, notes, extracted text) for content.
3. Read `progress.json` to know what's already been studied.
4. Generate cards organized by topic hierarchy.
5. Export as a single importable file.

## Card Generation Principles

### What makes a good Anki card:
- **Atomic**: One fact, one card. Never "list 5 properties of X."
- **Contextualized**: Not isolated definitions — show WHY this matters.
- **Bidirectional where useful**: "What does X mean?" AND "What concept is described by [definition]?"
- **Application-focused**: Not just "what is X" but "when would you use X" and "what goes wrong if you misuse X"
- **Layered**: Basic recall → understanding → application → edge cases

### Card types to generate per concept:

1. **Definition cards** — What is [concept]? (with the Layer 1 intuitive answer, not textbook jargon)
2. **Why cards** — Why does [concept] exist? What problem does it solve?
3. **Equation cards** — What is the formula for [X]? What does each term mean?
4. **Assumption cards** — What assumptions does [method] require? What breaks if violated?
5. **Comparison cards** — How does [X] differ from [Y]? When use one vs the other?
6. **Edge case cards** — When does [method] fail? What's the common mistake?
7. **Application cards** — Given [scenario], which method applies and why?
8. **Visual cards** — Describe/sketch the shape of [distribution/graph/relationship]
9. **Connection cards** — How does [concept A] relate to [concept B]?
10. **Wrong intuition cards** — What's the common misconception about [X] and why is it wrong?

### Cards to NEVER generate:
- Trivia (who invented it, what year)
- Verbatim textbook definitions (rephrase in plain language)
- Cards that require listing more than 3 items from memory
- Cards where the answer is ambiguous or debatable

## Deck Hierarchy

Structure mirrors `COURSE.md` tiers:

```
CourseName
├── CourseName::Tier1_Foundations
│   ├── CourseName::Tier1_Foundations::ConceptA
│   └── CourseName::Tier1_Foundations::ConceptB
├── CourseName::Tier2_Core
│   ├── CourseName::Tier2_Core::ConceptC
│   └── CourseName::Tier2_Core::ConceptD
└── CourseName::Tier3_Advanced
    ├── CourseName::Tier3_Advanced::ConceptE
    └── CourseName::Tier3_Advanced::ConceptF
```

Use `::` as the separator (Anki's subdeck notation).

## Export Format

Generate a **tab-separated text file** (`anki_export.txt`) that Anki can import directly:

```
Front\tBack\tTags
What is the fundamental assumption of DID?\tParallel trends: absent treatment, the treatment and control groups would have followed the same trajectory over time.\ttier2 did assumptions core
```

### File structure:
- Line 1: `#separator:tab`
- Line 2: `#html:true`
- Line 3: `#deck column:4`
- Remaining lines: `Front\tBack\tTags\tDeck`

### Formatting in cards:
- Use HTML for formatting (Anki supports it): `<b>`, `<i>`, `<br>`, `<ul><li>`
- Math: use Anki's MathJax notation: `\(inline\)` and `\[display\]`
- Code: `<code>...</code>`
- Cloze deletions where appropriate: `{{c1::answer}}` (use Note Type: Cloze)

### Tags per card:
- Tier: `tier1`, `tier2`, `tier3`
- Concept: `concept_name` (slugified)
- Card type: `definition`, `equation`, `assumption`, `comparison`, `edge_case`, `application`, `connection`, `wrong_intuition`
- Difficulty: `easy`, `medium`, `hard`

## Session Flow

### Full generation mode:
```
/anki
```
1. Ask: "Generate cards for the entire course, or specific concepts?"
2. If entire course: generate cards for ALL concepts in `COURSE.md`
3. If specific: ask which concepts
4. Generate cards following the principles above
5. Export to `anki_export.txt`
6. Report: total cards generated, by concept, by type

### Incremental mode:
```
/anki [concept name]
```
Generate cards for a single concept and APPEND to existing `anki_export.txt`.

### From mistakes mode:
```
/anki mistakes
```
Read `mistakes.jsonl` and generate cards specifically targeting logged errors. These are the highest-value cards — they address YOUR specific weaknesses, not generic material.

## Quality Rules

1. **Minimum 5 cards per concept, maximum 20** — enough to cover the concept, not so many it becomes noise
2. **Every equation card must have a "what does each variable mean" companion**
3. **Every method card must have a "when NOT to use this" companion**
4. **Comparison cards exist for every pair of easily-confused concepts** (read `mistakes.jsonl` for confusion patterns)
5. **Cards reference the compressed form from `/explainer`** (Layer 9) where available
6. **No card should require > 30 seconds to answer** — if it does, split it

## Output Summary

After generation:
```
## Anki Export Complete

File: anki_export.txt
Total cards: [N]
Deck: [CourseName]

By tier:
- Tier 1 (Foundations): [N] cards across [M] concepts
- Tier 2 (Core): [N] cards across [M] concepts
- Tier 3 (Advanced): [N] cards across [M] concepts

By type:
- Definition: [N]
- Equation: [N]
- Assumption: [N]
- Comparison: [N]
- Edge case: [N]
- Application: [N]
- Wrong intuition: [N]

Import instructions:
1. Open Anki → File → Import
2. Select anki_export.txt
3. Set "Fields separated by: Tab"
4. Set "Allow HTML in fields"
5. Map columns: Field 1 → Front, Field 2 → Back, Field 3 → Tags
6. Import

Cards will appear under deck: [CourseName] with subdecks per topic.
```

## Integration with Other Skills

- After `/explainer`: auto-suggest "Generate Anki cards for this concept?"
- After `/mistakes review`: auto-suggest "Generate mistake-targeted cards?"
- The `/master` skill should recommend `/anki mistakes` when error patterns accumulate

## Usage
```
/anki                    — full course or choose concepts
/anki [concept name]     — single concept, append to existing
/anki mistakes           — cards targeting your logged errors
```
