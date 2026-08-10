# Skeye

**A real-time safety layer for robots — 360° hazard detection running on the robot, not beside it.**

🔗 **[shrishchou.github.io/Skeye](https://shrishchou.github.io/Skeye/)**

Industrial robots are fast, strong, and largely blind to anything that was not in the plan. Skeye adds a perception layer whose only job is noticing when something has entered space it should not be in, and doing so fast enough to matter.

Two constraints shape the design. It has to see in **every direction** — a hazard behind the arm is still a hazard — which means multiple cameras and a fused view rather than one forward-facing sensor. And it has to run **on-device**, because a safety system that depends on a network round-trip has made latency a safety-critical dependency. That pushes toward distilled YOLO-family detectors small enough for edge inference while keeping usable recall on the classes that matter.

---

## This repository

This repo holds the **product site** — the landing page, hero animations and demo footage published at the link above. It is not the perception stack.

```
index.html                        the site
product_hero_animation.mp4        hero animation
phone_vid.mp4                     demo footage
*_scrub.mp4 / *_scrubbed.mp4      scrub-optimized re-encodes
script.txt                        the ffmpeg commands that produce them
favicon/                          icons and web manifest
```

**On the scrub-optimized encodes.** The site scrubs video position as you scroll, and normal H.264 makes that stutter — seeking to an arbitrary frame means decoding forward from the last keyframe, which by default may be seconds back. `script.txt` re-encodes with `-g 6 -keyint_min 6 -sc_threshold 0`, forcing a keyframe every 6 frames so any seek target is at most a few frames from one, plus `-movflags +faststart` so playback can begin before the whole file has arrived. That is the difference between scroll-linked video feeling attached to the scrollbar and feeling like it is buffering.

```bash
# regenerate the scrub-optimized encodes
ffmpeg -i phone_vid.mp4 -c:v libx264 -preset fast -crf 18 \
  -g 6 -keyint_min 6 -sc_threshold 0 -pix_fmt yuv420p \
  -movflags +faststart phone_vid_scrubbed.mp4
```

Serve `index.html` from any static host.
