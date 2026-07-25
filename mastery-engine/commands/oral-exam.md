# Mock Oral Exam

Simulate a timed, closed-book oral examination. Grades not just correctness but clarity, completeness, and ability to handle follow-ups under pressure.

## Model Instructions

This skill works with ANY Claude model (Haiku, Sonnet, Opus). Follow this exact sequence:
1. Read `COURSE.md` and `progress.json`. Pick 4-6 questions targeting concepts marked "in_progress" or "familiar."
2. Announce the exam format (use template below). Wait for "go."
3. Ask ONE question at a time. After the student answers, either:
   - Ask a follow-up (if answer was incomplete): "Can you elaborate on [specific part]?"
   - Move to next question (if answer was good or time is short)
4. After all questions, OUTPUT the grade report using the exact template.
5. Grade each answer on three axes: Correctness (is it right?), Clarity (could a peer understand?), Completeness (all parts addressed?).
6. Overall grade: A = all answers correct+clear, B = mostly correct with minor gaps, C = some wrong, D = major gaps, F = fundamental misunderstanding.

## Personalization (reads learner.json)

| Learner Attribute | Adaptation |
|-------------------|-----------|
| `weakness: "I freeze under pressure"` | Start with a LOW-PRESSURE mode: shorter (5 min), only 2 questions, no visible timer. Build up gradually across sessions. First few oral exams should build confidence, not crush it. |
| `exam_format: "oral"` or `goal: "teach others"` | Full intensity from the start — they need this pressure. Include "explain to a non-expert" questions heavily. |
| `exam_format: "multiple choice"` | De-emphasize oral fluency, emphasize quick recall and discrimination between similar concepts. |
| `familiarity: 1-2` | Keep questions at conceptual level (Layer 1-2 knowledge). Don't ask derivation questions in oral format until they've passed `/derive`. |
| `familiarity: 4-5` | Ask synthesis questions that combine concepts. Expect precise terminology. Probe the edges. |
| `session_length_minutes: 30` | Default to 10-minute exam (leaves time for debrief). For 60+ min sessions, do full 15-20 min. |
| `background` | Ask one question framed in their domain: "In your role as a [background], when would this apply?" |

## Session Flow

1. Read `learner.json`, `COURSE.md`, and `progress.json`.
2. Announce format (adapted to learner profile):
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
