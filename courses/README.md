# Authoring Lumen LMS Courses

How to create and configure course content for the Lumen LMS. A course is a
single YAML file in `courses/`, plus one line in `courses/catalog.yaml`. No code
changes are required to add or edit courses.

## Quick start

1. Create `courses/<your-course-id>.yaml` (see the schema below).
2. Add the id to `courses/catalog.yaml`:
   ```yaml
   courses:
     - agentic-fundamentals
     - your-course-id        # <- add this line
   ```
3. Reload the app — the course appears as a tile in the catalog.

> The app does **not** scan the directory (GitHub Pages and most static hosts
> don't expose directory listings). A course only shows up if it's listed in
> `catalog.yaml`.

## Course file structure

```yaml
id: your-course-id          # must match the filename (without .yaml) and the catalog entry
title: Your Course Title
description: One or two sentences shown on the catalog tile.
icon: robot                 # a Phosphor icon name (https://phosphoricons.com) — no "ph-" prefix
version: "1.0"              # optional, free-form
tags:                       # optional, shown as pills on the tile
  - AI
  - Fundamentals

steps:
  - id: intro               # unique within the course; used in the URL hash
    type: concept
    title: First Slide
    content: |
      Markdown body goes here.
  # ... more steps ...
```

Notes:
- `id` must match the filename and the `catalog.yaml` entry exactly.
- `icon` is any [Phosphor icon](https://phosphoricons.com) name **without** the
  `ph-` prefix (e.g. `robot`, `brain`, `rocket`, `book-open`).
- Step `id`s appear in the permalink hash (`#/course/<course-id>/<step-id>`), so
  keep them short and stable.
- A celebratory **completion slide is appended automatically** — you do not add
  it yourself. Reaching it triggers confetti.

## Step types

There are two top-level `type`s: `concept` (reading) and `challenge`
(comprehension check). Challenges have a `kind`.

### 1. Concept (reading slide)

The default slide. `content` is Markdown.

```yaml
  - id: tools-overview
    type: concept
    title: Tools & Tool Use
    content: |
      Agents extend their capabilities through **tools** — callable functions.

      - **name** — e.g. `search_web`
      - **description** — tells the model when to call it
      - **parameter schema** — typed inputs

      > Blockquotes, `inline code`, *italics*, and [links](https://example.com)
      > all render.
```

Supported Markdown: headings, **bold**, *italics*, lists (ordered/unordered),
`inline code`, fenced code blocks, blockquotes, links, and the two rich embeds
below.

#### Images

Standard Markdown image syntax. Images render **centered on their own line**,
framed, and responsive.

```yaml
    content: |
      Here's the agent loop visualized:

      ![Alt text describing the image](https://example.com/diagram.png)

      Text after the image continues normally.
```

#### YouTube videos

Put a **lone YouTube URL on its own line** (blank line above and below) and it
becomes a responsive 16:9 embedded player automatically. No special syntax.

```yaml
    content: |
      Prefer video? Watch this:

      https://www.youtube.com/watch?v=aircAruvnKk

      Then continue.
```

Accepted URL forms: `youtube.com/watch?v=ID`, `youtu.be/ID`,
`youtube.com/shorts/ID`, and `youtube.com/embed/ID`.

### 2. Challenge — Multiple choice

A single-answer quiz. Mark the right answer with `correct: true`. The optional
`explanation` is shown as feedback after submitting.

```yaml
  - id: tools-quiz
    type: challenge
    kind: multiple-choice
    title: Check Your Understanding — Tools
    question: Which best describes a "tool" in an agentic system?
    choices:
      - id: a
        text: A physical device used by the developer
      - id: b
        text: A callable function the model can invoke
        correct: true            # exactly one choice should have this
      - id: c
        text: A user-facing UI widget
      - id: d
        text: An npm package
    explanation: |
      Tools are callable functions exposed to the model, letting it act beyond
      pure text generation.
```

Behavior:
- The chosen answer is **persisted** — revisiting the slide restores it.
- A wrong answer marks the step with a **yellow warning** in the sidebar and on
  the catalog tile ("N to review"). It stays yellow until the user answers
  correctly. A correct answer turns it green.
- Getting it wrong does **not** block progress.

### 3. Challenge — Flashcards

A self-assessed memory deck (honor system). The learner flips each card, guesses,
then grades themselves "Got it" or "Missed it". Missed cards are reshuffled back
into the deck until gotten. The final score is the **first-try recall** rate,
shown as a letter grade (A–F).

```yaml
  - id: memory-flashcards
    type: challenge
    kind: flashcards
    title: Memory Types — Flashcard Review
    cards:
      - front: What is in-context memory?
        back: Information held directly inside the active prompt/context window.
      - front: What is external memory?
        back: A vector/KV store the agent queries to retrieve relevant facts.
      - front: What is episodic memory?
        back: Logs of the agent's past actions and their outcomes.
```

Behavior:
- A score below 100% marks the step **yellow** (with the letter grade) in the
  sidebar and contributes to the tile's "N to review" count — even if the course
  is otherwise 100% complete.
- A perfect (100%) run turns it green.

## Progress, grading & state (for reference)

- Visiting any slide marks it complete on the checklist. Each slide has a
  **Mark complete / Completed** toggle for manual control.
- Progress is saved in `localStorage` (key `lumen-lms-progress-v1`), keyed by
  course id and step id.
- Catalog tiles show a progress bar, a green "Done" badge at 100%, and a yellow
  "N to review" badge when any challenge step is imperfect (the two can coexist).
- Opening an already-completed course jumps straight to the completion slide.
- The completion slide's **Reset course** button clears that course's saved
  progress, returning it to a never-taken state.

## Full example

See [agentic-fundamentals.yaml](agentic-fundamentals.yaml)
for a complete course exercising every slide type.

## Checklist for a new course

- [ ] File named `courses/<id>.yaml` with matching `id:` field.
- [ ] Added `<id>` to `courses/catalog.yaml`.
- [ ] Valid `icon:` (a Phosphor name without the `ph-` prefix).
- [ ] Every step has a unique `id`.
- [ ] Multiple-choice steps have exactly one `correct: true` choice.
- [ ] Images and YouTube URLs are each on their own line.
