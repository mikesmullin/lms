# Lumen LMS

A zero-dependency, single-page Learning Management System that bakes to static
HTML/CSS/JS. Courses are defined entirely in YAML — drop a file in `courses/`
and add its id to `courses/catalog.yaml` to publish a new course.

Because the course source is structured YAML, Lumen is also a
**machine-readable LMS**: the same course files that drive the human learning
experience can be ingested directly by an LLM or agent to quickly load the same
concepts, checks, and workflow knowledge.

**🌐 Live site:** https://mikesmullin.github.io/lms/

## Run locally

```bash
npx http-server -p 2030
# → http://localhost:2030
```

Any static file server works (the app only needs `fetch` access to the
`courses/*.yaml` files, so it must be served over HTTP, not opened via `file://`).

## Deploy to GitHub Pages

Commit this directory and enable Pages on the branch/folder. No build step is
required — it is plain static HTML.

## Authoring courses

- `courses/catalog.yaml` — lists the course ids shown in the catalog.
- `courses/<id>.yaml` — one file per course. See
  [courses/README.md](courses/README.md) for the full schema.

A static `catalog.yaml` manifest is used (rather than a directory listing)
because hosts like GitHub Pages don't expose directory autoindex. To add a
course, drop its YAML file in `courses/` and add its id to `catalog.yaml`.

### Rich content in concept slides

Concept `content` is Markdown. Two extras are supported:

- **Images** — standard `![alt](url)` syntax; rendered centered on their own line.
- **YouTube videos** — put a lone YouTube URL on its own line (e.g.
  `https://www.youtube.com/watch?v=ID` or `https://youtu.be/ID`) and it becomes
  a responsive embedded player automatically.

### Step types

| `type`      | `kind`            | Behavior                                                        |
| ----------- | ----------------- | --------------------------------------------------------------- |
| `concept`   | —                 | Markdown reading slide.                                         |
| `challenge` | `multiple-choice` | Single-answer quiz with explanation feedback.                   |
| `challenge` | `flashcards`      | Self-assessed memory deck; scored A–F on first-try recall.      |

A celebratory completion slide (with confetti) is appended to every course
automatically.

## Features

- Catalog of course tiles with live progress bars.
- Per-step checklist sidebar with type icons; self-assessed steps show a letter grade.
- Failed/imperfect challenge steps show a yellow warning badge that persists
  until corrected (a quiz answered correctly or a perfect flashcard run turns it green).
- Catalog tiles surface an "N to review" warning when a course has imperfect
  self-assessment steps — shown even when the course is 100% complete.
- Visiting a slide marks it complete; a **Mark complete / Completed** toggle lets
  users hold themselves accountable.
- Hash routing (`#/course/<id>/<stepId>`) — every slide is permalinkable.
- Progress persisted in `localStorage`.
- Dark emerald/forest theme with CSS transitions throughout.

## Stack

Bun · Alpine.js · Tailwind CSS · Phosphor Icons · js-yaml · marked (all via CDN).
