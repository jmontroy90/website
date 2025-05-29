+++
date = "2023-12-18"
draft = false
tags = ["dsp"]
title = "DSP 9: Digital Noise Generation"
+++

(Note: this fell off - I was just having too much fun in Korea, then Thailand! But now I'm back in Hoi An, Vietnam, and feeling good to get back to this.)

Covered:

- [Chapter 2: Digital Noise Generation](https://www.dspguide.com/ch2/6.htm)

We often need to generate digital noise, which is a random signal that follows the Gaussian PDF. This noise is used to ensure that devices, signals, and algorithms work given the presence of random noise.

The [Central Limit Theorem](https://en.wikipedia.org/wiki/Central_limit_theorem) basically states that the distribution of random numbers drawn from independent random sources will converge to the Normal / Gaussian PDF. This is why the normal distribution gets so much attention - it allows statisticians to work with random processes with known Gaussian techniques, I suppose.

The below shows how the PDFs of different RND generation processes converges to the Gaussian - the final one is 12 RND sources and the PDF looks very Gaussian indeed:

![Image showing convergence to Gaussian distribution](004b14_a15903265f6e4257b4b1bf7577586746~mv2.png)

Random number generators (RNGs) use a seed, often a timestamp, to generate random numbers by permuting the seed through a known algorithm to get the next random number. These make these numbers pseudo-random, since they're reproducible given a known seed.