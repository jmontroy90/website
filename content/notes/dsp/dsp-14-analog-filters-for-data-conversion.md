+++
date = "2024-01-02"
draft = false
tags = ["dsp"]
title = "DSP 14: Analog Filters for Data Conversion"
+++

- Covered:
  - [Chapter 3: Analog Filters for Data Conversion](https://www.dspguide.com/ch3/4.htm)
- Key Terms:
  - anti-aliasing filter
  - Chebyshev, Butterworth, Bessel
  - Sallen-Key Circuit
- Questions:
  - How the heck does a Sallen-Key Circuit work?
  - What is a switched capacitor filter, exactly?
  - Is "anti-aliasing" here similar to the graphical antialiasing setting?

(Programming note: we're back! Took some time to see Hoi An and Da Nang with a friend. Happy 2024! May your luck be analog and your emotions only as convolved as you desire.)

---

So how do we actually remove frequencies above the Nyquist rate from the analog signal, in order to prevent aliasing? Well, we use an **antialias filter**! Note that we also use a *reconstruction filter* after the DAC, to compensate for conversion artifacts (I'm sure I'll learn more on this later):

![Diagram showing analog signal processing chain](https://static.wixstatic.com/media/004b14_dbff20ca923e4402971373aed29a82ea~mv2.png)

We need to understand these hardware filters, especially if we aim to replace hardware filtering with software.

There are three kinds of filters:

1. **Chebyshev** - best at roll-off
2. **Butterworth** - best at passband ripple
3. **Bessel** - best at step response and passband ripple

They can all be made from simple RC circuits, in particular the ***Sallen-Key Circuit***, and all have different intended behaviors and trade-offs. 

These circuits are great in theory, but difficult in practice (hard to put resistors on an integrated circuit), and so instead the switched capacitor filter is preferred, since it can be manufactured as an integrated circuit quite easily. Here, instead of the cutoff filter being related to capacitance, the cutoff filter is related to the clock frequency used to drive the switches.

Observe the following rolloffs per filter:

![Frequency response comparison of filter types](https://static.wixstatic.com/media/004b14_f7fe78d2d2164966aaf35ebb5f355617~mv2.png)

You'll notice the following characteristics per filter:

1. Each filter has some **rolloff** in frequency, meaning you need some wiggle room in your Nyquist rate. If you want to keep everything below 5kHz, you can't just sample at 10kHz - you need to account for the rolloff of your chosen analog antialias filter! The Chebyshev filter performs best here.
2. Each filter has some degree of **passband ripple** (the waviness in amplitude on the top of the passband), in particular the Chebyshev filter. The Butterworth and Bessel do well here - Bessel at the cost of roll-off.
3. See below: each filter has some **step response**, e.g. how quickly and well the filter responds to a step increase in input. The Bessel filter does great; the other two overshoot and have some ringing frequencies:

![Step response comparison of filter types](https://static.wixstatic.com/media/004b14_ab7d7530d39c4b9989e3aaba0e7d9daa~mv2.png)