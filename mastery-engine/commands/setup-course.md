# Setup Course

Bootstrap the mastery engine for a new course. Starts with a learner intake interview, then reads raw materials and generates the structured files all other skills depend on.

## Step 0: Learner Intake Interview

**Before touching any files**, ask these questions to personalize the experience. Store answers in `learner.json`.

### Questions to Ask:

1. **Background & Level**
   - "What's your background? (e.g., data scientist, econ PhD student, self-taught programmer, career switcher)"
   - "On a scale of 1-5, how familiar are you ALREADY with this subject? (1 = first exposure, 3 = seen it before but gaps, 5 = reviewing for depth)"
   - "What related topics do you already know well? (These become anchor points for explanations)"

2. **Goals & Timeline**
   - "What's your goal? (pass an exam, deep understanding for research, use at work, teach others)"
   - "What's your timeline? (2 weeks, 1 month, semester-long, no deadline)"
   - "Is there a specific exam/deliverable? If so, what format? (multiple choice, problem sets, oral, project)"

3. **Learning Style & Constraints**
   - "How much time per session? (30 min, 1 hour, 2+ hours)"
   - "How many sessions per week?"
   - "What's your weakness? (math notation scares me / I memorize but don't understand / I understand but can't apply / I can't derive from scratch / I freeze under pressure)"
   - "Preference: start with intuition and build to formalism, or start with formalism and build intuition?"

4. **Prior Attempts (if any)**
   - "Have you tried learning this before? What didn't work?"
   - "Are there specific concepts you KNOW you're weak on?"

### Generate `learner.json`:

```json
{
  "background": "data scientist, 5 years, strong Python, moderate stats",
  "familiarity": 3,
  "prior_knowledge": ["basic probability", "regression", "hypothesis testing", "causal inference basics"],
  "goal": "deep understanding for research",
  "timeline": "6 weeks",
  "exam_format": null,
  "session_length_minutes": 60,
  "sessions_per_week": 4,
  "weakness": "can't derive from scratch, hand-wave through proofs",
  "preference": "intuition first, then formalism",
  "prior_attempts": "Read Kohavi book but didn't do exercises, fuzzy on power analysis",
  "known_weak_spots": ["power analysis", "multiple testing corrections"],
  "created": "YYYY-MM-DD"
}
```

## Step 1: Scan Materials

Scan the current directory for course materials: PDFs, slides, extracted text files, markdown notes, problem sets, syllabi, textbooks. Read as many as needed to understand the full scope.

## Step 2: Generate Personalized `COURSE.md`

Use `learner.json` to customize the output:

```markdown
# [Course Title]

## Overview
[1-2 sentence description of the course]

## Learner Context
- Background: [from learner.json]
- Starting level: [familiarity score]
- Goal: [goal]
- Timeline: [timeline] → [calculated sessions available]
- Focus areas: [derived from weakness + known weak spots]

## Source Materials
- [list of files found and what they cover]

## Concepts (ordered by prerequisite dependency)

### Tier 1: Foundations
- **Concept Name** — one-line description
  - Prerequisites: none
  - Key theorems/algorithms: [list]
  - Difficulty: 1-5
  - Priority: [HIGH/MEDIUM/LOW — based on learner's goal and weak spots]
  - Skip if: [condition — e.g., "learner already has familiarity ≥ 4 in this area"]

### Tier 2: Core
[...]

### Tier 3: Advanced
[...]

## Personalized Learning Path
Based on your background, goals, and timeline:

### Phase 1: [Weeks 1-2] — [Focus]
- Concepts to cover: [list, respecting dependencies]
- Emphasis: [what the learner needs based on their weakness]
- Skip: [what they already know — mark as "pre-mastered" in progress.json]

### Phase 2: [Weeks 3-4] — [Focus]
[...]

### Phase 3: [Weeks 5-6] — [Focus]
[...]

## Concept Dependency Graph
[Mermaid diagram showing which concepts depend on which]

## Problem Style Guide
[Description of how problems in this course are styled]

## Adaptation Notes
- Because weakness is "[X]", extra emphasis on: [specific skills]
- Because goal is "[Y]", can deprioritize: [what matters less]
- Because timeline is [Z], pace should be: [calculation]
- Because prior knowledge includes [A, B], these concepts start at "familiar" not "unstarted"
```

## Step 3: Generate Personalized `progress.json`

```json
{
  "course": "Course Title",
  "setup_date": "YYYY-MM-DD",
  "learner_profile": "learner.json",
  "target_completion": "YYYY-MM-DD",
  "sessions_planned": 24,
  "sessions_completed": 0,
  "concepts": {
    "concept_already_known": {
      "status": "familiar",
      "derive_pass": false,
      "skeptic_pass": false,
      "problems_pass": false,
      "oral_pass": false,
      "last_reviewed": null,
      "mistakes_count": 0,
      "priority": "low",
      "skip_reason": "learner has prior knowledge"
    },
    "concept_new_and_critical": {
      "status": "unstarted",
      "derive_pass": false,
      "skeptic_pass": false,
      "problems_pass": false,
      "oral_pass": false,
      "last_reviewed": null,
      "mistakes_count": 0,
      "priority": "high",
      "skip_reason": null
    }
  }
}
```

**Status values:**
- `unstarted` — haven't touched it
- `familiar` — learner claims prior knowledge (still needs gate validation)
- `in_progress` — actively working through gates
- `mastered` — all gates passed

**For "familiar" concepts:** The student doesn't get a free pass. `/master` will still require at LEAST one gate (typically `/oral-exam` or `/generate-problems`) to verify the claim. But it won't make them do the full `/explainer` flow for stuff they already know.

## Step 4: Create empty `mistakes.jsonl` (if not exists)

## Step 5: Create `problems/` directory (if not exists)

## Step 6: Create `derivations/` directory (if not exists)

## Step 7: Generate `dashboard.html`

The interactive progress visualization and concept dependency graph. Follow the full specification in `/dashboard` to produce a self-contained HTML file with:
- Progress overview
- Concept dependency DAG (color-coded by priority too — high priority concepts highlighted)
- Concept detail cards on click
- Learner profile summary visible at top
- Timeline progress bar (sessions completed / sessions planned)
- Dark/light mode
- All data embedded inline, opens in any browser

## Step 8: Generate Personalized Recommendations

Print a summary that includes:
- How many concepts found, which are pre-known vs new
- The personalized learning path (phased)
- First recommended action based on their profile
- Estimated sessions to mastery given their pace
- Tell them to open `dashboard.html`

## Rules

- If `COURSE.md` already exists, ask whether to overwrite or merge.
- If `learner.json` already exists, ask whether to redo the interview or keep existing profile.
- Extract concepts at independently-testable granularity.
- Infer prerequisites from material order and explicit references.
- **Respect the learner's stated weakness** — if they say "I can't derive", weight `/derive` gates higher. If they say "I freeze under pressure", introduce `/oral-exam` early to build tolerance.
- **Respect the timeline** — if they have 2 weeks, focus on high-priority concepts only and skip low-priority ones. If they have a semester, cover everything including depth.
- **Respect prior knowledge** — don't waste time re-teaching what they know. But DO verify claimed knowledge with at least one gate before marking it mastered.

## Usage
```
/setup-course
```
Run this in any folder containing course materials. The intake interview starts immediately.
