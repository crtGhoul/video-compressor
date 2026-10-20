# Free Video Compressor

Paste a video, get a smaller MP4. No uploads, no accounts, no tracking — compression runs 100% in your browser.

**Live demo:** https://video-compressor-e82.pages.dev

![logo](logo.webp)

## How to use

**Compress tab:**
1. **Ctrl+V** a video anywhere on the page (or drag & drop it, or tap to pick a file on mobile). Pick several files at once and they queue up and compress one after another with the same settings.
2. Choose how it shrinks:
   - **Target size** — platform presets (**~8 / 10 / 16 MB WhatsApp / 25 MB Discord / 50 / 100 MB**), an exact-size box (type any MB value), or the custom bitrate slider.
   - **Quality (CRF)** — one slider from near-original to tiny; the encoder picks the bitrate.
3. Optionally cap the resolution (720p / 1080p / original), pick an output format (**MP4 / WebM / MOV**), trim the start/end, or strip the audio.
4. Hit **Compress**, watch the progress bar. Keep the tab open while it works — on phones it also asks the system to keep your screen on.
5. See original vs compressed size and % saved, then **Download**. Queued files land in a finished list, each with its own download link.

**Make GIF tab:**
1. Paste/drop/pick a video (the selected file is shared between tabs).
2. Set **start time**, **duration** (1–10 s), **width** (320/480/640), and **FPS** (10/12/15). An on-screen estimate shows the likely GIF size range, with a warning when a combo is headed past ~10 MB.
3. Hit **Make GIF** — it encodes with the high-quality two-pass palette method.
4. Preview the animated GIF, check its size, then **Download** the .gif. Short GIFs stay small enough to upload straight to Reddit, where they play animated inline.

## Privacy

This is the whole point of the project:

- **100% client-side.** Your video never leaves your device. There is no server, no upload endpoint, no account.
- **Zero trackers, zero analytics, zero telemetry.** The only network request the page makes (besides loading itself) is fetching the ffmpeg.wasm engine from a public CDN (unpkg, with a jsDelivr backup if the first source fails or stalls) on first use.
- Nothing you compress is visible to anyone but you — not even the site owner.

## Browser requirements

Any modern browser: Chrome, Edge, Firefox, Safari (desktop and mobile).

No special headers or settings needed. The app deliberately uses the **single-threaded** ffmpeg.wasm core, so it does not require SharedArrayBuffer / cross-origin isolation.

## How it works

The page loads ffmpeg.wasm 0.12.10 as ES modules from unpkg (falling back to jsDelivr, with a per-source timeout) and spawns its worker from a same-origin blob (bundling the four ESM worker files) via the `classWorkerURL` option, using the ESM core build. Your file is written to in-memory storage and re-encoded: **H.264 (veryfast preset) + AAC 96 kbps** for MP4/MOV, or **VP8 + Opus** for WebM. In target-size mode the bitrate is computed from your chosen size and the clip's (trimmed) duration; in CRF mode the encoder picks it. Phone clips are converted to constant frame rate so audio stays in sync, and MP4/MOV output gets the faststart flag so it starts playing before it finishes downloading.

## Known limits

- **Large files:** over ~1.5 GB on desktop / 500 MB on Android / 300 MB on iPhone will be rejected — a browser tab can't safely hold multi-GB files in memory.
- **Speed:** single-threaded WebAssembly encoding is slower than a native app. A 20-second 720p clip takes roughly a minute on a desktop.
- **Engine download:** the first compress downloads a one-time ~30 MB WebAssembly engine.
- **Codecs:** the browser has to be able to decode your input. If it can't, you'll get a clear message suggesting an MP4.

## License

MIT — do whatever you want with it. See [LICENSE](LICENSE).

Built with [Muse](https://muse.ai) by [crtGhoul](https://github.com/crtGhoul).
