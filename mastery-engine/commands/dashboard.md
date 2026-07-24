# Dashboard — Interactive Progress Visualization

Generate a self-contained HTML dashboard that visualizes your mastery progress and concept dependency graph. Open it in any browser — no server needed.

## Behavior

1. Read `progress.json`, `COURSE.md`, and `mistakes.jsonl` from the current directory.
2. Parse the concept dependency structure from `COURSE.md`.
3. Generate `dashboard.html` with all data embedded inline.
4. Tell the user to open it in their browser.

## What to Generate

Create a single `dashboard.html` file with this structure:

### Required Libraries (via CDN, with fallback note)
- D3.js v7 (for the dependency graph)
- dagre-d3 (for DAG layout — concepts flow left-to-right by tier)

### Page Layout

```
┌─────────────────────────────────────────────────────────┐
│  [Course Title]                    [Light/Dark toggle]   │
├─────────────────────────────────────────────────────────┤
│  PROGRESS OVERVIEW                                       │
│  ████████░░░░░░░░  45% mastered                         │
│  12 concepts: 5 mastered | 3 in progress | 4 unstarted  │
│  Sessions: 8 | Last: 2024-03-15                         │
├─────────────────────────────────────────────────────────┤
│                                                          │
│  CONCEPT DEPENDENCY GRAPH                                │
│                                                          │
│  [Interactive DAG visualization]                         │
│  ○ → ○ → ◉ → ○                                         │
│       ↘       ↗                                         │
│        ○ → ○                                            │
│                                                          │
│  Legend: 🟢 Mastered  🟡 In Progress  ⚪ Unstarted       │
│          🔴 Has Mistakes  🔒 Blocked                     │
│                                                          │
├─────────────────────────────────────────────────────────┤
│  CONCEPT DETAIL (click a node above)                     │
│  ┌──────────────────────────────────────┐               │
│  │ Concept: [Name]         Tier: [N]    │               │
│  │ Derive: ✓/✗  Skeptic: ✓/✗           │               │
│  │ Problems: ✓/✗  Oral: ✓/✗            │               │
│  │ Mistakes: [N]  Last reviewed: [date] │               │
│  │ Next action: [recommendation]        │               │
│  └──────────────────────────────────────┘               │
├─────────────────────────────────────────────────────────┤
│  DANGER ZONE                                             │
│  ⚠️ Pattern: [description] — [N] occurrences            │
│  ⚠️ Pattern: [description] — [N] occurrences            │
└─────────────────────────────────────────────────────────┘
```

### Graph Specifications

**Node styling:**
- Shape: rounded rectangle
- Size: proportional to difficulty tier (larger = harder)
- Color fill:
  - `#10b981` (green) — mastered (all 4 gates passed)
  - `#f59e0b` (amber) — in progress (at least 1 gate passed)
  - `#e5e7eb` (light grey) — unstarted
  - `#6b7280` (dark grey, 50% opacity) — blocked (prerequisites not met)
- Border:
  - `#ef4444` (red) thick border — has 2+ mistakes logged
  - Normal border otherwise
- Label: concept name (truncated if long, full on hover)

**Edge styling:**
- Arrows from prerequisite → dependent concept
- Solid line if prerequisite is mastered
- Dashed line if prerequisite is NOT yet mastered (shows what's blocking)

**Layout:**
- Left-to-right DAG (dagre layout)
- Tier 1 concepts on the left, Tier 3 on the right
- Vertical spacing groups related concepts

**Interactivity:**
- Hover: show tooltip with concept name, status, gate summary
- Click: populate the detail card below the graph
- Zoom/pan: mouse wheel + drag (D3 zoom behavior)

### Data Embedding

Embed the data as JavaScript variables at the top of the HTML:

```html
<script>
const COURSE_DATA = { /* parsed from COURSE.md */ };
const PROGRESS_DATA = { /* contents of progress.json */ };
const MISTAKES_DATA = [ /* parsed from mistakes.jsonl */ ];
</script>
```

Parse `COURSE.md` to extract:
- Concept names
- Tier assignments
- Prerequisite relationships (the edges)
- Difficulty ratings

### Styling Requirements

- Clean, modern design (no framework — just CSS variables)
- Dark mode default with light mode toggle (persisted in localStorage)
- Monospace font for data, sans-serif for labels
- Responsive: graph resizes with viewport
- Color palette accessible (not just color — use shapes/borders too)

### Dark mode colors:
- Background: `#0f172a`
- Card background: `#1e293b`
- Text: `#f1f5f9`
- Accent: `#3b82f6`

### Light mode colors:
- Background: `#ffffff`
- Card background: `#f8fafc`
- Text: `#1e293b`
- Accent: `#2563eb`

## Rules

- The HTML must be COMPLETELY self-contained (no external CSS files, no local JS imports)
- External dependencies only via CDN: D3.js, dagre-d3
- Must work when opened as a local file (`file:///...`)
- If `progress.json` doesn't exist yet, show an empty state with "Run /setup-course first"
- If `mistakes.jsonl` is empty, hide the Danger Zone section
- The file should be < 50KB (data excluded)

## Regeneration

Every time `/dashboard` is run, it overwrites `dashboard.html` with fresh data. The HTML is a snapshot — run it again after a study session to see updated progress.

## Usage
```
/dashboard
```
Generates `dashboard.html` in the current course folder. Open in browser.
