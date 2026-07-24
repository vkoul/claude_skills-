# Setup Course

Bootstrap the mastery engine for a new course. Reads raw materials in the current folder and generates the structured files all other skills depend on.

## Behavior

1. **Scan the current directory** for course materials: PDFs, slides, extracted text files, markdown notes, problem sets, syllabi, textbooks. Read as many as needed to understand the full scope.

2. **Generate `COURSE.md`** with this structure:
   ```markdown
   # [Course Title]

   ## Overview
   [1-2 sentence description of the course]

   ## Source Materials
   - [list of files found and what they cover]

   ## Concepts (ordered by prerequisite dependency)

   ### Tier 1: Foundations
   - **Concept Name** — one-line description
     - Prerequisites: none
     - Key theorems/algorithms: [list]
     - Difficulty: 1-5

   ### Tier 2: Core
   [...]

   ### Tier 3: Advanced
   [...]

   ## Concept Dependency Graph
   [Mermaid diagram or text representation showing which concepts depend on which]

   ## Problem Style Guide
   [Description of how problems in this course are styled — quantitative vs. conceptual, notation conventions, typical formats — so /generate-problems can mimic the style]
   ```

3. **Generate `progress.json`**:
   ```json
   {
     "course": "Course Title",
     "setup_date": "YYYY-MM-DD",
     "concepts": {
       "concept_name": {
         "status": "unstarted",
         "derive_pass": false,
         "skeptic_pass": false,
         "problems_pass": false,
         "oral_pass": false,
         "last_reviewed": null,
         "mistakes_count": 0
       }
     }
   }
   ```

4. **Create empty `mistakes.jsonl`** (if not exists)

5. **Create `problems/` directory** (if not exists)

6. **Create `derivations/` directory** (if not exists)

7. **Print a summary**: how many concepts found, the dependency tiers, and suggested first action.

## Rules
- If `COURSE.md` already exists, ask whether to overwrite or merge (new materials added).
- Extract concepts at the granularity where each one is independently testable — not too broad ("statistics") nor too atomic ("formula 3.2").
- Infer prerequisites from the material order and explicit references.
- The Problem Style Guide should capture: notation, typical question formats, whether problems are computational/conceptual/proof-based, and difficulty scaling patterns.

## Usage
```
/setup-course
```
Run this in any folder containing course materials. That's it.
