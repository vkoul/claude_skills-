# Master — The Meta-Orchestrator

The brain of the mastery engine. Reads your full state and tells you exactly what to do next. Enforces the 4-gate rule: no concept is "owned" until you pass all four gates.

## The 4-Gate Rule

A concept is mastered ONLY when ALL four pass:
1. **Derive** — Can you derive/prove the key result cold? (`/derive`)
2. **Skeptic** — Can you teach it to a hostile audience? (`/skeptic`)
3. **Problems** — Can you solve novel problems at difficulty 4+? (`/generate-problems`)
4. **Oral** — Can you explain it clearly under time pressure? (`/oral-exam`)

## Behavior

1. Read `COURSE.md`, `progress.json`, and `mistakes.jsonl`.
2. Compute current state:
   - Which concepts are fully mastered (all 4 gates)?
   - Which are in progress (some gates passed)?
   - Which are blocked (prerequisites not met)?
   - Which have recurring mistakes?
3. Generate today's study plan.

## Output Format

```
## Mastery Dashboard — [Course Name]

### Progress Overview
[N/Total] concepts mastered | [N] in progress | [N] not started

### Concept Status
| Concept | Derive | Skeptic | Problems | Oral | Status |
|---------|--------|---------|----------|------|--------|
| [name]  | ✓/✗    | ✓/✗     | ✓/✗      | ✓/✗  | [🟢🟡🔴] |

### Today's Prescription

Based on your current state, here's what to do (in order):

1. **[Action]** — [why this is the priority]
   - Run: `/[skill] [args]`
   - Time: ~[estimate]
   - Why now: [reasoning based on dependencies, mistakes, or gaps]

2. **[Action]** — [...]

3. **[Action]** — [...]

### Danger Zone (Recurring Mistakes)
- ⚠️ [Pattern]: [description] — last seen [date], [N] occurrences
  - Prescription: [specific fix]

### Blocked Concepts (Prerequisites Not Met)
- [Concept X] needs: [Concept A ✓, Concept B ✗] — focus on B first

### Estimated Time to Full Mastery
Based on current velocity: ~[N] sessions ([M] hours)
```

## Decision Logic

Priority ordering for recommendations:
1. **Recurring mistakes** — fix these first, they compound
2. **Almost-done concepts** (3/4 gates) — close them out
3. **Prerequisite unblocking** — if concept D is blocked by B, work on B
4. **New concept introduction** — only when current tier is solid
5. **Review/maintenance** — re-test concepts mastered >2 weeks ago

## Smart Scheduling

- If the student has been doing heavy derivation work, recommend a lighter skill next (problems or skeptic)
- If mistakes are accumulating, force a `/mistakes review` before new material
- If a concept was "passed" but later mistakes reveal weakness, regress its status

## Usage
```
/master
```
Run at the start of any study session to know exactly what to do.
