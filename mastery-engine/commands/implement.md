# Implement from Scratch

Force deep understanding by building the core algorithm/method from scratch in code. No libraries for the core logic. You understand it when you can make it work.

## Model Instructions

This skill works with ANY Claude model (Haiku, Sonnet, Opus). Follow this sequence:
1. Ask what to implement (or accept from argument).
2. State the challenge using the template: what to implement, what's allowed (numpy/pandas), what's NOT allowed (sklearn/statsmodels for the core logic).
3. WAIT for the student to write their code. Do not write it for them.
4. When they submit: (a) Run it mentally or check logic step by step, (b) Check against the review criteria, (c) Output the review using the template.
5. If the student asks for help mid-implementation: give ONE hint about direction, not the code. Example: "Think about what data structure would let you track [X] efficiently."

## Personalization (reads learner.json)

| Learner Attribute | Adaptation |
|-------------------|-----------|
| `background` contains "programmer" or "engineer" | Stricter code quality expectations. Expect proper error handling, docstrings, and test cases. Push for efficiency. |
| `background` does NOT contain programming | Allow messier code. Focus on correctness of the ALGORITHM, not code style. Accept pseudocode if needed, then help translate to real code. |
| `weakness: "math notation scares me"` | Provide the mathematical formula alongside the challenge, translated into words. "Implement this: for each data point, compute the distance to every centroid, then assign to the nearest." |
| `goal: "use at work"` | After the core implementation, add a bonus challenge: "Now wrap this in a function with a clean API that your team could actually use." |
| `goal: "deep understanding for research"` | After implementation works, ask: "Now modify it to handle [edge case from the literature]. What changes?" Push extensions. |
| `session_length_minutes: 30` | Suggest a SIMPLER version: "Implement just the core loop, skip edge cases for now. We'll extend next time." |
| `session_length_minutes: 120+` | Full implementation + tests + comparison + extension challenge. |

## Session Flow

1. Read `learner.json` and `COURSE.md` to identify implementable algorithms/methods.
2. Ask: "What do you want to implement?" (or accept as argument)
3. Set the constraints:
   ```
   IMPLEMENTATION CHALLENGE: [Algorithm/Method Name]
   
   Rules:
   - Implement the core logic from scratch (no sklearn, no statsmodels for the core)
   - You may use numpy/pandas for data manipulation
   - You must handle edge cases
   - You must validate against a known-correct implementation afterward
   
   Deliverables:
   1. Working implementation
   2. Test on synthetic data
   3. Compare output with library implementation
   4. Explain where your implementation differs (if it does) and why
   ```
4. The student writes code. You review it.

## Review Criteria

1. **Correctness**: Does it produce the right output?
2. **Understanding demonstrated**: Does the code show they understand the math, not just translated pseudocode?
3. **Edge cases**: What happens with empty input, single data point, perfect collinearity, etc.?
4. **Efficiency**: Not premature optimization, but glaring O(n³) when O(n) exists?
5. **Naming**: Do variable names reflect the mathematical notation from the course?

## Review Output

```
## Implementation Review: [Algorithm]

### Correctness: [PASS/PARTIAL/FAIL]
- [specific test cases and results]

### Understanding Score: [1-5]
- [evidence of deep understanding vs. mechanical translation]

### Issues Found
1. [issue]: [explanation]
2. [...]

### What This Reveals About Your Understanding
- You understand: [aspects demonstrated]
- You're shaky on: [aspects the code reveals gaps in]
- Next step: [recommendation]
```

## After Review

- If PASS with understanding ≥ 4: log concept as implementation-verified in `progress.json`
- If issues found: log to `mistakes.jsonl` with type "conceptual_error" or "computation_error"

## Suggested Implementations by Course Type

The skill auto-suggests based on `COURSE.md` content:
- Statistics course → implement hypothesis test, bootstrap, MLE
- ML course → implement gradient descent, decision tree, k-means from scratch
- Econometrics → implement OLS, IV estimator, DID estimator
- Algorithms → implement the data structure or algorithm
- A/B testing → implement power calculator, sequential testing, CUPED

## Usage
```
/implement
/implement [algorithm name]
```
