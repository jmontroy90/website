+++
date = "2023-10-03"
draft = false
tags = ["dsp"]
title = "DST 5: Mean and Standard Deviation"
+++

**μ** is mean. It's calculated as ever:

![Mean formula](004b14_1ab34e704bdd4d979354bc8429cd8e73~mv2.png)

> In electronics, the mean is commonly called the DC (direct current) value. Likewise, AC (alternating current) refers to how the signal fluctuates around the mean value. If the signal is a simple repetitive waveform, such as a sine or square wave, its excursions can be described by its peak-to-peak amplitude. 

So basic signals can be analyzed with things like the mean, but most signals are complex. For this, we bring in the *standard deviation*, denoted by **σ**. The *average deviation* is calculated simply, with just summing absolute values of differences from the mean. But the standard deviation squares things, because we're more interested in signal power - **P ∝ V^2** - so we get:

![Standard deviation formula](004b14_6e8fb6b665d94a23a878c1764ad70bb7~mv2.png)

Note the *N - 1* in the summation - this is important later.

**σ^2** occurs a lot, and is called the **variance**. Standard deviation is fluctuation from the mean, and variance is the power of this fluctuation. We also have **root-mean-square (rms)**:

> By definition, the standard deviation only measures the AC portion of a signal, while the rms value measures both the AC and DC components. If a signal has no DC component, its rms value is identical to its standard deviation.

Often we want to do *running statistics* of means, standard deviations, variances, etc. without having to recalculate everything from scratch. Additionally, the equations above have rounding / floating-point errors if not careful. An alternate equation helps:

![Alternate variance formula](004b14_dbe3bec29df346e7ad856d4e5918a8c5~mv2.png)

Two more terms:

- **signal-to-noise ratio (SNR)**: mean divided by the standard deviation
- **coefficient of variation (CV)**: standard deviation divided by the mean, multiplied by 100 percent. 

> Better data means a higher value for the SNR and a lower value for the CV.