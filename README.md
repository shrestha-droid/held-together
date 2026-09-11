# Held Together

Give it a drawing and it becomes tens of thousands of glowing particles. Touch it and it scatters, then pulls itself back together.

One HTML file, one dependency (three.js r128 from a CDN), no build step. Open `index.html` directly or host it anywhere static.

## Use it

Move across the drawing to push it. Drag fast to flick particles in the direction you're moving. Double-click or double-tap to scatter from that point. The speed control in the dock slows everything down to a quarter or speeds it up to double, including how fast the drawing builds itself.

| Key | Action |
| --- | --- |
| Space or S | Scatter the whole drawing |
| B | Replay the build |
| [ and ] | Slower, faster |
| R | Record a clip |
| O | Use your own drawing |
| D | Show stats |
| ? | Show shortcuts |

You can also drop an image anywhere on the page or paste one from the clipboard. Add `?stats` to the URL to open with stats showing.

## Recording

Pick 5, 10 or 15 seconds, and optionally start the clip with the drawing building itself. When it finishes, a preview opens. Nothing is saved until you press Save video. In Chrome and Edge that opens a normal Save dialog; in other browsers the file goes to your Downloads folder, and the preview tells you the file name and the shortcut that opens your downloads. On phones, Share sends the clip straight to Photos or another app.

## How it works

**Physics on a worker thread.** The simulation runs in a Web Worker. Each frame, position data moves between the worker and the main thread as transferable `ArrayBuffer`s, so 45,000 particles change threads without being copied. The physics function is written with no outside dependencies and serialised with `toString()`, so the same source runs in the worker or, if workers are unavailable, on the main thread. Every message carries a generation id, so frames from a previous drawing are dropped instead of corrupting a new one.

**Glow without post-processing.** Each point is drawn with a soft radial falloff and additive blending. Overlapping points sum into a bloom, at a fraction of the cost of a bloom pass on a phone.

**Sampling that respects the subject.** Pixels are sampled at random, weighted by colour saturation. Random sampling avoids the stripe patterns you get from taking every Nth pixel, and the weighting gives the main subject more particles than a pale background wash.

**Heat.** Particles glow hotter with speed and cool as they settle, so every interaction leaves a visible trail.

**Adaptive quality.** After each build, the page measures real frame times. If the device can't keep up, it steps down to fewer particles and a lower pixel ratio instead of stuttering.

**Accurate interaction.** The pointer is raycast into the drawing's own rotated plane, so the push lands exactly under the finger even while the drawing drifts.

**Also handled:** saving only on a click (browsers can block downloads that start on their own), pausing quality changes while recording, pausing in background tabs, WebGL context loss, reduced-motion preferences, keyboard access, and screen reader announcements.

## Deploy on GitHub Pages

1. Create a repository and upload `index.html` and this README.
2. Go to Settings, then Pages, and choose to deploy from the `main` branch.
3. Your link will be `https://<username>.github.io/<repo>/`.