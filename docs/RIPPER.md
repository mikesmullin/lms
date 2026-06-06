# RIPPER — Twitter Video → LMS Course Pipeline

A repeatable recipe for turning any Twitter/X video post into a fully authored
Lumen LMS course YAML file. No manual transcription required.

---

## Overview

```
Twitter URL
  └─▶ yt-dlp          (download audio track)
        └─▶ whisper-file   (transcribe to SRT)
              └─▶ AI author  (draft course YAML)
                    └─▶ courses/<id>.yaml  (publish to LMS)
```

---

## Prerequisites

| Tool | Notes |
|------|-------|
| `yt-dlp` | Found at `/home/user/.local/share/containers/storage/overlay/f6762f303c728aef9b998d688bf41b5195c0cdd23e2b40747aed5e5ea4d40cd2/diff/app/plugins/youtube/yt-dlp` — alias it for convenience |
| `whisper-file` | In PATH; uses `large-v3` model by default |

```bash
# One-time convenience alias (add to ~/.bashrc to persist)
alias yt-dlp="/home/user/.local/share/containers/storage/overlay/f6762f303c728aef9b998d688bf41b5195c0cdd23e2b40747aed5e5ea4d40cd2/diff/app/plugins/youtube/yt-dlp"
```

---

## Step 1 — Inspect the video formats

```bash
yt-dlp --no-update --list-formats "https://x.com/<user>/status/<id>"
```

Look for the `hls-audio-*` lines. For transcription, audio-only is sufficient
and much smaller than downloading full video.

---

## Step 2 — Download audio track

Pick `hls-audio-128000-Audio` (128 kbps — good quality, ~26 MB for a 28-min video).

```bash
yt-dlp --no-update \
  -f hls-audio-128000-Audio \
  -o /tmp/<slug>.mp4 \
  "https://x.com/<user>/status/<id>"
```

Example (the Riley Brown / Codex video):

```bash
yt-dlp --no-update \
  -f hls-audio-128000-Audio \
  -o /tmp/lms-riley-codex.mp4 \
  "https://x.com/rileybrown/status/2049285752866107856"
```

---

## Step 3 — Transcribe with Whisper

```bash
whisper-file /tmp/<slug>.mp4 /tmp/<slug>.txt
```

Output is **SRT subtitle format** (segment number, timestamp range, text line).
`large-v3` is the default model — accurate and runs locally.

Example:
```bash
whisper-file /tmp/lms-riley-codex.mp4 /tmp/lms-riley-codex.txt
# → wrote 779 segments → /tmp/lms-riley-codex.txt
```

---

## Step 4 — Extract plain text from SRT

Strip segment numbers and timestamps to get a readable transcript for authoring:

```bash
grep -vE '^\s*$|^[0-9]+$|-->' /tmp/<slug>.txt | cat -s
```

Pipe it to a file if you want to keep it:

```bash
grep -vE '^\s*$|^[0-9]+$|-->' /tmp/<slug>.txt | cat -s > /tmp/<slug>-plain.txt
```

---

## Step 5 — Author the course YAML

Using the plain-text transcript as source material, draft `courses/<id>.yaml`
following the Lumen LMS schema (see [courses/README.md](../courses/README.md)).

Guidelines for structuring from a video:

- Use **one concept slide per major chapter** from the video. If the video has a
  chapter list in the tweet body, those map directly.
- Prefer **concise, synthesised prose** over copy-pasting verbatim transcript —
  the LMS slide is a learning aid, not a transcript viewer.
- Add a **multiple-choice challenge** after every 2–3 concept slides to break up
  reading and check comprehension.
- Add a **flashcard challenge** near the end to reinforce key vocabulary/facts.
- Embed the original Twitter video on the intro slide using the bare URL on its
  own line — the LMS renders it as an embedded player automatically.

---

## Step 6 — Register the course

Add the course `id` to `courses/catalog.yaml`:

```yaml
courses:
  - agentic-fundamentals
  - your-new-course-id   # ← add this
```

---

## Step 7 — Preview locally

```bash
npx http-server -p 2030
# → http://localhost:2030
```

Open the catalog, click the new tile, and verify all slides, embeds, and
challenges render correctly.

---

## Twitter video embedding in the LMS

By default, the LMS only auto-embeds YouTube URLs. Twitter/X video URLs are
also supported after the `embedVideos()` function in `index.html` was extended
to detect `x.com` and `twitter.com` status links and render them via the
Twitter oEmbed widget.

To embed the source tweet in a slide, put the bare tweet URL on its own line
in a `content` block:

```yaml
    content: |
      Watch the full video this course is based on:

      https://x.com/rileybrown/status/2049285752866107856

      Then continue through the slides below.
```

---

## Example run (Codex 7 Capabilities)

| Step | Command | Result |
|------|---------|--------|
| Inspect | `yt-dlp --list-formats "https://x.com/rileybrown/status/2049285752866107856"` | 11 format options found |
| Download | `yt-dlp -f hls-audio-128000-Audio -o /tmp/lms-riley-codex.mp4 ...` | 26.74 MB in ~72 s |
| Transcribe | `whisper-file /tmp/lms-riley-codex.mp4 /tmp/lms-riley-codex.txt` | 779 segments, ~82 s |
| Author | AI read transcript → drafted `courses/codex-7-capabilities.yaml` | 17 steps |
| Publish | Added `codex-7-capabilities` to `catalog.yaml` | Live in LMS |
