# Implement from Scratch

Force deep understanding by building the core algorithm/method from scratch in code. No libraries for the core logic. You understand it when you can make it work.

## Session Flow

1. Read `COURSE.md` to identify implementable algorithms/methods.
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
