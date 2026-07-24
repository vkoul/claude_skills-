# Mock Oral Exam

Simulate a timed, closed-book oral examination. Grades not just correctness but clarity, completeness, and ability to handle follow-ups under pressure.

## Session Flow

1. Read `COURSE.md` and `progress.json`.
2. Announce format:
   ```
   ORAL EXAM — [Course Name]
   Duration: [10/15/20 minutes — ask or default to 15]
   Rules: No looking anything up. Answer as if speaking to an examiner.
   I will ask 4-6 questions, with follow-ups.
   Grading: Correctness (40%) | Clarity (30%) | Completeness (30%)
   
   Ready? Type "go" to start the clock.
   ```
3. Start timer on "go".
4. Ask questions that:
   - Span multiple concepts from `COURSE.md`
   - Start with a broad conceptual question, then drill into specifics
   - Include at least one "what if?" variation
   - Include at least one "how would you explain this to a non-expert?"
   - Target concepts where `progress.json` shows incomplete mastery
5. After each answer:
   - If incomplete: one follow-up, then move on (time pressure)
   - If wrong: note it, ask one clarifying question, then move on
   - If good: escalate difficulty on next question
6. When time is up (or all questions asked), deliver the grade report.

## Grading Rubric

### Correctness (40%)
- All key facts accurate
- No logical errors
- Proper use of terminology

### Clarity (30%)
- Could a peer understand your explanation?
- Structured answer (not rambling)
- Appropriate level of formality

### Completeness (30%)
- Addressed all parts of the question
- Mentioned relevant caveats/assumptions
- Connected to broader context when appropriate

## Grade Report Format

```
## Oral Exam Report — [Date]

### Overall Grade: [A/B/C/D/F]

### Question-by-Question

**Q1**: [question]
- Your answer: [brief summary]
- Grade: [letter] — [one-line feedback]
- Gap identified: [if any]

[...]

### Strengths
- [what you did well]

### Critical Gaps
- [what would fail you in a real exam]

### Recommended Actions
- [specific skills to run next to close gaps]
```

## After the Exam

- Update `progress.json`: set `oral_pass: true` for concepts answered correctly and clearly
- Log gaps to `mistakes.jsonl`
- If overall grade < B: recommend specific concepts to revisit

## Usage
```
/oral-exam
/oral-exam [duration in minutes]
```
