+++
date = "2023-12-23"
draft = false
tags = ["dsp"]
title = "DSP 13: Digital-to-Analog Conversion"
+++

Last time we focused on analog-to-digital conversion -- what about digital-to-analog? 

The easiest way to convert from digital back to analog is by reconstructing it via the impulse train we generate from the original ADC process. Then you do a low-pass filter with the cutoff at the Nyquist frequency (all higher frequencies are artifacts of the sampling process), and that should perfectly reconstruct the original frequency spectrum.

This is fine in theory, but difficult in practice - impulse trains are hard to generate. So instead we do a **zeroth-order hold**, which is the "DAC equivalent of the sample-and-hold used during ADC". This hold generates the staircase-looking effect on the impulse train in the time domain, like so:

![Zeroth-order hold effect](004b14_d413b42ad84e4277a87d1182be2c510b~mv2.png)

In the frequency domain, the zeroth-order hold has the effect of multiplying the impulse train's frequency spectrum by the **sinc function**, a very common thing in DSP:

![Sinc function effect](004b14_d413b42ad84e4277a87d1182be2c510b~mv2.png)

So in order to reconstruct the analog signal, you need to do two things:

1. Remove all frequencies above the Nyquist rate
2. Boost the frequencies by the reciprocal of the zeroth-order hold's effect, i.e., 1/sinc(x)

This image breaks down the whole process well. Pay close attention to how we display time vs. frequency domains - both are necessary for understanding.

![Full DAC process](004b14_6f974cc376284e07b65b4547ae48aa75~mv2.png)

A last point - we know that the ADC process can lose information based on bit rate and sampling, but analog signals are just as lossy, based on *noise* and *bandwidth*. Noise is akin to quantization error, and bandwidth akin to sampling, e.g. are we able to see frequencies above a certain level.