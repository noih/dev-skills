# Resolve Important Visual Gaps

Use distributed samples and transcript timestamps to locate useful passages, then
inspect the images. OCR can miss small code, moving words, or low-contrast captions;
an OCR-empty interval may still contain the key demonstration.

| Signal | Inspect until you have |
| --- | --- |
| Incomplete sentence or words appearing individually | Relevant clauses in order, including qualifiers |
| Building list, formula, table, or counter | Completed values with headers, relevant rows, and units |
| Scrolling or typed code | Overlapping readable sections and context needed for the explanation |
| Transition or comparison | Setup, change, and result, with intervening motion when relevant |
| Visual style | Complementary composition/detail views and motion when rhythm matters |

Increase sampling density around the important gap, not across the whole batch.
If another pass still misses it, inspect a continuous segment or exact frames around
the change. Stop when the selected point and its qualifiers are supported. If the
source itself is cropped or unreadable, record the gap; more frames cannot recover
missing pixels. Never fill missing wording from a plausible guess.

## Preserve Sequence and Context

- Animated words may disappear before the next appears: no frame may contain the
  whole sentence. Keep timestamped frames in reading order and transcribe the combined
  text separately. Never paint reconstructed wording onto a source screenshot.
- Verify actual extracted frames against media timestamps. ASR timing, scan indices,
  and seeking can drift; frame number divided by nominal fps may be inaccurate.
- Include preceding and following context where it changes meaning. Wait for final
  counters and read their labels: algorithm steps, elapsed time, and throughput are
  different claims. Include exceptions and failures alongside successful results.
- Describe only what the evidence establishes. A demonstrated effect does not reveal
  its implementation, and an uncertain ASR name does not identify a product.

## Deliver Readable Evidence

Retain selected full-resolution frames with source and time. Contact sheets aid
browsing; keep their source frames. Caption crops can clarify sequences, but retain
full frames and keep diagrams with their explanations. Put labels outside source pixels.
Inspect the exact attachment after compositing or conversion for legibility, clipping,
missing panels, and reading order.

Name the reviewed interval and remaining gaps. Verifying a selected passage does not
mean all subtitles were checked. Whole-video subtitle extraction, when requested,
requires a separate coverage pass. Keep corrected excerpts distinct from raw transcripts
and label whether they came from speech, screen text, or a supplied description.
