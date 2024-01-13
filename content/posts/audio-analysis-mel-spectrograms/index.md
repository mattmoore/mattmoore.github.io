---
title: "Audio analysis: Mel Spectrograms"
summary: "Frequency analysis in audio signals."
date: 2024-01-13T10:48:59-05:00
draft: false
---

## Waveform

You've maybe seen audio waveforms before. A waveform is essentially a pressure graph over time. It represents the amount of displacement or pressure at any given point in time. It's a very effective tool for audio editors to use for slicing audio and manipulating volume (pressure).

You can see below in the waveform graph, the X axis is time and the Y axis is amplitude (pressure).

{{< notebook "posts/audio-analysis-mel-spectrograms/jupyter/waveform" 700 >}}

While useful for general audio editing/mastering, waveforms represent the time domain, and don't give us any useful information about specific frequencies.

## Discrete Fourier Transform

If we want to see the specific frequencies in a given audio signal, we need to go from the "time domain" of the waveform to the "frequency domain". We do this with Fourier Transforms. To be exact, we use a "discrete" FFT.

Notice the X axis here represents frequency, and the Y axis represents magnitude of each frequency.

{{< notebook "posts/audio-analysis-mel-spectrograms/jupyter/discrete-fft" 900 >}}

## Spectrogram

While the discrete FFT above gave us the magnitude of each frequency, we lost the time information. To preserve as much information as possible for real-world practical audio processing, we need to preserve these 3 dimensions of the audio signal:

- Time
- Frequency
- Magnitude (the amplitude or "strength" of each frequency)

To do this, we can create what is called a "spectrogram". In a spectrogram the 3 dimensions above are represented as:

- X dimension for time
- Y dimension for frequency
- Heatmap for magnitude

Here's an example:

{{< alert >}}
**Note:** It may be difficult to see on this page - if you click the link below to view the jupyter notebook full-page and zoom in a bit it might be easier - but there are frequencies in the graph. They're so faint on this spectrogram that it's difficult to tell without looking closely.
{{< /alert >}}

{{< notebook "posts/audio-analysis-mel-spectrograms/jupyter/spectrogram" 950 >}}

## Mel Spectrogram

So far we've created a spectrogram to represent the magnitude of each frequency in the audio signal over time. However, there is one final step needed because of a rather interesting neurological phenomenon in humans called "frequency discrimination".

For humans, whenever the pitch of a sound (frequency) changes, we can generally tell that it has changed. However, our ability to hear the difference in pitch is not the same across all frequencies. We can discriminate changes more easily on the lower end of the frequency spectrum, but on the higher end of the frequency spectrum, our ability to differentiate between pitches is noticeably reduced.

In other words, our ability to perceive differences in pitch across the spectrum of frequencies is not linear; it is logarithmic.

What this means then is that we need to normalize (adjust) the frequency representation to something that more closely resembles the human perception of the frequencies and the changes in frequencies. This is done with a "mel-spectrogram". "Mel" is short for "melody".

Here's how we do that:

{{< notebook "posts/audio-analysis-mel-spectrograms/jupyter/mel-spectrogram" 950 >}}

Here the dimensions represented are:

- X axis for time
- Y axis for frequency
- Heatmap for magnitude, but here I've also converted it from raw amplitude to decibels, the standard for measuring volume.
