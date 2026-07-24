# Adversarial Problem Generator

Generate novel problems in the course's style at increasing difficulty. These are NOT from the textbook — they are generated fresh to force transfer, not memorization.

## Behavior

1. Read `COURSE.md` (for concepts, difficulty tiers, and Problem Style Guide).
2. Read any existing problem sets in the folder (as style reference only — never reproduce them).
3. Ask: "Which concept(s)? What difficulty (1-5)? How many problems?"
   - Or accept as arguments: `/generate-problems "power analysis" 3 5` (concept, difficulty, count)
4. Generate problems that:
   - Match the course's notation, style, and format
   - Are genuinely novel (not rephrased textbook problems)
   - Test TRANSFER: apply the concept in an unfamiliar context
   - At difficulty 4-5: combine multiple concepts or add realistic complications

## Problem Design Principles

- **Difficulty 1-2**: Direct application. One concept, clean numbers, clear setup.
- **Difficulty 3**: Requires choosing the right approach. Mild ambiguity in setup.
- **Difficulty 4**: Combines concepts. Requires insight or a non-obvious first step.
- **Difficulty 5**: Research-adjacent. Novel framing. May require extending course material slightly.

## Output Format

For each problem:
```
## Problem [N] — [Concept] — Difficulty [1-5]

[Problem statement]

---
<details>
<summary>Hint (only if stuck)</summary>
[A directional hint, not the solution approach]
</details>

<details>
<summary>Solution</summary>
[Full worked solution with reasoning at each step]
</details>
```

## After Generation

- Save problems to `problems/[concept]_[date]_d[difficulty].md`
- If the student attempts them and gets them wrong, log to `mistakes.jsonl`
- Track: "3/5 correct at difficulty 3" → ready for difficulty 4

## Adversarial Mode (Difficulty 5+)

At the highest level, generate problems that are specifically designed to trigger common misconceptions logged in `mistakes.jsonl`. If the student has a pattern of error X, generate a problem where error X is the obvious-but-wrong approach.

## Usage
```
/generate-problems
/generate-problems "concept_name" [difficulty] [count]
```
