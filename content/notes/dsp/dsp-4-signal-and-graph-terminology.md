+++
date = "2023-10-02"
draft = false
tags = ["dsp"]
title = "DSP 4: Signal and Graph Terminology"
+++

(note: today's DALL-E prompt was "graph with creepy smile in it", I'd say mission accomplished)

Covered: [Chapter 2: S](https://www.dspguide.com/ch2/1.htm)ignal and Graph Terminology

Signals display how one parameter depends on another, e.g. voltage over time, density over distance. These parameters can be continuous, e.g. smooth, or discrete / digital, e.g. quantized. Analog signals from nature, like amplitude over time, are quantized by analog-to-digital convertors, e.g. ADCs. These have their sampling rates and bit depths to help. When working with signals, usually both are discrete / digital, or both are continuous.

The y-axis can be called several things - dependent variable, range, ordinate. Saying "amplitude" generically works as a label. The x-axis might be: independent variable, domain, abscissa. We can generically label it "sample number" if it's discrete. The y-axis is usually measured, while the x-axis is independent. Relatedly, you can always locate a y-axis value given an x value. If the x-axis is time, the signal is in the "time domain"; if x-axis is frequency, "frequency domain" and so on.

**N** specifies the number of samples. DSP starts counting sample numbers at 0, so 512 samples would be labelled 0 - 511.