# Mastery Engine — Claude Code Skills for Deep Learning

A system of Claude Code skills that turns any course folder into a rigorous self-study environment. Inspired by how MIT students learn: derive everything cold, teach it to skeptics, solve novel problems, survive oral exams.

## Philosophy

You don't "know" something until you can:
1. **Derive it** from scratch without notes
2. **Teach it** to someone who actively fights back
3. **Solve novel problems** you've never seen, at high difficulty
4. **Explain it clearly** under time pressure in an oral exam

All four gates must pass. This system enforces that.

## Quick Start

### 1. Install the skills

Copy the `commands/` folder to your Claude Code commands directory:

```bash
# Option A: Global (available in any project)
cp -r mastery-engine/commands/* ~/.claude/commands/

# Option B: Per-project (only in this course folder)
mkdir -p .claude/commands
cp -r mastery-engine/commands/* .claude/commands/
```

### 2. Set up a course

Put your course materials (PDFs, slides, notes, problem sets) in a folder, then:

```
cd my_course_folder/
# In Claude Code:
/setup-course
```

This reads your materials and generates `COURSE.md`, `progress.json`, and the folder structure.

### 3. Start studying

```
/master
```

The orchestrator tells you exactly what to do next based on your current state.

## Available Skills

Ordered by impact on deep understanding (highest first):

| Skill | Purpose | How It Helps | When to Use |
|-------|---------|--------------|-------------|
| `/explainer` | 9-layer explanation stack for new concepts | Builds genuine understanding from intuition → formalism → edge cases, with wrong-intuition inoculation at every layer. The foundation everything else builds on — you can't test what you never properly learned. | First encounter with any new concept |
| `/socratic` | Strict Socratic questioning — never gives answers | Exposes the exact point where your understanding breaks down. Forces you to articulate reasoning, not just recognize correct answers. The single fastest way to find hidden gaps. | After `/explainer`, to stress-test your new understanding |
| `/derive` | Derive a theorem cold, get a rigor audit | Proves you own the logic, not just the conclusion. Hand-waving is invisible until someone audits it — this is that audit. Separates "I've seen this" from "I can reproduce this." | When you believe you understand a proof or derivation |
| `/skeptic` | Teach concept to a hostile, adversarial audience | If you can convince a skeptic, you truly understand. Exposes jargon-dependence, edge-case blindness, and inability to justify design choices. Feynman technique with teeth. | When you think you can explain something |
| `/generate-problems` | Novel problems at increasing difficulty | Forces transfer, not memorization. Textbook problems test recognition; generated problems test whether you can apply concepts in unfamiliar contexts. The gap between these is where exams live. | When you need practice beyond the textbook |
| `/master` | Meta-orchestrator — tells you exactly what to do next | Eliminates decision fatigue and ensures you're always working on the highest-leverage gap. Enforces the 4-gate rule so nothing slips through. Your study session autopilot. | Start of every study session |
| `/mistakes` | Misconception pattern analysis + resurfacing | Your errors have patterns you can't see. This finds them, predicts where you'll fail next, and forces confrontation before exams. More diagnostic than generic spaced repetition. | Regularly, and always before exams |
| `/oral-exam` | Timed mock oral examination | Tests clarity and completeness under pressure. Knowing something in your own time ≠ knowing it when someone is watching the clock. Closest simulation to quals/defenses/vivas. | When preparing for actual exams or presentations |
| `/implement` | Build the algorithm from scratch in code | You don't understand an algorithm until you can make it work without libraries. Implementation reveals every assumption you glossed over in the math. Reading ≠ building. | For any implementable method in quantitative courses |
| `/anki` | Generate topic-organized Anki cards, export as importable file | Produces atomic, application-focused flashcards organized by deck hierarchy matching your concept tiers. Targets YOUR mistakes, not generic material. The spaced-repetition layer that locks in what the other skills build. | After learning concepts, and `/anki mistakes` when errors accumulate |
| `/dashboard` | Interactive HTML progress visualization + dependency graph | See your entire learning path as an interactive graph — which concepts unlock which, where you are, what's blocking you. Turns abstract progress into a visual map you can navigate. | After `/setup-course`, and after study sessions to see updated state |
| `/setup-course` | Bootstrap the mastery engine for a new course | Reads your raw materials (PDFs, slides, notes), generates the structured concept map, and produces the initial `dashboard.html`. The one-time setup that makes everything else work. | Once, when starting a new course |

## Folder Structure (Per Course)

After `/setup-course`, your course folder looks like:

```
my_course/
├── COURSE.md           ← generated: concepts, dependencies, style guide
├── progress.json       ← tracks your mastery state
├── mistakes.jsonl      ← append-only error log
├── dashboard.html      ← interactive progress graph (open in browser)
├── problems/           ← generated problems + your attempts
├── derivations/        ← your cold derivations
└── [your raw materials: PDFs, slides, notes, etc.]
```

## The 4-Gate Rule

A concept progresses through states:
- **Unstarted** → you haven't engaged with it
- **In progress** → you've started but haven't passed all gates
- **Mastered** → all 4 gates passed (derive, skeptic, problems, oral)

The `/master` skill enforces this and won't let you skip ahead.

## Tips for Maximum Effectiveness

1. **Run `/master` at the start of every session** — it knows your state and optimizes your time
2. **Don't skip `/mistakes` reviews** — recurring errors are the #1 killer of exam performance
3. **Use `/skeptic` for concepts you think you know** — it exposes false confidence
4. **Generate difficulty 5 problems** only after passing difficulty 3-4 — it's discouraging otherwise
5. **Implement from scratch** for any algorithm — reading about gradient descent ≠ understanding it

## Daily Study Rhythm

| Time | Action |
|------|--------|
| Session start | `/master` — what should I do today? |
| New concept | `/explainer` → `/socratic` |
| Think you know it | `/derive` + `/skeptic` |
| Need practice | `/generate-problems` at increasing difficulty |
| Quant method | `/implement` from scratch |
| Weekly | `/mistakes` — find patterns |
| Pre-exam | `/oral-exam` — full simulation |

The system handles "what should I study and in what order." You show up, run `/master`, and do what it says.

## Works With Any Course

This system is course-agnostic. It works for:
- Statistics / Econometrics
- Machine Learning / Deep Learning
- Mathematics (pure or applied)
- Computer Science (algorithms, systems)
- Physics / Engineering
- Economics / Finance
- Any quantitative or proof-heavy field

The `/setup-course` skill adapts to whatever materials you give it.

## Contributing

These skills evolve. If you find a better prompting pattern for any skill, update it and push.
