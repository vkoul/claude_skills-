# Setup Course

Bootstrap the mastery engine for a new course. Starts with a learner intake interview, then reads raw materials and generates the structured files all other skills depend on.

## Model Instructions

This skill works with ANY Claude model (Haiku, Sonnet, Opus). Follow the steps in EXACT order. Do not skip steps. Use the templates provided — fill in the blanks rather than inventing a new format.

## Supported Input Types

This skill handles ANY of the following (alone or combined):

| Input Type | How to Handle |
|-----------|---------------|
| **Textbook PDF** | Extract chapter titles, key concepts, theorems, definitions. Use table of contents as concept structure. |
| **Lecture slides** | Each slide deck = one or more concepts. Extract titles, key points, formulas. |
| **Lecture transcripts** (video/audio) | Scan for topic transitions ("now let's talk about..."), definitions, examples. More verbose — focus on extracting the CONCEPTS, not the filler. |
| **A single book** | Use table of contents as the primary structure. Chapter → concepts. Section headers → sub-concepts. |
| **Problem sets / exams** | Reverse-engineer which concepts they test. Each problem type = a concept that needs to be in the map. |
| **Course syllabus / webpage** | Use as the authoritative concept list and ordering. Fill in details from other materials. |
| **Handwritten notes** (text extracted) | Treat like lecture transcripts — scan for key terms and structures. |
| **YouTube video transcripts** | Same as lecture transcripts. Look for topic shifts and key definitions. |
| **Just a book title + no files** | Ask the user what chapters/topics to focus on. Generate concept structure from your training knowledge of that subject. |
| **Mixed bag of all the above** | Use the syllabus/TOC as the skeleton, fill in with details from all other sources. |

**Rule:** If the folder has ANYTHING in it, you can work with it. If the folder is empty, ask: "What course/book/subject? I'll structure it from what I know."

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
- **Tell them that `START_HERE.md` has instructions for resuming in future sessions**

## Step 9: Create Resume/Continuation Guide

Generate a `START_HERE.md` file that explains how learners can resume from where they left off. This is critical for learners who study across multiple sessions.

```markdown
# 🚀 Quick Start Guide - [Course Name]

## When You Return, Do This:

### Step 1: Check Your Progress
Open `dashboard.html` in your browser to see:
- Which concepts you've completed
- Your mistakes count
- Session progress (X/N sessions done)

### Step 2: Resume Learning

**SIMPLEST: Use Auto-Resume** (Recommended)
Type `/master` and the system will:
- Read your `progress.json` automatically
- Find where you left off
- Continue from your last concept
- Choose the right learning activity
- Update progress as you complete gates

**Alternative: Pick a Specific Concept**
- `/explainer <concept_name>` - Deep understanding (intuition → formalism)
- `/generate-problems <concept_name>` - Practice problems immediately
- `/oral-exam <concept_name>` - Test yourself with questions
- `/derive <concept_name>` - Prove you can build it from scratch
- `/mistakes` - Review your error patterns

### Step 3: Track Progress

The system automatically updates `progress.json` as you:
- Complete explainer gates
- Solve practice problems
- Pass oral exams
- Log mistakes

**You never need to manually edit progress.json** - all skills read and write to it automatically.

---

## 📝 Typical Multi-Session Workflow

**Session 1 (First time):**
1. Type `/master`
2. System: "Starting Session 1 with [Concept A]. Let's begin with /explainer."
3. Work through concept
4. System automatically marks concept as "in_progress" in progress.json

**Session 2 (Next day):**
1. Type `/master` 
2. System: "Last time you started [Concept A]. You completed explainer but need practice. Shall I generate problems?"
3. Continue with problems
4. System marks "problems_pass": true when done

**Session 3 (Day after):**
1. Type `/master`
2. System: "You've mastered [Concept A]! Moving to [Concept B]. Starting with /explainer..."
3. New concept begins
4. System updates status: Concept A → "mastered", Concept B → "in_progress"

**Session N (Final session):**
1. Type `/master`
2. System: "All concepts complete! Running final assessment..."
3. Get completion report

---

## 🗂️ File Reference

| File | Purpose | Auto-Updated? |
|------|---------|---------------|
| `progress.json` | Tracks concept status, gates passed, mistakes | ✅ Yes - by all skills |
| `mistakes.jsonl` | Logs every mistake with context | ✅ Yes - when you use /mistakes or log errors |
| `dashboard.html` | Visual progress tracker | ❌ Regenerate with /dashboard |
| `COURSE.md` | Course structure & session plans | ❌ Static reference |
| `learner.json` | Your profile | ❌ Static (unless you redo interview) |
| `START_HERE.md` | This file | ❌ Static guide |

---

## 🔄 How Progress Tracking Works

The `/master` skill is the orchestrator. It:

1. **Reads `progress.json`** on every invocation
2. **Finds the first concept** where `status != "mastered"`
3. **Checks which gates** are incomplete (`derive_pass`, `problems_pass`, etc.)
4. **Runs the appropriate skill** to complete the next gate
5. **Updates `progress.json`** when gates pass
6. **Moves to next concept** when all gates for current concept pass

**You can also bypass /master** and use skills directly:
- `/explainer confidence_intervals` - manual concept learning
- `/generate-problems p_values` - manual problem practice

But **progress.json won't auto-update** unless you're using `/master` or manually call skills that write to it.

---

## 🆘 Common Situations

**"I don't remember where I was"**
→ Open `dashboard.html` or type `/master` - it will tell you

**"I want to review a concept I already finished"**
→ `/explainer <concept_name>` or `/generate-problems <concept_name>` - works anytime

**"I made mistakes and want to review them"**
→ `/mistakes` - shows all logged errors with patterns

**"I want to see overall progress"**
→ Open `dashboard.html` or type `/dashboard` to regenerate it

**"I want to jump to a different concept"**
→ You can! Just use `/explainer <other_concept>` or tell /master "skip to X"
→ But be aware of prerequisites - later concepts may assume earlier ones

**"I changed my timeline/goal"**
→ Edit `learner.json` manually or tell me to update it
→ Then regenerate with `/setup-course` (it will ask to merge)

---

## 💡 Pro Tips

1. **Start every session with `/master`** - it's the easiest way to continue
2. **Check `dashboard.html` frequently** - visual progress is motivating
3. **Review mistakes before your final session** - use `/mistakes`
4. **Don't skip concepts** - later ones build on earlier ones
5. **Log mistakes immediately** - don't wait until later, you'll forget context

---

## 🎯 Quick Command Reference

| Command | What It Does |
|---------|-------------|
| `/master` | Auto-continue from last point (RECOMMENDED) |
| `/explainer <concept>` | Learn a specific concept |
| `/generate-problems <concept>` | Practice problems for concept |
| `/oral-exam <concept>` | Test understanding with questions |
| `/derive <concept>` | Prove you can derive from scratch |
| `/mistakes` | Review error patterns |
| `/dashboard` | Regenerate progress visualization |
| Open `dashboard.html` | See current progress visually |

---

## 🎓 Understanding Your Learning Path

Your personalized path is in `COURSE.md` under "Personalized Learning Path". It's broken into phases:

**Phase 1:** Foundation concepts (prerequisites for everything else)
**Phase 2:** Core concepts (main material)
**Phase 3:** Advanced concepts (depth and applications)

`/master` follows this sequence automatically, but you can skip around if needed.

---

**Ready to continue?** Just type `/master` and the system will pick up where you left off!
```

After creating `START_HERE.md`, print a short message to the learner:

```
📚 **Resume Instructions Created!**

Next time you return, just type:
- `/master` - Auto-resume from where you left off (easiest)
- Or open `START_HERE.md` for full instructions

Your progress is saved in `progress.json` and will persist across sessions.
```

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
