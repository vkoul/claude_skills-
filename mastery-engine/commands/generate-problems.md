# Adversarial Problem Generator

Generate novel problems in the course's style at increasing difficulty. These are NOT from the textbook — they are generated fresh to force transfer, not memorization.

## Model Instructions

This skill works with ANY Claude model (Haiku, Sonnet, Opus). Follow these rules:
1. Read `COURSE.md` first. Use the Problem Style Guide to match the course's format.
2. NEVER copy or rephrase problems from the source materials. Generate completely new scenarios.
3. Each problem must have: (a) problem statement, (b) one hint (hidden), (c) full solution (hidden).
4. Use the exact output template for each problem.
5. Difficulty calibration:
   - Difficulty 1-2: Change only the numbers/context from a textbook-style problem. Same structure, different story.
   - Difficulty 3: Combine the target concept with one other concept OR add a realistic complication.
   - Difficulty 4: Require a non-obvious first step, or present the problem in an unfamiliar framing.
   - Difficulty 5: Combine 3+ concepts, or require extending the method beyond what was taught.
6. If `mistakes.jsonl` exists and has entries for this concept, design at least one problem that specifically triggers that error pattern.

## Personalization (reads learner.json)

| Learner Attribute | Adaptation |
|-------------------|-----------|
| `goal: "pass an exam"` + `exam_format` | Match the exam format: if multiple choice, generate MCQs. If problem sets, generate multi-part problems. If oral, generate "explain why" problems. |
| `goal: "use at work"` | Frame problems in workplace contexts (real product scenarios, business metrics, not textbook setups). |
| `weakness: "I understand but can't apply"` | Start at difficulty 2 (not 3), with very explicit problem setup. Gradually remove scaffolding across the problem set. |
| `session_length_minutes` | Generate fewer problems for short sessions (3 for 30 min) vs more for long ones (7-10 for 2 hours). |
| `familiarity: 4-5` | Default starting difficulty is 3-4, not 1-2. Skip the warmup. |
| `prior_knowledge` | For problems combining concepts, prioritize combinations with concepts they already know (bridges new to old). |

## Behavior

1. Read `learner.json`, `COURSE.md` (for concepts, difficulty tiers, and Problem Style Guide).
2. Read any existing problem sets in the folder (as style reference only — never reproduce them).
3. Read `mistakes.jsonl` to inform adversarial problem design.
4. Ask: "Which concept(s)? What difficulty (1-5)? How many problems?"
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
