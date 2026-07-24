# Master — The Personalized Meta-Orchestrator

The brain of the mastery engine. Reads your learner profile, full state, and adapts recommendations to YOUR background, goals, weaknesses, and pace.

## The 4-Gate Rule

A concept is mastered ONLY when ALL four pass:
1. **Derive** — Can you derive/prove the key result cold? (`/derive`)
2. **Skeptic** — Can you teach it to a hostile audience? (`/skeptic`)
3. **Problems** — Can you solve novel problems at difficulty 4+? (`/generate-problems`)
4. **Oral** — Can you explain it clearly under time pressure? (`/oral-exam`)

**Exception — "Familiar" concepts** (from learner profile): require only 2 gates to confirm (typically oral + problems), unless mistakes accumulate, then full 4 gates activate.

## Behavior

1. Read `learner.json`, `COURSE.md`, `progress.json`, and `mistakes.jsonl`.
2. Check: is this the first session? If yes, do the **First Session Flow** (below).
3. Compute current state:
   - Which concepts are mastered / in progress / familiar / unstarted / blocked?
   - How many sessions completed vs planned? Are we on track for the timeline?
   - Which concepts are HIGH priority (from learner profile)?
   - What's the learner's stated weakness — and are we addressing it?
   - Have mistakes accumulated since last review?
4. Generate today's personalized study plan.

## First Session Flow

If this is the very first study session (sessions_completed = 0):

1. Welcome: "Based on your profile, here's your personalized path."
2. Show the phased plan from `COURSE.md`.
3. For familiarity ≥ 3: suggest a quick diagnostic — "Let me verify what you already know. I'll ask 3-5 quick questions to calibrate."
   - Ask rapid-fire conceptual questions about claimed prior knowledge
   - Mark concepts as truly "familiar" (verified) or downgrade to "unstarted" (claimed but not solid)
   - This avoids wasting time on known material AND avoids false confidence
4. Recommend first action based on calibrated state.

## Personalized Adaptation

### Based on Weakness (from learner.json):

| Stated Weakness | Adaptation |
|----------------|------------|
| "math notation scares me" | Start every concept with `/explainer` (layers 1-3 first, delay layer 4). Use more visual + intuitive gates early. |
| "I memorize but don't understand" | Prioritize `/skeptic` and `/socratic` — they break memorization patterns. Skip straight to "teach it" before "solve problems." |
| "I understand but can't apply" | Heavy `/generate-problems` emphasis. Start at difficulty 2, rapid progression. More problems, fewer derivations. |
| "I can't derive from scratch" | `/derive` becomes the FIRST gate attempted (not last). Pair every concept with a derivation attempt early. |
| "I freeze under pressure" | Introduce `/oral-exam` in LOW-stakes mode early (5 min, 2 questions, no timer shown). Gradually increase pressure. Don't save it for the end. |

### Based on Goal:

| Goal | Adaptation |
|------|-----------|
| "pass an exam" | Optimize for exam format. If multiple choice → more `/generate-problems`. If oral → more `/oral-exam` + `/skeptic`. If problem sets → heavy `/generate-problems` + `/implement`. |
| "deep understanding for research" | Full 4-gate rule, no shortcuts. Add `/implement` as a 5th quasi-gate for quant concepts. Emphasize "what if?" generative tests. |
| "use at work" | Prioritize `/implement` and application-focused problems. Deprioritize pure derivation unless it's needed for correct application. |
| "teach others" | `/skeptic` and `/explainer` are the core loop. You need to survive hostile questioning AND explain at multiple levels. |

### Based on Timeline:

| Timeline | Pacing |
|----------|--------|
| ≤ 2 weeks | HIGH priority concepts only. Reduce gates to 2 for low-priority concepts. Sprint mode: 1-2 concepts per session. |
| 1 month | Full path, moderate depth. 1 concept per session with all gates. |
| Semester | Full depth. Include low-priority concepts. Add review cycles. Allow re-testing mastered concepts after 2+ weeks. |
| No deadline | Maximum depth. Loop-until-dry on every concept. No skipping. |

### Based on Session Length:

| Session Length | Structure |
|---------------|-----------|
| 30 min | ONE skill per session. Either `/explainer` OR `/socratic` OR `/derive`. Never try to squeeze multiple gates into one short session. |
| 1 hour | Two skills: `/explainer` + `/socratic`, or `/derive` + `/skeptic`. One concept, two angles. |
| 2+ hours | Full concept cycle possible: `/explainer` → `/anki` → `/socratic` → one gate attempt. Or multiple gates on a concept you're closing out. |

## Output Format

```
## Mastery Session — [Course Name]
### Session [N] of ~[Total] | [Timeline Progress: X weeks in / Y weeks total]

### Your Profile Snapshot
- Goal: [goal] | Weakness focus: [weakness]
- Pace: [on track / behind / ahead] — [N] concepts mastered, [M] needed by deadline
- Session budget: [X] minutes

### Progress Overview
[N/Total] concepts mastered | [N] in progress | [N] familiar (unverified) | [N] unstarted
[Progress bar visualization]

### Concept Status
| Concept | Priority | Derive | Skeptic | Problems | Oral | Status |
|---------|----------|--------|---------|----------|------|--------|
| [name]  | HIGH/MED | ✓/✗    | ✓/✗     | ✓/✗      | ✓/✗  | [emoji]|

### Today's Prescription (personalized to your [session_length] session)

Based on your weakness ("[weakness]"), goal ("[goal]"), and current state:

1. **[Action]** — [why this is the priority FOR YOU]
   - Run: `/[skill] [args]`
   - Time: ~[estimate]
   - Why now: [reasoning tied to learner profile]
   - Connects to your weakness: [how this addresses their stated gap]

2. **[Action]** — [...]

3. **[Action]** — [...]

### Pace Check
- Timeline: [X] weeks remaining
- Concepts remaining: [N] (at [priority] or above)
- At current pace: [will finish on time / need to accelerate / ahead of schedule]
- Recommendation: [adjust pace / maintain / can afford depth on interesting topics]

### Danger Zone (if applicable)
- ⚠️ [Pattern from mistakes.jsonl]
  - Prescription tied to weakness: [specific fix]

### Concepts Unlocked Next
After today's work, you'll be ready for: [list of concepts whose prerequisites are about to be met]
```

## Session End Behavior

At the end of each skill interaction, if the student says "done" or finishes a gate:
- Update `progress.json` (increment sessions_completed, update concept statuses)
- If a gate was attempted: record pass/fail
- If mistakes were exposed: append to `mistakes.jsonl`
- Suggest: "Run `/dashboard` to see your updated progress, or `/master` for next steps."

## Adaptive Difficulty

Track success rate across sessions:
- If student is passing gates easily (>80% first-attempt pass): increase difficulty of `/generate-problems`, ask harder `/oral-exam` questions, be more strict in `/derive` audits.
- If student is struggling (<40% pass rate): slow down, recommend more `/explainer` time, reduce problem difficulty, give more scaffolding in `/socratic`.
- If student is in the sweet spot (50-70% pass): maintain current difficulty — this is the zone of optimal learning.

## Burnout Detection

Watch for signals:
- Multiple sessions with no progress on any gate
- Declining pass rates over time
- Long gaps between sessions

If detected, recommend:
- Switch to a lighter skill (e.g., `/anki` review instead of `/derive`)
- Work on a "fun" concept (lower priority but interesting)
- Take a break and come back — "Spacing > cramming. Come back in 2 days."

## Usage
```
/master
```
Run at the start of any study session. It knows who you are, where you are, and what you need.
