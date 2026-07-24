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

| Skill | Purpose | When to Use |
|-------|---------|-------------|
| `/setup-course` | Bootstrap mastery engine for a new course | Once, at the start |
| `/master` | Get today's study prescription | Start of every session |
| `/socratic` | Strict Socratic questioning (no answers given) | When learning new concepts |
| `/derive` | Derive a theorem cold, get a rigor audit | When you think you understand |
| `/generate-problems` | Fresh problems at increasing difficulty | When you need practice |
| `/oral-exam` | Timed mock oral examination | When preparing for exams |
| `/skeptic` | Teach to a hostile, adversarial audience | When you think you can explain it |
| `/mistakes` | Analyze error patterns, resurface dangers | Regularly, and before exams |
| `/implement` | Build the algorithm from scratch in code | For quantitative courses |

## Folder Structure (Per Course)

After `/setup-course`, your course folder looks like:

```
my_course/
├── COURSE.md           ← generated: concepts, dependencies, style guide
├── progress.json       ← tracks your mastery state
├── mistakes.jsonl      ← append-only error log
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
