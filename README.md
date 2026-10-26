# audio-vizard

What does sound look like?

The waveform in an audio editor is a poor answer. It's a wiggly line, and it tells you almost nothing about what you're hearing. Your ear does something smarter. It splits the sound into frequencies and responds to how much of each one is there right now.

This page does the same thing a few dozen times a second, then draws with the result. Bass pushes particles around. Treble makes the lines brighter. A sudden onset sends a ring outward. It's one HTML file, and your audio never leaves your browser.

## Try it

Open the hosted page, or run it yourself. Browsers only allow live capture on `https` or `localhost`, so locally you need a server:

```
python3 -m http.server
# open http://localhost:8000
```

Then pick a source.

| Source | Works in | Notes |
|---|---|---|
| Capture tab or system audio | Desktop Chrome, Edge | Share a tab and tick **Share audio**. On Windows you can share the whole screen with system audio instead. |
| Microphone or input device | Most browsers | On a Mac, send an app through a loopback device such as BlackHole and choose it from the input list. A phone can only use the microphone. |
| Audio file or demo loop | All modern browsers | Drag a file onto the page or use **Open audio file**. |

Keys: `Space` plays and pauses, `←` and `→` seek 5 seconds, `Esc` closes settings.

## The idea

Sound is pressure over time. To see which frequencies are in it, take a short window of recent samples and ask, for each frequency, how well a sine wave of that frequency matches the signal. That's the Fourier transform, and the fast version is the FFT.

The window length is a trade. With N samples at sample rate fs, each frequency bin is fs/N wide and the window lasts N/fs seconds. At 2048 samples and 48 kHz that's about 23 Hz bins and a 43 ms window. Make the window longer and you can tell nearby frequencies apart, but you lose track of when things happened. You can't have both. That's the **FFT size** setting.

From the spectrum on each frame, the page pulls out four things.

- **Band levels.** Six slices of the spectrum, from 20 Hz to 12 kHz. Bass, mids and treble drive the motion.
- **A log-spaced spectrum.** Pitch is roughly logarithmic, so equal steps in log frequency look even to the ear. This feeds the wreath, the spectrogram and the bars.
- **Spectral flux.** Add up only the bins that got louder since the last frame. A big jump means something just started. When flux rises above its recent average by more than k standard deviations, that's an onset and a ring goes out.
- **The spectral centroid.** The centre of mass of the spectrum, measured on a log axis. It's a rough measure of brightness, and it decides where on the palette the colour sits.

### One thing worth knowing

The **Music** preset scales each band against its own recent floor and peak. That makes any track fill the screen, but a perfectly steady sound has no peak to be compared with, so it slowly fades to nothing. A pure tone or white noise will do exactly that.

The **Noise & tones** preset fixes this by using a fixed dB window instead, so a steady sound stays where it is. It also averages bins in linear power, because averaging dB values would dilute a narrow tone across a wide band and under-read it.

## Visuals

- **Drift.** A flow field of particles, beat rings, and the spectrum wreath in the middle.
- **Wreath.** The circular spectrum, leaving echoes of itself as it expands.
- **Spectrogram.** Frequency up, time to the left, brightness for level.
- **Spectrum bars.** With peak caps. In Absolute mode there's a dB axis.
- **Scope.** The waveform, started at a rising zero crossing so that a periodic signal holds still.

## Settings

**Basic** has the preset, visual, palette, sensitivity, trails, motion speed and beat rings. **Advanced** exposes the signal processing and the visual parameters, each with a short note on what it does. Rows that don't apply to the current visual or scaling mode are hidden. Settings are saved in your browser, and **Reset all** puts everything back.

## Limits

Levels are quantised to 8 bits over the analyser's dB window. Frequency resolution is the bin width above. The beat detector is a heuristic and doesn't estimate tempo. The dB axis shows mean power per FFT bin within each display bin, so a pure tone reads a few dB below its loudest bin. Pick **Loudest bin** under Bin statistic if you want the peak. This is a visualiser, not a measuring instrument.
