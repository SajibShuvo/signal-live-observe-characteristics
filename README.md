# Signal Lab

A single-file, browser-based teaching tool for live audio sampling. Speak into
your mic (or generate a test tone), then hear and *see* what happens as you
change the sample rate and bit depth in real time — including aliasing.

No build step, no dependencies, no server. Open `signal-lab.html` in a
browser and go.

![type: HTML/JS](https://img.shields.io/badge/type-single--file%20HTML-46e08a)
![deps](https://img.shields.io/badge/dependencies-none-46e08a)

## What it does

- **Live waveform** of your microphone (or a generated test tone), with the
  quantized/downsampled signal plotted as discrete sampled points overlaid on
  the raw input.
- **Sample rate** and **bit depth** faders that re-quantize the signal live —
  including on a *paused, frozen* recording, so you can stop, then sweep the
  faders and watch the sampled points and spectrum respond instantly.
- **Live spectrum** (FFT) of both the raw and sampled signal, with a marker at
  the current Nyquist frequency, so you can watch energy fold back
  (**aliasing**) when the sample rate drops below what the signal needs.
- **Test tone generator** — a pure sine wave with its own frequency slider,
  requires no microphone permission, and is the easiest way to *demonstrate*
  aliasing: push the tone above the Nyquist line and watch (and hear) it
  fold into a false lower pitch.
- **Monitor modes** — listen to the signal Off, Raw, or Sampled (aliased), to
  compare by ear as well as by eye.
- **Rolling window + scrub bar** — keeps a 10-second rolling history; turn
  off auto-follow (or hit Pause) to scrub back through it.
- **Zoom controls** — independent horizontal (time) and vertical (amplitude)
  zoom, plus an adjustable dot size for the sampled-points display.
- **Single-screen layout** — sized to fit one screen with no scrolling, for
  showing to a class or during a screen share.

## Usage

1. Open `signal-lab.html` in any modern desktop browser (Chrome, Edge,
   Firefox, or Safari).
2. Click **Start** and allow microphone access — or switch the **Source**
   toggle to **Test tone** if you'd rather not grant mic access.
3. Pull the **Sample Rate** and **Bit Depth** faders on either side of the
   scope and watch the sampled points, waveform, and spectrum change live.
4. To demonstrate aliasing: switch to **Test tone**, set the tone frequency
   above the dashed Nyquist line in the spectrum panel, and switch
   **Monitor** to **Sampled** to hear the aliased pitch.
5. Hit **Pause** to freeze a moment and inspect it — the faders and zoom
   controls keep working on the frozen data. Turn off **Rolling window** (or
   use it while paused) to scrub back through the last 10 seconds via the
   scrub bar under the scope.

No installation is required — this is a static HTML file. To host it (e.g.
for students to open on their own machines), just serve or share the file
directly; it can also be opened straight from disk (`file://`) in most
browsers, though some browsers restrict microphone access on `file://` URLs,
in which case serve it over `http://localhost` or any static host (GitHub
Pages, Netlify, etc.).

## How it works

- **Sampling** is simulated with a sample-and-hold decimator: the incoming
  audio (captured at the device's native rate, typically 44.1/48 kHz) is
  held at the chosen target rate rather than actually changing the hardware
  sample rate, which browsers don't expose control over. This reproduces the
  same stair-stepping and aliasing behavior as real downsampling.
- **Quantization** rounds each held sample to the nearest of `2^bitDepth`
  evenly spaced levels across the full amplitude range.
- **Spectrum** is computed with a small in-browser radix-2 FFT (Hann-windowed,
  2048-point) run on both the raw and sampled signal every frame, so the
  aliased/folded spectral image is visible in real time.
- Everything is recomputed live, at render time, from a rolling raw-audio
  ring buffer — including while paused — so slider changes are reflected
  immediately no matter the playback state.
- Built with the Web Audio API (`ScriptProcessorNode` for the audio graph)
  and a `<canvas>`-based renderer. No external libraries.

## Browser support

Requires the Web Audio API and `getUserMedia` (for microphone input). Works
in current versions of Chrome, Edge, and Firefox. Safari is supported but the
vertical fader sliders fall back to a simpler native rendering.


