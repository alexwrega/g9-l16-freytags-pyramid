# G9 · U3 Narrative · L16 — Plot & Conflict: Freytag's Pyramid

lwai Content Renderer lesson: the episode video with all eight questions placed as
timestamp cues — four mid-video checkpoints plus the four quiz questions chained on
the closing card.

## Files

| File | What it is |
|---|---|
| `lesson-g9-l16-freytags-pyramid.html` | The lesson (bootloader-style shell: one video segment with 8 cue mounts, hidden `tb-catalog`) |
| `items/*.xml` | 8 QTI 3.0 single-select MCQs (retry-until-correct, per-choice feedback, TTS catalogs) |
| `poster-g9-l16-freytags-pyramid.jpg` | Poster frame (fully-labeled pyramid, t=100.8s) |
| `preview.html` | Local harness that mimics pause-at-cue playback — **not** the real renderer |
| `_frames/gridA.jpg`, `gridB.jpg` | Frame-grid evidence used to verify checkpoint placement |
| `G9-U3-Narrative-L16-….md` | Source script (editor's reference) |

## Cue placement — how the timestamps were derived

The delivered MP4 runs **336.5 s**, not the scripted ~5:08 (308 s), so script times were not
usable. The editor's checkpoint holds were located by combining ffmpeg `silencedetect`
(four narration gaps of 5.5–7.7 s vs. the ~1 s gaps everywhere else), `freezedetect`,
and frame extraction to confirm what is on screen:

| Cue | Item | Hold (silence) | On screen | Notes |
|---|---|---|---|---|
| **96.5 s** | L16Q1 stage order | 94.5–100.0 | "Where's the climax?" card | after THE FIVE STAGES |
| **181.5 s** | L16Q2 internal conflict | 178.9–184.5 | "External or internal?" card | after TWO FLAVORS |
| **285.5 s** | L16Q3 conflict first | 282.3–287.9 | "What's the conflict?" card | after THE HABIT |
| **301.5 s** | L16Q-READ Poe passage | 295.8–303.5 | Poe excerpt, fully typed at ~300.8 | narration at 303.5 **reveals the answer** — cue must fire before it; 2 s margin left for player pause latency |
| **333 / 333.9 / 334.8 / 335.6 s** | L16Q4–Q7 closing quiz | 331.7–336.5 | End card: "Find the conflict first." + labeled pyramid | staggered ~0.9 s so they fire in order back-to-back over the static card; last completes ~1 s before the end so `VIDEO_COMPLETED` still fires |

Each cue fires ~0.5 s after its card settles, so the paused backdrop is the checkpoint card.

## Verify locally

```sh
cd "G9-L16 Freytag's Pyramid"
python3 -m http.server 8123
# open http://localhost:8123/preview.html — use the jump buttons to land ~4 s before each cue
```

## Before pushing to the platform

1. **URLs are relative** so the bundle works locally. The live player needs fully-qualified
   `https://` URLs for media (`<video src>`, `poster`). Catalog item hrefs may stay relative
   only if the embed sets `config.baseUrl`; otherwise make them absolute too.
2. The file is authored **bootloader-style** (outer `tb-instructional-content` wrapper).
   For postMessage / Direct URL embeds send only the inner content — do not double-wrap.
3. Run the MCQ set through the **mcq-qc** workflow (writer ≠ QC judge).
4. **Render-verify in the live player** — a green push / HTTP 200 does not mean it rendered.

## Content decisions to be aware of

- **Q-READ passage matches the video, not the script.** On screen the excerpt is trimmed
  (ends at "…that I am mad?"; the "disease had sharpened my senses" sentence is not shown).
  The item embeds the on-screen version so "based only on this passage" stays exact.
- **Q-READ correct-answer explanation was adapted.** The script's stock explanation
  ("the gate is open") referred to the old sample passage, not Poe; it now reads
  "Nothing in the world acts against him in these lines…".
- Wrong-choice feedback was written fresh (script only supplies the correct-answer
  explanation); hints redirect without revealing the answer, since items are
  retry-until-correct (`completionStatus=incomplete` on a miss).
- The end-of-episode quiz (L16Q4–Q7, "not scened" in the script) rides the video's end
  card as four chained cues rather than a separate post-video section, so the whole
  lesson lives inside the video. With only one segment left, the renderer shows no
  Continue button and completion is `VIDEO_COMPLETED` → `CONTENT_COMPLETED`.
