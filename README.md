# audio-vizard

Generative art and spectrum views that react to audio in real time. A single static HTML file: no build step, no dependencies, no server. All analysis runs in the browser and no audio leaves the page.

## Use it

Open the hosted page, or run it locally (capture needs `https` or `localhost`):

```
python3 -m http.server
# then open http://localhost:8000
```

Audio sources:

| Source | Works in | Notes |
|---|---|---|
| Capture tab or system audio | Desktop Chrome, Edge | Share a tab and tick **Share audio**. On Windows, sharing the whole screen with system audio also works. |
| Microphone or input device | Most browsers | On a Mac, route an app through a loopback device such as BlackHole and pick it from the input list. Phones can only listen through the microphone. |
| Audio file or demo loop | All modern browsers | Drag a file onto the page, or use **Open audio file**. |

Keyboard: `Space` play or pause, `←` `→` seek 5 s, `Esc` closes settings.

## Visuals

Drift (flow field, beat rings, spectrum wreath), Wreath (spectrum echoes), Spectrogram, Spectrum bars, Scope.

## Settings

- **Presets:** *Music* (adaptive per-band levels, beat rings) and *Noise & tones* (absolute dB scale, power averaging, 4096-point FFT, beats off, spectrogram view).
- **Basic:** preset, visual, palette, sensitivity, trails, motion speed, beat rings.
- **Advanced:** FFT size, analyser smoothing and dB window, band edges, spectrum range and bins, level scaling mode, bin statistic, attack and release, beat detector thresholds, colour mapping, and per-visual options.

Settings are stored in the browser (`localStorage`). **Reset all** restores the defaults.

## How the signal chain works

1. Audio goes into one Web Audio `AnalyserNode`.
2. Per frame, the 8-bit FFT magnitudes are reduced to six band levels, a log-spaced spectrum, a spectral centroid and a spectral-flux onset signal.
3. Levels are either rescaled by a running floor and peak (adaptive) or mapped through a fixed dB window (absolute), then smoothed with an asymmetric one-pole filter.
4. The visuals are drawn on a 2D canvas from those features.

Limits: levels are quantised to 8 bits over the analyser dB window, frequency resolution is the FFT bin width (sample rate divided by FFT size), and the beat detector is a heuristic with no tempo estimate. It is a visualiser, not a measurement instrument.
