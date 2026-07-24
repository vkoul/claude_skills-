# Claude Skills

A collection of reusable Claude Code skills for learning, productivity, and workflow automation.

## Available Skill Systems

### [Mastery Engine](./mastery-engine/)
A rigorous self-study system that turns any course folder into an MIT-level learning environment. 10 skills that enforce deep understanding through multi-layered concept explanation, derivation, adversarial teaching, novel problem generation, and mock oral exams.

**Quick start**: Copy `mastery-engine/commands/*` to `~/.claude/commands/`, then `/setup-course` in any folder with course materials.

### [PDF to Interactive Textbook](./pdf_to_interactive_textbook.md)
Convert PDF course materials into interactive HTML textbooks with embedded exercises.

## Installation

```bash
# Clone this repo
git clone https://github.com/vkoul/claude_skills-.git

# Install skills globally (available from any project)
cp -r claude_skills-/mastery-engine/commands/* ~/.claude/commands/
```

## Structure

```
claude_skills-/
├── README.md
├── mastery-engine/
│   ├── README.md           ← full documentation
│   ├── commands/           ← the skill files (copy to ~/.claude/commands/)
│   │   ├── explainer.md
│   │   ├── setup-course.md
│   │   ├── master.md
│   │   ├── socratic.md
│   │   ├── derive.md
│   │   ├── generate-problems.md
│   │   ├── oral-exam.md
│   │   ├── skeptic.md
│   │   ├── mistakes.md
│   │   └── implement.md
│   └── templates/          ← reference templates
│       ├── COURSE_TEMPLATE.md
│       └── progress_template.json
└── pdf_to_interactive_textbook.md
```
