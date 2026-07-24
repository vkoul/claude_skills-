# Mastery Engine — Claude Code Skills for Deep Learning

A system of 12 Claude Code skills that turns any course folder into a rigorous self-study environment. Inspired by how MIT students learn: derive everything cold, teach it to skeptics, solve novel problems, survive oral exams, and build the machinery from scratch.

## Philosophy

You don't "know" something until you can:
1. **Explain it** in 9 layers — from bar-napkin intuition to formal proof
2. **Derive it** from scratch without notes
3. **Teach it** to someone who actively fights back
4. **Solve novel problems** you've never seen, at high difficulty
5. **Explain it clearly** under time pressure in an oral exam
6. **Build it** from scratch in code (for quantitative methods)

The system enforces this. No skipping. No self-deception.

---

## Quick Start (5 minutes)

### Step 1: Install the skills

```bash
# Clone this repo
git clone https://github.com/vkoul/claude_skills-.git

# Install globally (available from any project folder)
cp -r claude_skills-/mastery-engine/commands/* ~/.claude/commands/

# On Windows (PowerShell):
Copy-Item -Recurse claude_skills-\mastery-engine\commands\* $env:USERPROFILE\.claude\commands\
```

### Step 2: Set up a course

Create a folder, dump your raw materials in (PDFs, slides, notes, problem sets — anything), then:

```
cd my_course_folder/
# In Claude Code:
/setup-course
```

This reads everything you dumped in and generates:
- `COURSE.md` — structured concept map with dependencies
- `progress.json` — your mastery state (all concepts start at "unstarted")
- `dashboard.html` — interactive visual graph (open in browser immediately)
- `mistakes.jsonl` — empty, ready to accumulate your errors
- `problems/` and `derivations/` directories

### Step 3: Start studying

```
/master
```

It tells you exactly what to do. Follow it.

---

## Complete Skill Reference

### `/explainer` — The 9-Layer Explanation Stack

**What it does:** Generates a deep, multi-layered explanation of any concept. Not a textbook definition — a complete mental model built from multiple angles.

**The 9 layers:**
1. **Naive intuition** — one sentence, no jargon, what you'd tell a friend at a bar
2. **Why it exists** — what problem was unsolved, what breaks without it
3. **Visual/geometric** — diagram or interactive visualization
4. **Mathematical/formal** — notation and derivation (only after intuition is set)
5. **Concrete worked example** — real numbers, every step shown
6. **Edge cases and failure modes** — where your intuition misleads you
7. **Connection to what you already know** — explicit links to prior concepts
8. **Minimal generative test** — "what if I change this assumption?"
9. **Compressed form** — one sentence + one equation + one picture (your flashcard)

**Critical feature:** Every layer includes a `WRONG INTUITION` callout — the plausible-but-incorrect model most people default to. Naming it directly inoculates against it.

**Best usage:**
```
/explainer "difference-in-differences"
/explainer "CUPED variance reduction"
```
Use on first encounter with any concept. Don't skip to problem-solving without this foundation.

---

### `/socratic` — Strict Socratic Questioning

**What it does:** Enters a mode where Claude NEVER gives answers — only asks the next probing question. Like a strict TA who forces you to think.

**How it works:**
- Asks one question at a time, escalating in precision
- If you hand-wave ("it follows that..."), it calls it out
- If you're stuck for 3+ attempts, asks a scaffolding question (still not the answer)
- If you give a correct answer, asks "Why? How do you know?"
- Logs exposed gaps to `mistakes.jsonl`

**Best usage:**
```
/socratic "power analysis"
```
Use immediately after `/explainer` to stress-test your new understanding. The gap between "I read it" and "I can reason about it under pressure" is exactly what this exposes.

---

### `/derive` — Derivation-from-Scratch Checker

**What it does:** You write a proof or derivation cold (no notes). It performs a line-by-line rigor audit.

**What it checks:**
- Hand-waving ("clearly", "it follows", "it's obvious")
- Skipped steps and logical jumps
- Unproven assumptions used silently
- Notation inconsistencies
- Circular reasoning
- Edge cases ignored

**Grading:** A (publication-ready) through F (fundamental misunderstanding). Grade B+ required to pass the gate.

**Best usage:**
```
/derive "OLS estimator is BLUE"
/derive "central limit theorem"
```
Use when you THINK you understand a proof. The audit reveals whether you actually do or just recognize the shape.

---

### `/skeptic` — Explain-to-a-Skeptic Loop

**What it does:** You teach the concept. Claude plays a confused, adversarial student who refuses to accept hand-waving and actively fights back.

**Escalation levels:**
1. "I don't understand this part" (forces clarity)
2. "Why this approach and not [alternative]?" (forces justification)
3. "Here's a case where your explanation breaks" (forces edge-case thinking)
4. "Prove it from first principles" (forces rigor)

**Best usage:**
```
/skeptic "instrumental variables"
```
Use when you think you can explain something. The Feynman technique only works if the audience fights back — otherwise you pattern-match your way through without noticing gaps.

---

### `/generate-problems` — Adversarial Problem Generator

**What it does:** Generates novel problems in the course's style at specified difficulty. NOT from the textbook — generated fresh to force transfer.

**Difficulty levels:**
- 1-2: Direct application, clean setup
- 3: Requires choosing the right approach, mild ambiguity
- 4: Combines multiple concepts, non-obvious first step
- 5: Research-adjacent, extends course material

**Adversarial mode (difficulty 5+):** Reads your `mistakes.jsonl` and generates problems specifically designed to trigger your known error patterns.

**Best usage:**
```
/generate-problems "hypothesis testing" 3 5    # concept, difficulty, count
/generate-problems "CUPED" 5 3                 # hard problems targeting transfer
```
Start at difficulty 3. Only go to 5 after consistently passing 3-4. Skipping ahead is discouraging and doesn't accelerate learning.

---

### `/master` — The Meta-Orchestrator

**What it does:** Reads your full state and generates today's study plan. Enforces the 4-gate rule.

**The 4-Gate Rule** — a concept is mastered ONLY when ALL four pass:
1. Derive it cold ✓
2. Teach it to a skeptic ✓
3. Solve novel problems at difficulty 4+ ✓
4. Explain it clearly in an oral exam ✓

**Priority logic:**
1. Fix recurring mistakes (they compound)
2. Close almost-done concepts (3/4 gates)
3. Unblock prerequisites
4. Introduce new concepts (only when current tier is solid)
5. Review mastered concepts (>2 weeks since last test)

**Best usage:**
```
/master
```
Run at the START of every study session. Don't decide yourself — the orchestrator has full state and optimizes your time better than you will.

---

### `/mistakes` — Misconception Tracker

**What it does:** Analyzes your accumulated errors, finds patterns, predicts future failures, and resurfaces dangers.

**Three modes:**
- `/mistakes` — full pattern analysis (clusters, recurring errors, predictions)
- `/mistakes review` — resurface top 3 most dangerous unresolved mistakes
- `/mistakes add` — manually log a new mistake

**Why it matters:** Your errors have structure you can't see from inside. This skill finds "you always confuse X with Y" and "you'll make this exact error again on concept Z because it has the same structure."

**Best usage:**
```
/mistakes              # weekly analysis
/mistakes review       # before exams — confront your patterns
/mistakes add          # after a study session where you got something wrong
```

---

### `/oral-exam` — Mock Oral Examination

**What it does:** Simulates a timed, closed-book oral exam. Grades correctness (40%), clarity (30%), and completeness (30%).

**Format:** 4-6 questions in 15-20 minutes, with follow-ups. Includes:
- Broad conceptual questions → specific drill-downs
- "What if?" variations
- "Explain to a non-expert" questions
- Questions targeting concepts with incomplete mastery

**Best usage:**
```
/oral-exam          # default 15 minutes
/oral-exam 20       # longer session
```
Use when preparing for actual exams, presentations, or job interviews. The time pressure reveals what you actually have vs. what you can reconstruct slowly.

---

### `/implement` — Build from Scratch

**What it does:** Forces you to implement a core algorithm from scratch in code. No sklearn, no statsmodels for the core logic.

**What it checks:**
- Correctness (does it produce right output?)
- Understanding (does the code show you grok the math?)
- Edge cases handled
- Comparison against library implementation

**Best usage:**
```
/implement "bootstrap confidence interval"
/implement "CUPED estimator"
/implement "propensity score matching"
```
Use for any algorithm/method in a quantitative course. You don't understand gradient descent until you can make it converge without pytorch.

---

### `/anki` — Generate Importable Flashcards

**What it does:** Generates atomic, well-structured Anki flashcards organized by topic hierarchy, exportable as a single file you import into Anki.

**Card types generated:**
- Definition (intuitive, not textbook jargon)
- Equation (+ "what does each term mean" companion)
- Assumption (what does the method require?)
- Comparison (how does X differ from Y?)
- Edge case (when does this fail?)
- Application (given scenario, which method?)
- Wrong intuition (common misconception + why it's wrong)
- Connection (how does X relate to Y?)

**Three modes:**
- `/anki` — full course or choose concepts
- `/anki [concept]` — single concept, appends to existing file
- `/anki mistakes` — generates cards targeting YOUR logged errors (highest value cards)

**Export:** Tab-separated `anki_export.txt` with proper headers. Import directly into Anki → subdecks auto-created matching your course hierarchy (`CourseName::Tier2::ConceptX`).

**Best usage:**
```
/anki "power analysis"       # after /explainer teaches you the concept
/anki mistakes               # after /mistakes surfaces error patterns
```
The Layer 9 compressed forms from `/explainer` are perfect Anki card seeds.

---

### `/dashboard` — Interactive Progress Visualization

**What it does:** Generates a self-contained `dashboard.html` with your progress and concept dependency graph. Open in any browser.

**What you see:**
- Progress bar (% mastered, in-progress, unstarted)
- Interactive DAG: concepts as nodes, prerequisites as edges
- Color coding: green (mastered), amber (in progress), grey (unstarted), locked (blocked)
- Red borders on concepts with recurring mistakes
- Click any node → 4-gate status, mistake count, recommended action
- Dark/light mode toggle

**Best usage:**
```
/dashboard
```
Regenerate after every study session. The graph updates to show your movement. Most valuable for seeing WHY you're stuck — usually a prerequisite isn't solid.

---

### `/setup-course` — Course Bootstrap

**What it does:** Reads raw course materials and generates everything the other skills need.

**Input:** Any combination of PDFs, slides, lecture notes, problem sets, syllabi, textbook chapters — thrown into a folder. No organization needed.

**Output:**
- `COURSE.md` — concept list with tiers, dependencies, key theorems, problem style guide
- `progress.json` — initialized mastery state
- `dashboard.html` — visual concept graph (ready to open)
- `mistakes.jsonl` — empty error log
- `problems/` and `derivations/` directories

**Best usage:**
```
/setup-course
```
Run once when starting a new course. Run again if you add new materials (e.g., week 5 slides arrive) — it merges.

---

## Best Usage Patterns

### The Daily Rhythm

| When | What to Do | Why |
|------|-----------|-----|
| Session start | `/master` | It knows your state — let it decide |
| New concept | `/explainer` → `/anki` → `/socratic` | Learn → lock in → stress-test |
| Think you own it | `/derive` + `/skeptic` | Prove it — to yourself and a hostile audience |
| Need practice | `/generate-problems` (difficulty 3→4→5) | Transfer, not recognition |
| Quantitative method | `/implement` | You don't own what you can't build |
| Weekly | `/mistakes` + `/anki mistakes` | Find patterns, create targeted cards |
| Pre-exam | `/oral-exam` + `/mistakes review` | Simulate pressure, confront weaknesses |
| End of session | `/dashboard` | See where you moved, plan next time |

### The Concept Mastery Flow

```
                    ┌─────────────┐
                    │ /explainer  │ ← First contact. Build the 9-layer model.
                    └──────┬──────┘
                           │
                    ┌──────▼──────┐
                    │   /anki     │ ← Lock in the compressed form.
                    └──────┬──────┘
                           │
                    ┌──────▼──────┐
                    │  /socratic  │ ← Expose gaps in understanding.
                    └──────┬──────┘
                           │
              ┌────────────┼────────────┐
              │            │            │
       ┌──────▼───┐ ┌─────▼─────┐ ┌───▼────────┐
       │ /derive  │ │ /skeptic  │ │ /implement │  ← The 4 gates
       └──────┬───┘ └─────┬─────┘ └───┬────────┘    (pass all to master)
              │            │            │
              └────────────┼────────────┘
                           │
                  ┌────────▼────────┐
                  │/generate-problems│ ← Novel problems at difficulty 4+
                  └────────┬────────┘
                           │
                    ┌──────▼──────┐
                    │  /oral-exam │ ← Final gate: explain under pressure
                    └──────┬──────┘
                           │
                      ┌────▼────┐
                      │MASTERED │
                      └─────────┘
```

### Example: Learning MIT DASS (DEDP MicroMasters)

```bash
# 1. Gather materials
mkdir mit-dass && cd mit-dass
# Download lecture slides, problem sets, textbook PDFs into this folder

# 2. Bootstrap (in Claude Code)
/setup-course
# → Generates COURSE.md with concepts: RCTs, hypothesis testing, regression,
#   IV, DID, RDD, power analysis, etc. Opens dashboard.html.

# 3. Day 1: Start with foundations
/master
# → "Start with: Randomized Controlled Trials (Tier 1, no prereqs)"
/explainer "randomized controlled trials"
# → Full 9-layer breakdown
/anki "randomized controlled trials"
# → 12 flashcards generated, exported
/socratic "randomized controlled trials"
# → 15 min of probing questions

# 4. Day 3: You think you understand hypothesis testing
/derive "relationship between confidence interval and p-value"
# → Rigor audit: "Step 3 — you assumed normality but didn't state why"
/skeptic "hypothesis testing"
# → "Why not just use a bigger sample every time?"

# 5. Day 7: Weekly review
/mistakes
# → "Pattern: you confuse Type I vs Type II error direction (3 occurrences)"
/anki mistakes
# → Generates 5 cards specifically targeting this confusion
/dashboard
# → See 4 concepts mastered, 2 in progress, 8 unstarted

# 6. Day 14: Quant method
/implement "difference-in-differences estimator"
# → Build it from scratch in Python. Compare with statsmodels.

# 7. Pre-exam
/oral-exam 20
# → 20-minute timed exam covering all concepts you claim to know
/mistakes review
# → Confronts your top 3 unresolved error patterns
```

### Anti-Patterns (What NOT to Do)

| Don't | Do Instead |
|-------|-----------|
| Skip `/explainer` and jump to problems | The 9 layers build the foundation that makes everything else stick |
| Do `/oral-exam` on concepts you haven't derived | Pass the derive + skeptic gates first — oral tests recall, not learning |
| Generate difficulty 5 problems immediately | Start at 3, build to 4, then 5. Early failure is discouraging, not educational |
| Ignore `/mistakes` output | Your error patterns are invisible to you. The tracker sees what you can't |
| Skip `/implement` for "theoretical" courses | Even "theoretical" stats has implementable methods. Building reveals gaps reading hides |
| Study without running `/master` first | You'll default to what's comfortable, not what's highest-leverage |
| Run `/anki` without `/explainer` first | Cards without understanding = memorization without comprehension |

---

## Folder Structure (Per Course)

After `/setup-course`, your course folder looks like:

```
my_course/
├── COURSE.md           ← generated: concepts, dependencies, style guide
├── progress.json       ← tracks your mastery state per concept
├── mistakes.jsonl      ← append-only error log (grows over weeks)
├── dashboard.html      ← interactive progress graph (open in browser)
├── anki_export.txt     ← importable Anki cards (grows as you /anki)
├── problems/           ← generated problems + your attempts
├── derivations/        ← your cold derivations for audit
└── [your raw materials: PDFs, slides, notes, problem sets, etc.]
```

## The 4-Gate Rule

A concept progresses through states:

```
UNSTARTED → IN PROGRESS → MASTERED
                ↑
        (requires ALL 4 gates)
```

| Gate | Skill | What It Proves |
|------|-------|---------------|
| Derive | `/derive` | You own the logic, not just the conclusion |
| Teach | `/skeptic` | You can explain it to someone who fights back |
| Solve | `/generate-problems` (difficulty 4+) | You can transfer to novel contexts |
| Oral | `/oral-exam` | You can recall and explain under time pressure |

The `/master` orchestrator enforces this. It won't let you mark a concept as mastered until all four pass.

---

## Works With Any Course

This system is course-agnostic. Tested with:
- Statistics / Econometrics / Causal Inference
- Machine Learning / Deep Learning
- Mathematics (pure or applied)
- Computer Science (algorithms, systems, theory)
- Physics / Engineering
- Economics / Finance / Quantitative Methods
- Any field with concepts that can be derived, tested, and implemented

The `/setup-course` skill adapts to whatever materials you give it. Messy slides, dense PDFs, handwritten notes — it structures the chaos.

---

## Installation on a New Machine

```bash
git clone https://github.com/vkoul/claude_skills-.git
cp -r claude_skills-/mastery-engine/commands/* ~/.claude/commands/
```

That's it. Now `/master`, `/explainer`, `/socratic`, etc. work from any folder.

Your per-course data (`progress.json`, `mistakes.jsonl`) stays in the course folder. The skills are global.

---

## Contributing

These skills sharpen with use. If you find a better prompting pattern, a missing card type, or a more effective questioning strategy — update the skill file and push. The system evolves.
