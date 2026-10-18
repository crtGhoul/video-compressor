# Free Video Compressor

Paste a video, get a smaller MP4. No uploads, no accounts, no tracking — compression runs 100% in your browser.

**Live demo:** https://video-compressor-e82.pages.dev

![logo](logo.webp)

## How to use

**Compress tab:**
1. **Ctrl+V** a video anywhere on the page (or drag & drop it, or tap to pick a file on mobile).
2. Pick a target size — **~8 MB**, **~16 MB**, or **~25 MB** presets — or dial in a custom bitrate with the slider. Optionally cap the resolution (720p / 1080p / original).
3. Hit **Compress**, watch the progress bar.
4. See original vs compressed size and % saved, then **Download** the MP4.

**Make GIF tab:**
1. Paste/drop/pick a video (the selected file is shared between tabs).
2. Set **start time**, **duration** (1–10 s), **width** (320/480/640), and **FPS** (10/12/15).
3. Hit **Make GIF** — it encodes with the high-quality two-pass palette method.
4. Preview the animated GIF, check its size, then **Download** the .gif. Short GIFs stay small enough to upload straight to Reddit, where they play animated inline.

## Privacy

This is the whole point of the project:

- **100% client-side.** Your video never leaves your device. There is no server, no upload endpoint, no account.
- **Zero trackers, zero analytics, zero telemetry.** The only network request the page makes (besides loading itself) is fetching the ffmpeg.wasm engine from the unpkg CDN on first use.
- Nothing you compress is visible to anyone but you — not even the site owner.

## Browser requirements

Any modern browser: Chrome, Edge, Firefox, Safari (desktop and mobile).

No special headers or settings needed. The app deliberately uses the **single-threaded** ffmpeg.wasm core, so it does not require SharedArrayBuffer / cross-origin isolation.

## How it works

The page loads ffmpeg.wasm 0.12.10 as ES modules from the unpkg CDN and spawns its worker from a same-origin blob (bundling the four ESM worker files) via the `classWorkerURL` option, using the ESM core build. Your file is written to in-memory storage, re-encoded to **H.264 (veryfast preset) + AAC 96 kbps** with a target bitrate computed from your chosen size preset and the video's duration, capped at your chosen resolution, then muxed to MP4 with the faststart flag so it starts playing before it finishes downloading.

## Known limits

- **Large files:** over ~1.5 GB on desktop / ~500 MB on phones will be rejected — a browser tab can't safely hold multi-GB files in memory.
- **Speed:** single-threaded WebAssembly encoding is slower than a native app. A 20-second 720p clip takes roughly a minute on a desktop.
- **Engine download:** the first compress downloads a one-time ~30 MB WebAssembly engine.
- **Codecs:** the browser has to be able to decode your input. If it can't, you'll get a clear message suggesting an MP4.

## License

MIT — do whatever you want with it. See [LICENSE](LICENSE).

Built with [Muse](https://muse.ai) by [crtGhoul](https://github.com/crtGhoul).
