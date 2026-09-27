# perfect-workout-media

Video clips for [perfect-workout](https://github.com/lemony312/perfect-workout).
Nothing here is authored by hand — every file is cut from a source video by a
script in the main repo. This repo exists only to *serve* them.

## Why this is a separate repo

GitHub Pages publishes at most **1 GB per site**. With the salsa clips included,
the main site built to ~1.28 GB — already 28% over the limit before the
intermediate courses added anything. Splitting the clips onto their own Pages
site puts both sites back under it:

| Site | Contents | Size |
|---|---|---|
| `perfect-workout` | app, plus bodyweight/posture/stretching clips | ~680 MB |
| `perfect-workout-media` (here) | all salsa clips, beginners + intermediate | ~940 MB |

All salsa clips live here, not just the new ones. Moving only the intermediate
material would have left the main site over the limit, which was the whole point.

## Layout

Paths mirror what the app requests, so a clip's URL is its repo path:

```
clips/salsa/moves/<move>-slow.mp4      solo steps course
clips/salsa/moves/<move>-fast.mp4
clips/salsa/couples/<move>-slow.mp4    couples courses
clips/salsa/couples/<move>-fast.mp4
```

served as:

```
https://lemony312.github.io/perfect-workout-media/clips/salsa/moves/enchufla-slow.mp4
```

The app never hard-codes that origin. Data files keep root-relative paths like
`/clips/salsa/moves/enchufla-slow.mp4`, and `frontend/src/lib/media.ts` in the
main repo maps the `/clips/salsa/` prefix onto `NEXT_PUBLIC_MEDIA_BASE`. Unset
that variable and the same paths resolve against the main site instead, which is
what makes local development work without this repo being reachable.

## Pages configuration

Deployed straight from the `main` branch, root directory — no build step, these
are static files. `.nojekyll` is required: without it Pages runs the files
through Jekyll, which is pure overhead here and silently drops any path
containing an underscore-prefixed segment.

## Regenerating

From the main repo, not here:

```
uv run scripts/clip_salsa.py              # beginners
uv run scripts/clip_salsa_intermediate.py # intermediate
```

then copy `frontend/public/clips/salsa/` over `clips/salsa/` and commit.

**Re-encoding is expensive in this repo, not just in CPU.** Git stores every
version of every binary forever, so re-cutting all 104 clips at a different
bitrate adds the full new size to history without removing the old — two passes
and this repo is over the 1 GB Pages limit on history alone. If clips need
re-encoding wholesale, rewrite history (or start a fresh repo) rather than
committing a second copy on top.
