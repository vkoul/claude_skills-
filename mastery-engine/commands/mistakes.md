# Misconception Tracker

Analyze your accumulated mistakes, find patterns, and resurface errors before they become permanent. This is not generic spaced repetition — it's diagnostic.

## Model Instructions

This skill works with ANY Claude model (Haiku, Sonnet, Opus). Follow these rules:
1. Check which mode was invoked: no argument = analysis, "review" = resurface, "add" = log new mistake.
2. Read `mistakes.jsonl` — each line is one JSON object.
3. For **analysis mode**: group mistakes by concept, count occurrences, find the top 3 patterns. Use the output template.
4. For **review mode**: pick the 3 highest-severity unresolved mistakes. For each, ask ONE question (Socratic — don't give the answer). If the student answers correctly, mark as resolved.
5. For **add mode**: ask these 4 questions one at a time: (1) What concept? (2) What was the error? (3) Why did it happen? (4) What skill exposed it? Then append one JSON line to `mistakes.jsonl`.
6. If `mistakes.jsonl` is empty or missing, say: "No mistakes logged yet. They'll accumulate as you use other skills. Add one manually with `/mistakes add`."

## Behavior

1. Read `mistakes.jsonl` from the current directory.
2. Read `progress.json` for context.
3. Perform analysis based on mode.

## Modes

### Default: Pattern Analysis
```
/mistakes
```
Analyze all logged mistakes and report:
- **Error clusters**: which concepts have the most mistakes?
- **Error types**: are you making reasoning errors, computation errors, or conceptual errors?
- **Recurring patterns**: the SAME mistake appearing across different problems
- **Predictions**: "Based on your pattern, you WILL make this error on [concept X] because it has the same structure as [concept Y] where you failed."

### Review Mode: Resurface
```
/mistakes review
```
Pick the 3 most dangerous unresolved mistakes and:
1. Present the original error context
2. Ask: "What went wrong here?" (Socratic — don't give the answer)
3. If corrected: mark as resolved
4. If not: escalate severity

### Add Mode: Log a New Mistake
```
/mistakes add
```
Prompt for:
- What concept?
- What was the error?
- Why did it happen? (if known)
- Where? (which skill/problem exposed it)

Then append to `mistakes.jsonl`.

## Analysis Output Format

```
## Misconception Report — [Date]

### Top Error Patterns (most dangerous first)

**Pattern 1: [Name]** — [N occurrences]
- Concept: [X]
- The error: [specific description]
- Why it keeps happening: [root cause hypothesis]
- Prediction: "You will make this mistake again when you encounter [scenario]"
- Prescription: [specific action — derive X, solve 3 problems targeting this, etc.]

**Pattern 2: [...]**

### Concepts at Risk
| Concept | Mistake Count | Last Error | Severity | Recommended Action |
|---------|--------------|------------|----------|-------------------|
| [...]   | [N]          | [date]     | [H/M/L]  | [action]          |

### Progress Since Last Review
- Mistakes resolved: [N]
- New mistakes: [N]
- Recurring mistakes (unresolved): [N] ← THIS IS THE DANGER ZONE
```

## mistakes.jsonl Format

Each line is a JSON object:
```json
{
  "date": "YYYY-MM-DD",
  "concept": "concept_name",
  "type": "reasoning_gap|computation_error|conceptual_error|notation_error|edge_case_missed",
  "description": "Specific description of what went wrong",
  "severity": "high|medium|low",
  "source": "socratic|derive|generate-problems|oral-exam|skeptic|manual",
  "resolved": false,
  "resolved_date": null
}
```

## Usage
```
/mistakes              — pattern analysis
/mistakes review       — resurface top errors
/mistakes add          — log a new mistake manually
```
