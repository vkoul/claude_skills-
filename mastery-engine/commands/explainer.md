# Explainer — The 9-Layer Explanation Stack

When you encounter a new concept, this skill generates a deep, multi-layered explanation designed to build genuine understanding — not memorization. Each layer attacks the concept from a different angle, and together they form a complete mental model.

## The Explanation Stack

For every concept, generate ALL 9 layers in order. Do not skip layers. Each one does work the others can't.

### Layer 1: Naive Intuition
- One sentence, zero jargon
- What you'd tell a smart friend outside the field at a bar
- This is the anchor — if you lose everything else, this remains

### Layer 2: Why This Concept Needs to Exist
- What problem was unsolved before this?
- What breaks if you don't have it?
- What ugly workaround were people stuck with?
- Concepts learned as *solutions to a felt problem* stick. Concepts learned as definitions don't.

### Layer 3: Visual / Geometric
- Diagram, graph, or picture of the idea
- Where possible: describe an interactive version (drag a parameter, watch the shape change)
- If the concept lives in a space, draw the space
- If it's a process, draw the process flow
- Label the axes and key regions in plain language

### Layer 4: Mathematical / Formal
- The actual notation and derivation
- ONLY after layers 1-3, so every symbol maps to something already felt
- Define every variable. No "it's obvious" steps.
- Show WHERE each assumption enters and WHY it's needed

### Layer 5: Concrete Worked Example
- Plug in real numbers (realistic, not toy)
- Trace every step — no "it follows that" shortcuts
- Show intermediate calculations
- The reader should be able to follow with a calculator

### Layer 6: Edge Cases and Failure Modes
- Where does the intuition from Layer 1 mislead you?
- What inputs break the method?
- When does the formula give nonsense?
- What are the boundaries of applicability?
- This is where real understanding vs. pattern-matching gets exposed

### Layer 7: Connection to What You Already Know
- Read `COURSE.md` and `progress.json` to know what the student has already mastered
- Explicit analogy or contrast to a prior concept
- "This is like X, except Y" or "This generalizes X by relaxing assumption Z"
- New nodes need edges into the existing knowledge graph, not to float alone
- This is where retention comes from

### Layer 8: Minimal Generative Test
- Not "can you recite this" but "can you predict what happens if I perturb one assumption?"
- 2-3 "what if?" questions that test genuine understanding
- If the student can generate correct predictions in a slightly novel setup, they actually have it
- Include the answers (collapsed/hidden) for self-check

### Layer 9: Compressed Form
- After all the above, compress back down to:
  - **One sentence** (the retrieval cue)
  - **One equation** (the formal anchor)
  - **One picture** (the visual anchor)
- Memorable enough to be a flashcard or misconception log entry
- This is what gets stored in long-term memory

## CRITICAL: Common Wrong Intuition Callout

At EACH layer, include a clearly marked callout:

```
⚠️ WRONG INTUITION: [The plausible-but-incorrect mental model most people default to]
WHY IT'S WRONG: [What specifically breaks]
THE FIX: [The correct reframe]
```

This is often MORE valuable than the correct explanation itself. Most stuck-ness comes from an unexamined wrong model, not absence of a right one. Naming the wrong model directly inoculates against it.

## Personalization (reads learner.json)

Adapt the explanation stack based on the learner profile:

| Learner Attribute | Adaptation |
|-------------------|-----------|
| `preference: "intuition first"` | Spend more space on Layers 1-3, make Layer 4 gentler with more intermediate steps |
| `preference: "formalism first"` | Lead with Layer 4 notation early, make Layers 1-3 brief confirmations |
| `weakness: "math notation scares me"` | Layer 4: introduce notation ONE symbol at a time, always mapping back to Layer 1 intuition. More verbal, less symbolic. |
| `weakness: "I memorize but don't understand"` | Heavy emphasis on Layer 2 (WHY) and Layer 6 (WHERE IT BREAKS). These break memorization patterns. |
| `background` contains relevant domain | Layer 7: use analogies from their actual background (e.g., if they know causal inference, explain power analysis via "the minimum detectable effect is like...") |
| `familiarity: 4-5` | Compress Layers 1-3 into a quick confirmation, expand Layers 6-8 (edge cases and generative tests — where deep learners actually have gaps) |
| `familiarity: 1-2` | Expand Layers 1-3 significantly, add more examples in Layer 5, make Layer 4 optional on first pass |
| `goal: "use at work"` | Layer 5: use workplace-realistic examples, not textbook ones. Add a "when would you use this in practice?" section. |
| `goal: "teach others"` | Emphasize Layer 1 and Layer 7 (these are what teachers need most). Add "common student questions" at each layer. |

## Session Flow

1. Read `learner.json` for personalization preferences.
2. Read `COURSE.md` for course context and what's already known.
3. Read `progress.json` for mastered concepts (Layer 7 needs this).
4. Read `mistakes.jsonl` for any prior misconceptions about this concept.
5. Ask: "What concept do you want explained?" (or accept as argument)
6. Generate the personalized 9-layer stack with wrong-intuition callouts.
7. After delivery, ask: "Which layer felt weakest? Want me to expand any layer or generate more examples?"

## Output Format

```
# [Concept Name] — Full Explanation Stack

## ⚡ Layer 1: The One-Sentence Version
[intuition]

⚠️ WRONG INTUITION: [common misconception at this level]
WHY IT'S WRONG: [...]

---

## 🧩 Layer 2: Why This Exists
[problem it solves, what breaks without it]

⚠️ WRONG INTUITION: [...]

---

## 📐 Layer 3: The Picture
[diagram/description, ideally with interactive parameters noted]

⚠️ WRONG INTUITION: [...]

---

## 📝 Layer 4: The Math
[formal notation and derivation]

⚠️ WRONG INTUITION: [...]

---

## 🔢 Layer 5: Worked Example
[concrete numbers, every step shown]

⚠️ WRONG INTUITION: [...]

---

## 💥 Layer 6: Where It Breaks
[edge cases, failure modes, boundaries]

⚠️ WRONG INTUITION: [...]

---

## 🔗 Layer 7: Connection to Your Existing Knowledge
[explicit links to prior concepts from progress.json]

⚠️ WRONG INTUITION: [...]

---

## 🧪 Layer 8: Can You Predict?
[2-3 "what if" questions]

<details><summary>Answers</summary>[...]</details>

---

## 💎 Layer 9: The Compressed Form
- **Sentence**: [...]
- **Equation**: [...]
- **Picture**: [description or ASCII]

---

## 🗺️ Where This Fits
- Prerequisites: [what you needed to know first]
- Unlocks: [what concepts this enables]
- Related misconceptions already logged: [from mistakes.jsonl if any]
```

## After the Explanation

- Ask if the student wants to immediately test understanding with `/socratic` on this concept
- Suggest adding the Layer 9 compressed form to their Anki deck
- If the concept was in `progress.json` as "unstarted", move it to "in_progress"

## Usage
```
/explainer
/explainer [concept name]
```
