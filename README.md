# 48 Hour Italian

A single-file web app that teaches travel-ready **standard Italian** in 48 fifteen-minute blocks
(24 lessons × 8 modules). Live at **[48italian.com](https://48italian.com)**, deployed on Cloudflare Pages.

Sister course: **48 Hour Portuguese** (European Portuguese) — same engine, separate repo.

## Structure

```
italian.html        ← the whole app: design, quiz engine, and all lesson content
dist/index.html     ← deploy copy (Cloudflare Pages build output dir = dist)
```

There is **no build step**. `dist/index.html` is just a copy of `italian.html`.
After editing the source, refresh the copy and commit:

```bash
cp italian.html dist/index.html
git add -A && git commit -m "..." && git push
```

Every push to `main` auto-deploys via Cloudflare Pages.

## How the content is organised

Two arrays near the top of the `<script>` block:

- **`MAP`** — the 24-lesson curriculum (id, week, title, blurb). Drives the home screen.
- **`LESSONS`** — the authored content. Each lesson:

```js
{ id, week, title, blurb, modules: [ /* 8 modules */ ] }
```

Each module has `teach` (cards) and `test` (questions), built with small helpers:

```js
lead("intro paragraph")                              // one per module
phrase("Italiano", "fo-NEH-tik", "English")          // 4–6 per module; CAPS = stressed syllable
tip("a <b>note</b> about usage or pronunciation")    // 1–2 per module
mc("question", ["a","b","c"], 1, "explanation")      // multiple choice; index of correct answer
text("question", ["accepted","variants"], "why")     // free text; lowercased accepted answers
```

Module 8 of every lesson is the **exam** (mixed review, no new phrases, pass at 70%).
Free-text answers accept forms with and without accents (e.g. `["è","e"]`).

## Features

- **Progress persistence** — saved to `localStorage` under `i48_progress`; hydrates on load,
  fails safe in private mode. "Reset progress" link in the footer.
- **Lesson unlocking** — finish a lesson (all modules ≥70%) to unlock the next.
- **Audio** — speaker buttons use the browser's Web Speech API with an **it-IT** voice picker
  (`pickItVoice()` / `speak()`). Quality depends on the Italian voice installed on the device.
  To swap in recorded audio later, replace the body of `speak()` with file playback.

## Validate before deploying

A quick structural check (Node, no dependencies):

```bash
node -e '
const fs=require("fs");const html=fs.readFileSync("italian.html","utf8");
const code=html.match(/<script>([\s\S]*?)<\/script>/)[1];
new Function("window","document","localStorage","confirm","speechSynthesis","SpeechSynthesisUtterance",code);
console.log("script parses OK");
'
```

## License / use

Course content © Alpha1 Entertainment. Not for redistribution.
