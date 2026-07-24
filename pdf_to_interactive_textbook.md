---
name: interactive-textbook
description: End-to-end recipe for converting any PDF collection into a deployed interactive textbook with nested tooltips, quizzes, visualizations, flashcards, and dark mode. Truly plug-and-play for any subject.
---

# Interactive Textbook — Complete Execution Guide

This skill converts **any collection of PDFs/lecture notes** into a fully interactive, deployed web textbook. It is subject-agnostic — works for A/B Testing, Neuroscience, Economics, Physics, or any course material.

---

## Phase 0: Inputs & Configuration

Before starting, collect these inputs:

| Input | Example | Used For |
|-------|---------|----------|
| PDF files / source text | `lecture1.pdf`, `lecture2.pdf`, ... | Content extraction |
| Course title | "Digital Experimentation Methods" | Title bar, sidebar header |
| GitHub repo name | `Digital-Experimentation-Methods` | Deployment URL, vite base path |
| Short prefix (3-4 chars) | `dem` | localStorage keys: `dem-theme`, `dem-quiz-ch1` |
| Target audience | "First-year CS major with AP stats" | Tooltip depth decisions |
| Known concepts (exclusion list) | "Basic probability, algebra, Python" | Skip tooltips for these |
| Number of chapters | 7 | Route structure |

---

## Phase 1: Project Scaffolding

### 1.1 Create Project

```bash
npm create vite@latest {project-name} -- --template react-ts
cd {project-name}
npm install react-router-dom@7 recharts@2 framer-motion@11 katex@0.16 zustand@5 @floating-ui/react@0.26
npm install -D tailwindcss@3 postcss autoprefixer @types/katex
npx tailwindcss init -p
```

### 1.2 Config Files

**`vite.config.ts`** — Set `base` to your GitHub repo name:
```ts
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],
  base: '/{GITHUB_REPO_NAME}/',
  build: { outDir: 'dist' },
})
```

**`tailwind.config.ts`**:
```ts
import type { Config } from 'tailwindcss'

export default {
  darkMode: 'class',
  content: ['./index.html', './src/**/*.{js,ts,jsx,tsx}'],
  theme: {
    extend: {
      colors: {
        primary: {
          50: '#eff6ff', 100: '#dbeafe', 200: '#bfdbfe',
          500: '#3b82f6', 600: '#2563eb', 700: '#1d4ed8', 900: '#1e3a5f',
        },
        tooltip: { bg: '#1e293b', border: '#475569', link: '#93c5fd' },
      },
      maxWidth: { prose: '72ch' },
    },
  },
  plugins: [],
} satisfies Config
```

**`postcss.config.js`**:
```js
export default {
  plugins: { tailwindcss: {}, autoprefixer: {} },
}
```

**`index.html`**:
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>{COURSE_TITLE}</title>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/katex@0.16.9/dist/katex.min.css" />
  </head>
  <body class="bg-white text-gray-900 antialiased">
    <div id="root"></div>
    <div id="tooltip-root"></div>
    <script type="module" src="/src/main.tsx"></script>
  </body>
</html>
```

**`src/main.tsx`**:
```tsx
import React from 'react'
import ReactDOM from 'react-dom/client'
import { HashRouter } from 'react-router-dom'
import App from './App'
import './index.css'

ReactDOM.createRoot(document.getElementById('root')!).render(
  <React.StrictMode>
    <HashRouter>
      <App />
    </HashRouter>
  </React.StrictMode>,
)
```

**`src/index.css`**:
```css
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer base {
  body { @apply bg-white text-gray-900 dark:bg-gray-900 dark:text-gray-100; }
  h1 { @apply text-2xl font-bold text-gray-900 dark:text-white; }
  h2 { @apply text-xl font-semibold text-gray-800 dark:text-gray-100 mt-8 mb-3; }
  h3 { @apply text-lg font-semibold text-gray-700 dark:text-gray-200 mt-6 mb-2; }
}

.concept-link {
  @apply text-blue-600 dark:text-blue-400 underline decoration-dotted cursor-help;
}
.concept-link--in-chain {
  @apply text-purple-600 dark:text-purple-400 underline decoration-solid;
}
```

**`.github/workflows/deploy.yml`**:
```yaml
name: Deploy to GitHub Pages
on:
  push:
    branches: [main]
permissions:
  contents: read
  pages: write
  id-token: write
concurrency:
  group: pages
  cancel-in-progress: false
jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
      - run: npm install
      - run: npm run build
      - uses: actions/configure-pages@v5
      - uses: actions/upload-pages-artifact@v3
        with:
          path: dist
      - id: deployment
        uses: actions/deploy-pages@v4
```

### 1.3 Core Component Scaffolding

Create these files immediately (they're reused by everything else):

- `src/components/content/MathBlock.tsx` — KaTeX renderer
- `src/components/content/ChapterLayout.tsx` — Article wrapper with title + reading time
- `src/components/content/ExpandableReading.tsx` — Accordion for bonus content
- `src/components/layout/AppShell.tsx` — Sidebar + content area + progress bar + search
- `src/components/layout/SidebarNav.tsx` — Navigation with chapters + resources
- `src/components/tooltip/ConceptLink.tsx` — Inline hoverable term
- `src/components/tooltip/NestedTooltip.tsx` — Recursive positioned tooltip
- `src/components/tooltip/TooltipProvider.tsx` — Context for tooltip chain state
- `src/App.tsx` — Lazy routes + TooltipProvider wrapper

---

## Phase 2: PDF Content Extraction

### 2.1 Reading the PDFs

Read each PDF and extract:
1. **Chapter title** and **subtitle**
2. **Section headings** (H2, H3 level)
3. **Body text** — preserve paragraph breaks
4. **Mathematical expressions** — convert to LaTeX strings
5. **Figures/tables** — note their location and caption
6. **Key terms** — bolded or italicized terms that are domain concepts
7. **Learning objectives** (if present)
8. **Exercises/review questions** (if present)

### 2.2 Content Chunking Strategy

| Signal | Action |
|--------|--------|
| PDF is one file per lecture/chapter | 1 chapter = 1 PDF |
| Single large PDF with clear H1 breaks | Split at each H1 |
| No clear structure | Aim for 2000-4000 words per chapter (10-20 min read) |
| >5000 words in one section | Split into sub-chapters (e.g., `L3a`, `L3b`) |

### 2.3 Figure & Image Handling

**Decision tree:**

1. Is the figure a **static diagram** that's essential to understanding?
   → Place in `public/images/{chapter}/fig-{n}.png`, reference as:
   ```tsx
   <img src={import.meta.env.BASE_URL + 'images/ch1/fig-1.png'} alt="..." className="rounded-lg shadow-sm my-4 max-w-full" />
   ```

2. Is the figure a **chart/graph** that students would learn more from by manipulating?
   → Recreate as an interactive Recharts widget (preferred). Place original as fallback.

3. Is the figure **decorative** or low-value?
   → Omit. Add a note: `{/* Figure X from original: [description] — omitted, see source */}`

4. Can't extract cleanly from PDF?
   → Screenshot the region, save as PNG in `public/images/`.

### 2.4 Mathematical Expression Handling

- Inline math: `<MathBlock tex="E[X] = \mu" />` (renders inline)
- Display math: `<MathBlock tex="\bar{X} \sim N(\mu, \sigma^2/n)" display />`
- Convert all notation from the PDF to LaTeX. Common patterns:
  - Summations: `\sum_{i=1}^{n}`
  - Fractions: `\frac{a}{b}`
  - Greek: `\alpha, \beta, \mu, \sigma`
  - Subscripts/superscripts: `X_{i}`, `\hat{\beta}^{OLS}`

---

## Phase 3: Chapter Generation

### 3.1 Agent Workflow

Fan out **1 agent per chapter**. Each agent receives:
- The extracted text for that chapter
- The list of known concepts (exclusion list)
- The component library reference (ChapterLayout, ConceptLink, MathBlock, ExpandableReading)

Each agent produces:
1. A complete `src/chapters/{ChX}.tsx` file
2. A list of prerequisite concepts it used `<ConceptLink>` for
3. Suggested interactive widget opportunities

### 3.2 Chapter File Template

```tsx
import { ChapterLayout } from '../components/content/ChapterLayout'
import { ConceptLink } from '../components/tooltip/ConceptLink'
import { MathBlock } from '../components/content/MathBlock'
import { ExpandableReading } from '../components/content/ExpandableReading'
import { KeyTakeaways } from '../components/content/KeyTakeaways'
import { DecisionScenario } from '../components/content/DecisionScenario'
import { QuizSection } from '../components/content/QuizSection'

export default function Ch1() {
  return (
    <ChapterLayout title="Chapter Title" subtitle="Subtitle from source">
      {/* === Learning Objectives === */}
      <div className="bg-blue-50 dark:bg-blue-900/20 border-l-4 border-blue-500 p-4 rounded-r-lg">
        <h3 className="font-semibold text-blue-800 dark:text-blue-200">Learning Objectives</h3>
        <ul className="list-disc list-inside text-sm text-blue-700 dark:text-blue-300 mt-2 space-y-1">
          <li>Objective 1</li>
          <li>Objective 2</li>
        </ul>
      </div>

      {/* === Main Content (verbatim with ConceptLinks) === */}
      <h2>Section Heading</h2>
      <p>
        Body text with <ConceptLink conceptId="term-id">technical terms</ConceptLink> linked
        to tooltips for prerequisite concepts the student might not know.
      </p>

      <MathBlock tex="\text{Key equation here}" display />

      <ExpandableReading title="Deep Dive: Why This Matters">
        <p>Additional explanation not in the main flow...</p>
        {/* Optional: interactive widget here */}
      </ExpandableReading>

      {/* === Decision Scenario (1-2 per chapter) === */}
      <DecisionScenario
        scenario="Describe a realistic decision the student would face..."
        choices={[
          { label: "Option A", explanation: "Why this is/isn't recommended...", isRecommended: false },
          { label: "Option B", explanation: "Why this is the best choice...", isRecommended: true },
          { label: "Option C", explanation: "Why this is problematic...", isRecommended: false },
        ]}
      />

      {/* === Key Takeaways (3-4 bullets) === */}
      <KeyTakeaways items={[
        "Most important insight from this chapter",
        "Second key point — actionable",
        "Third point — connects to next chapter",
      ]} />

      {/* === Review Questions === */}
      <h2>Review Questions</h2>
      <ol className="list-decimal list-inside space-y-2">
        <li>Question from the source material...</li>
      </ol>

      {/* === Quiz (always last) === */}
      <QuizSection chapterId="ch1" />
    </ChapterLayout>
  )
}
```

### 3.3 Faithfulness Rules

- **Verbatim text:** The main content paragraphs should be nearly identical to the source. Do not paraphrase or summarize unless the source is poorly written.
- **Additions only via components:** New content goes inside `<ExpandableReading>`, `<DecisionScenario>`, `<KeyTakeaways>`, or as tooltip content — never injected into the main flow.
- **Footnotes/references:** Preserve exactly. Render as superscript with hover or end-of-section notes.
- **Section structure:** Keep the source's heading hierarchy. Map H1→`<h2>`, H2→`<h3>` (since ChapterLayout renders the chapter title as the page H1).

---

## Phase 4: Concept Graph & Tooltips

### 4.1 Concept Identification

After all chapters are generated, collect the union of all `conceptId` values used in `<ConceptLink>` tags. For each concept, determine:

| Field | Description |
|-------|-------------|
| `id` | kebab-case slug (e.g., `central-limit-theorem`) |
| `displayName` | Human-readable (e.g., "Central Limit Theorem") |
| `shortDefinition` | One sentence shown in tooltip header |
| `content` | Full JSX explanation (2-4 paragraphs, may include MathBlock) |
| `prerequisites` | Other concept IDs this depends on |
| `chapterOrigin` | Which chapter introduces this concept |
| `category` | Domain-specific grouping (e.g., `'statistics' \| 'design' \| 'analysis'`) |

### 4.2 Concept Graph DAG

**`src/data/conceptGraph.ts`** template:
```ts
export interface ConceptNode {
  id: string
  prerequisites: string[]
  chapterOrigin: string
  category: string
}

export const conceptGraph: Record<string, ConceptNode> = {
  'concept-slug': {
    id: 'concept-slug',
    prerequisites: ['dependency-1', 'dependency-2'],
    chapterOrigin: 'ch2',
    category: 'statistics',
  },
  // ... all concepts
}
```

**Validation:** The graph MUST be a DAG (no cycles). Include a `isDAG()` validator function.

### 4.3 Concept Definitions

**`src/data/conceptDefinitions.tsx`** template:
```tsx
import { type ReactNode } from 'react'
import { MathBlock } from '../components/content/MathBlock'

export interface ConceptDefinition {
  id: string
  displayName: string
  shortDefinition: string
  content: ReactNode
  prerequisites: string[]
  chapterOrigin: string
  category: string
}

const concepts: Record<string, ConceptDefinition> = {
  'concept-slug': {
    id: 'concept-slug',
    displayName: 'Concept Name',
    shortDefinition: 'One sentence definition.',
    content: (
      <div className="space-y-2">
        <p>Explanation paragraph 1...</p>
        <MathBlock tex="\text{relevant formula}" display />
        <p>Why this matters...</p>
      </div>
    ),
    prerequisites: ['dep-1'],
    chapterOrigin: 'ch2',
    category: 'statistics',
  },
}

export function getConcept(id: string): ConceptDefinition | undefined {
  return concepts[id]
}

export function getAllConcepts(): ConceptDefinition[] {
  return Object.values(concepts)
}
```

### 4.4 Tooltip Iteration Loop

Use a worker pool (4 parallel agents, max 8 concepts each):
1. Take the union of all prerequisite concepts not yet defined
2. Generate definitions (with their own ConceptLinks if needed)
3. Collect new prerequisites discovered
4. Repeat until no new unknown concepts remain
5. Validate DAG has no cycles

---

## Phase 5: Interactive Enhancements

### 5.1 Quizzes

**`src/data/quizData.ts`** — 5 MCQs per chapter:
```ts
export interface QuizQuestion {
  id: string
  question: string
  options: { id: string; text: string }[]
  correctId: string
  explanation: string
}

export const quizData: Record<string, QuizQuestion[]> = {
  ch1: [
    {
      id: 'ch1-q1',
      question: 'Clear, specific question testing understanding (not recall)?',
      options: [
        { id: 'a', text: 'Plausible distractor 1' },
        { id: 'b', text: 'Correct answer' },
        { id: 'c', text: 'Plausible distractor 2' },
        { id: 'd', text: 'Plausible distractor 3' },
      ],
      correctId: 'b',
      explanation: '1-2 sentences explaining WHY the correct answer is correct and a common misconception.',
    },
  ],
}
```

**Quiz quality guidelines:**
- Test **understanding**, not memorization (no "which of these is the definition of X")
- Distractors should be **plausible** — common misconceptions, not obviously wrong
- Explanation should address **why wrong answers are wrong**, not just restate the correct one
- Mix difficulty: 2 easy, 2 medium, 1 hard per chapter

### 5.2 Flashcards

**`src/data/flashcardData.ts`** — 5 per chapter:
```ts
export interface Flashcard {
  id: string
  front: string
  back: string
  chapter: string
}
```

**Quality guidelines:**
- **Front:** One specific question or prompt (not "What is X?" — too vague)
- **Back:** Concise answer (1-3 sentences max)
- **Atomic:** One concept per card
- Good front: "Why does increasing sample size reduce the standard error?"
- Bad front: "Define standard error"

### 5.3 Decision Scenarios

**Quality guidelines:**
- Present a **realistic dilemma** professionals actually face
- 3 choices: one recommended, two common mistakes
- Explanations for wrong answers should be **empathetic** (explain why someone might choose this)
- At least 1 per chapter, placed at the content point where the decision is relevant

### 5.4 Key Takeaways

- 3-4 bullets per chapter
- **Actionable and specific** (not "Statistics are important")
- Should help a student who only reads takeaways still get the core message
- Connect to the next chapter when possible ("This sets up...")

### 5.5 Interactive Widgets

**When to create a new widget:**
- A concept involves a **parameter trade-off** (e.g., alpha vs power) → slider widget
- A concept involves a **process** that unfolds over iterations (e.g., CLT) → step-through widget
- A concept involves a **visual relationship** (e.g., power curve) → chart widget

**Widget design template:**
```tsx
import { useState, useMemo } from 'react'
import { LineChart, Line, XAxis, YAxis, ResponsiveContainer, CartesianGrid } from 'recharts'

export function WidgetName() {
  const [param, setParam] = useState(defaultValue)

  const data = useMemo(() => {
    // Compute visualization data from param
    return points
  }, [param])

  return (
    <div className="bg-white dark:bg-gray-800 border border-gray-200 dark:border-gray-700 rounded-lg p-5 shadow-sm my-6">
      <h3 className="font-semibold text-gray-800 dark:text-gray-200 mb-4">Widget Title</h3>
      <p className="text-sm text-gray-600 dark:text-gray-400 mb-4">
        Instruction: what to do and what to observe.
      </p>

      {/* Controls */}
      <div className="flex flex-wrap gap-4 mb-4">
        <label className="text-sm text-gray-700 dark:text-gray-300">
          Parameter:
          <input type="range" min={0} max={100} value={param}
            onChange={(e) => setParam(Number(e.target.value))}
            className="ml-2 w-32 align-middle" />
          <span className="ml-2 font-mono">{param}</span>
        </label>
      </div>

      {/* Chart */}
      <ResponsiveContainer width="100%" height={200}>
        <LineChart data={data}>
          <CartesianGrid strokeDasharray="3 3" stroke="#374151" opacity={0.3} />
          <XAxis dataKey="x" tick={{ fontSize: 10 }} />
          <YAxis tick={{ fontSize: 10 }} />
          <Line type="monotone" dataKey="y" stroke="#3b82f6" strokeWidth={2} dot={false} />
        </LineChart>
      </ResponsiveContainer>
    </div>
  )
}
```

### 5.6 Case Studies

**`src/data/caseStudyData.ts`**:
```ts
export interface CaseStudy {
  id: string
  title: string
  company: string
  year: string
  summary: string
  violation: string          // What went wrong or was notable
  violationTag: string       // For filtering: 'peeking' | 'interference' | 'ethics' | etc.
  lessonsLearned: string[]
  relatedChapter: string     // Route path
}
```

Find 6-8 real-world cases relevant to the subject. Prefer:
- Famous failures that teach a lesson
- Surprising successes that illustrate a principle
- Ethical dilemmas that provoke discussion

### 5.7 Formulas

**`src/data/formulaData.ts`**:
```ts
export interface Formula {
  id: string
  name: string
  tex: string               // LaTeX string
  description: string       // Plain English: when/why to use this
  chapter: string
  category: string          // For filter tabs
}
```

Include every equation from the source material plus derived/related formulas.

---

## Phase 6: Resource Pages

Create 5 resource pages, all lazy-loaded:

| Page | Route | Component | Data Source |
|------|-------|-----------|-------------|
| Glossary | `/glossary` | `src/pages/Glossary.tsx` | `conceptDefinitions.tsx` → `getAllConcepts()` |
| Formula Sheet | `/formulas` | `src/pages/Formulas.tsx` | `src/data/formulaData.ts` |
| Flashcards | `/flashcards` | `src/pages/Flashcards.tsx` | `src/data/flashcardData.ts` |
| Case Studies | `/case-studies` | `src/pages/CaseStudies.tsx` | `src/data/caseStudyData.ts` |
| Concept Map | `/map` | `src/pages/ConceptMap.tsx` | `src/data/conceptGraph.ts` |

All pages support: dark mode, search/filter, responsive layout.

---

## Phase 7: Navigation & UX

### Routing Setup (`src/App.tsx`)

```tsx
import { Routes, Route, Navigate } from 'react-router-dom'
import { lazy, Suspense } from 'react'
import { TooltipProvider } from './components/tooltip/TooltipProvider'
import { AppShell } from './components/layout/AppShell'

const Ch1 = lazy(() => import('./chapters/Ch1'))
// ... all chapters
const Glossary = lazy(() => import('./pages/Glossary'))
// ... all resource pages

function App() {
  return (
    <TooltipProvider>
      <AppShell>
        <Suspense fallback={<div className="p-8 text-gray-400">Loading...</div>}>
          <Routes>
            <Route path="/" element={<Navigate to="/ch1" replace />} />
            <Route path="/ch1" element={<Ch1 />} />
            {/* ... all routes */}
          </Routes>
        </Suspense>
      </AppShell>
    </TooltipProvider>
  )
}
export default App
```

### Sidebar Structure

```tsx
const chapters = [
  { path: '/ch1', label: 'Ch 1: Chapter Title' },
  // ... all chapters
]

const resources = [
  { path: '/glossary', label: 'Glossary' },
  { path: '/formulas', label: 'Formula Sheet' },
  { path: '/flashcards', label: 'Flashcards' },
  { path: '/case-studies', label: 'Case Studies' },
  { path: '/map', label: 'Concept Map' },
]
```

### Hooks to Include

- `src/hooks/useDarkMode.ts` — localStorage (`{PREFIX}-theme`) + `classList.toggle('dark')` + system preference fallback
- `src/hooks/useReadingProgress.ts` — `scrollY / (docHeight - windowHeight) * 100` with rAF
- `src/hooks/useKeyboardNav.ts` — Left/Right arrow between chapters, skip when in input/textarea

### Search Modal

- Trigger: Cmd+K (Mac) or Ctrl+K (Windows)
- Data: `src/data/searchIndex.ts` — flat array of `{ title, path, type }` entries
- Covers: chapter titles, section headings, concept names, formula names
- Navigation: arrow keys + Enter to navigate, Esc to close

---

## Nested Tooltips Specification

Nested tooltips explain concepts with long dependency chains, with a similar UX to Paradox Interactive's grand strategy games.

### UX Flow

1. **Hover** a `<ConceptLink>` → 300ms timer starts
2. Timer fires → tooltip renders (positioned away from source, never occluding it)
3. Tooltip starts **non-interactive** (mouse passes through)
4. **Lock conditions:**
   - Keep cursor still over source for additional delay → tooltip becomes interactive (subtle fill-bar progress affordance on tooltip edge)
   - Moving cursor toward tooltip → "safe corridor" (triangle between cursor and tooltip bounds) prevents dismissal
5. Locked tooltip: border change indicates interactivity
6. Inside locked tooltip, `<ConceptLink>`s are hoverable → spawns child tooltip (recursively)
7. **Depth cap:** 4-5 simultaneous levels; beyond that, oldest ancestor collapses
8. **Circular references:** Disallowed; display differently so user knows
9. **Dismissal:**
   - Cursor leaves entire chain → 200-300ms grace timer → dismiss all
   - Cursor returns to ancestor → prune descendants
   - Esc → close entire chain

### Implementation

- `TooltipProvider` manages chain state (array of open tooltip IDs + positions)
- `NestedTooltip` renders via React portal into `#tooltip-root`
- `@floating-ui/react` handles positioning with flip/shift middleware
- Each tooltip receives `parentTooltipId` for chain tracking

---

## Adaptation Checklist (For Any New Subject)

When applying this skill to a new PDF collection:

1. **Choose a prefix:** Short abbreviation for localStorage keys (e.g., `micro` for Microeconomics)
2. **Choose chapter slugs:** Keep them short (`/ch1`, `/l1`, `/week1`, etc.)
3. **Set vite base:** Match your GitHub repo name in `vite.config.ts`
4. **Define categories:** What are the 3-5 conceptual groupings? (e.g., for Economics: `'micro' | 'macro' | 'policy' | 'methods'`)
5. **Identify widget opportunities:** What parameters do students manipulate in this subject?
   - Economics: supply/demand curves, multiplier effects, IS-LM equilibrium
   - Physics: projectile motion, circuit builders, wave interference
   - Biology: population dynamics, enzyme kinetics, gene expression
   - Statistics: distributions, hypothesis testing, regression
6. **Find case studies:** Every field has famous failures/successes. Include 6-8.
7. **Calibrate tooltip depth:** Use the exclusion list to avoid over-explaining basics.
8. **Color scheme:** Primary-900 for sidebar, primary-500 for accents. Can customize per subject.

---

## Persistence Conventions

All localStorage keys use the chosen prefix:

| Key | Value | Purpose |
|-----|-------|---------|
| `{PREFIX}-theme` | `"dark"` or `"light"` | Dark mode preference |
| `{PREFIX}-quiz-{chapterId}` | JSON: `{ questionId: selectedOptionId }` | Quiz answer persistence |
| `{PREFIX}-flashcard-progress` | JSON: `{ cardId: "got-it" \| "review" }` | Flashcard tracking |

---

## Execution Order (Sprint Plan)

For a model executing this skill, follow this order:

### Sprint 1: Foundation (must be sequential)
1. Create project, install deps, write all config files
2. Create `MathBlock`, `ChapterLayout`, `ExpandableReading`, `AppShell`, `SidebarNav`
3. Create tooltip system (`ConceptLink`, `NestedTooltip`, `TooltipProvider`)
4. Verify: `npx tsc --noEmit` passes, `npm run dev` shows empty shell

### Sprint 2: Chapter Content (parallel — 1 agent per chapter)
1. Extract content from each PDF
2. Generate chapter `.tsx` files with ConceptLinks
3. Collect all prerequisite concepts
4. Verify: all chapters render, ConceptLinks display (tooltips can be stubs)

### Sprint 3: Concept Graph (iterative)
1. Define all concepts from Sprint 2's collected prerequisites
2. Build `conceptGraph.ts` + `conceptDefinitions.tsx`
3. Iterate: generate tooltip content → discover new prerequisites → repeat
4. Validate DAG (no cycles)
5. Verify: hovering ConceptLinks shows real tooltip content

### Sprint 4: Interactive Features (parallel)
1. `useDarkMode` + `DarkModeToggle` + dark mode classes
2. `useReadingProgress` + `ReadingProgressBar`
3. `KeyTakeaways` component + add to all chapters
4. `DecisionScenario` component + add 1-2 per chapter
5. `QuizSection` + `QuizQuestion` + `quizData.ts` + add to all chapters
6. `SearchModal` + `searchIndex.ts` + `useKeyboardNav`
7. Any subject-specific widgets (Recharts-based)
8. Verify: toggle dark mode, scroll progress, answer quizzes, Cmd+K search

### Sprint 5: Resource Pages (parallel)
1. `Glossary.tsx` + route
2. `Formulas.tsx` + `formulaData.ts` + route
3. `Flashcards.tsx` + `FlashCard.tsx` + `flashcardData.ts` + route
4. `CaseStudies.tsx` + `caseStudyData.ts` + route
5. `ConceptMap.tsx` + route
6. Update `SidebarNav` with Resources section
7. Update `App.tsx` with all lazy routes
8. Verify: navigate to each page, filter/search works, dark mode consistent

### Sprint 6: Polish & Deploy
1. Run `npx tsc --noEmit` — fix any type errors
2. Run `npm run build` — verify production build succeeds
3. Push to GitHub → Actions deploys to Pages
4. Verify live URL works with HashRouter

---

## Quality Checklist (Before Declaring Done)

- [ ] All chapters render without errors
- [ ] Dark mode works on every page (no white-on-white or black-on-black)
- [ ] All ConceptLinks show tooltips on hover
- [ ] Nested tooltips work 2-3 levels deep
- [ ] Quizzes give green/red feedback + explanation
- [ ] Decision scenarios reveal all options after one click
- [ ] Flashcards flip with animation
- [ ] Search modal opens with Cmd+K, navigates with arrows
- [ ] Left/Right arrows navigate between chapters
- [ ] Progress bar tracks scroll position
- [ ] All resource pages load and filter correctly
- [ ] `npm run build` succeeds with no errors
- [ ] GitHub Pages deploy works (HashRouter routes resolve)
- [ ] Math renders correctly (KaTeX)
- [ ] Mobile viewport is usable (sidebar collapses)

---

## Concepts Exclusion List

(Fill this in per project — concepts the student already knows, so no tooltips needed)

Concepts that the student already understands:
- <fill in with your knowledge level, e.g. "AP Biology only", "undergrad-level understanding of math">

---

## Credits

Creator: [Sophia Xu](https://x.com/thesophiaxu)
