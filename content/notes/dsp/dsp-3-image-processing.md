+++
date = "2023-09-29"
draft = false
tags = ["dsp"]
title = "DSP 3: Image Processing"
+++

Covered:

- [Chapter 1: Image Processing](https://www.dspguide.com/ch1/5.htm) 

Images are fun signals - they can be signals over both time AND space, which makes them typically much bigger.

The original **CT (Computed Tomography)** scanner is an example of DSP done well. A single x-ray might not resolve everything you need - organs might be blocked, it's a static image, can't differentiate two similar organs - but if you do several x-rays at different angles, you can use DSP to essentially construct slices of a human, which allows you to resolve and probe more closely. This requires DSP, and was a huge boon!

**MRIs - Magnetic Resonance Imaging** - also relies on DSP. We scan across the body using magnetic fields that are tuned to the electron quantum states of nuclei in the body. This means the magnetic fields cause resonance, resulting in detectable radio waves emitted from these probed nuclei. So this is is a way to get at imaging the body WITHOUT x-rays, which is great. The processing of these MRI images comes via DSP.

Last but not least - all the filtering, contrast / brightness adjustments, focus / blur, compression, and so on that goes into taking a picture? That's all DSP.