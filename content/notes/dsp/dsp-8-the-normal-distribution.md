+++
date = "2023-10-12"
draft = false
tags = ["dsp"]
title = "DSP 8: The Normal Distribution"
+++

Covered:

- [Chapter 2: The Normal Distribution](https://www.dspguide.com/ch2/5.htm)

Random noise generates the Normal distribution as its PDF - this was first discovered and termed by Gauss, so it's the Gaussian curve:

![Normal Distribution](004b14_03f4c8d3a9b64adcb39e010541dd09b3~mv2.png)

The standard deviation determines the width of the bell, and the mean says where the curve is centered. Like all PDFs, it's normalized s.t. its integral is one.

The integral of a PDF over its domain is known as its **cumulative distribution function (CDF)**. The CDF of the Gaussian is hugely important, but the Gaussian cannot be integrated through analytical means, instead only yielding to numerical integration where we essentially chop up and sample the the curve and integrate slowly.

The CDF of the Gaussian is shown as **Φ(x)**, and helps determine what the probability of a given value occuring between negative infinity and N standard deviations. This is where we get that 68% of values occur within one standard deviation of mean, by calculating the CDF for 1 and -1 and taking the difference.

![Gaussian CDF](004b14_c1487fd43c0c44e6b70e7a7a6d09fba9~mv2.png)