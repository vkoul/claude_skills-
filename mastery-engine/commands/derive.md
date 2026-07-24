# Derivation-from-Scratch Checker

You derive a theorem, proof, or formula cold (closed-book). Then this skill performs a rigorous audit of your derivation.

## Session Flow

1. Read `COURSE.md` to know available theorems/proofs.
2. Ask: "Which theorem or result do you want to derive?" (or accept it as an argument)
3. Say: "Go ahead. Write your derivation from scratch. I'll audit it when you're done."
4. **Wait for the student's derivation.** Do not interrupt or help.
5. Once submitted, perform the **Rigor Audit**.

## Rigor Audit Checklist

For each step in the derivation, check:

1. **Hand-waving**: "it's obvious that", "clearly", "it follows" — without showing WHY
2. **Skipped steps**: logical jumps where an intermediate result is needed
3. **Unproven assumptions**: using a result that hasn't been established or cited
4. **Notation errors**: inconsistent variable names, index misuse, dimension mismatches
5. **Logical errors**: non-sequiturs, circular reasoning, affirming the consequent
6. **Boundary/edge cases ignored**: "for all x" claims without checking edge cases
7. **Direction errors**: proving the converse instead of the statement

## Output Format

```
## Derivation Audit: [Theorem Name]

### Overall Grade: [A/B/C/D/F]
- A: Publication-ready. Airtight.
- B: Correct reasoning, minor gaps a reader could fill.
- C: Right idea, but multiple steps need tightening.
- D: Serious logical gaps or errors.
- F: Fundamental misunderstanding.

### Step-by-Step Analysis

**Step 1**: [quote their step]
- ✓ Valid / ⚠️ Gap / ✗ Error
- [Explanation of the issue]

**Step 2**: [...]

### Critical Gaps
1. [Most serious issue, with explanation of what's missing]
2. [...]

### What a Perfect Derivation Includes
[Brief sketch of what they missed — not the full derivation, just the pieces]
```

## After the Audit

- If grade is A or B: update `progress.json` → `derive_pass: true` for this concept
- If grade is C or below: log to `mistakes.jsonl` and suggest re-attempting after reviewing the specific gap
- Ask: "Want to try again with the gaps identified, or move on?"

## Mistake Log Format
```json
{"date": "YYYY-MM-DD", "concept": "concept_name", "type": "derivation_gap", "description": "Skipped showing X in proof of Y", "severity": "medium", "source": "derive"}
```

## Usage
```
/derive [optional: theorem name]
```
