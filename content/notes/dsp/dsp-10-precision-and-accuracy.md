+++
date = "2023-12-19"
draft = false
tags = ["dsp"]
title = "DSP 10: Precision and Accuracy"
+++

Covered: [Chapter 2: ](https://www.dspguide.com/ch2/7.htm)Precision and Accuracy

This is simpler. When you measure something repeatedly, you get a histogram or PMF. This PMF can be described by its precision and accuracy.

- **Precision** is captured by the standard deviation of that PMF - how well do individual sample points agree with one another? Are you fluctuating wildly per measurement, or are your measurements agreeing with each other? Low precision usually comes about because of noise, and precision is always improved by more measurements. 
- **Accuracy** is how far off your mean is from the true value of whatever you're measuring. So you might be very precise, but not accurate - that would mean, for example, that you measure 90 every single time, precisely, but your true value is 100. So your mean is far off from the real value. Which obviously requires you to know your real value in advance. Accuracy is usually more a systematic or calibration error, and doesn't improve with more measurements.

![Precision and Accuracy](004b14_7e4f8ed29a9b4f229f1333abf3ac06b8~mv2.png)

> When deciding which name to call the problem, ask yourself two questions. First: Will averaging successive readings provide a better measurement? If yes, call the error precision; if no, call it accuracy. Second: Will calibration correct the error? If yes, call it accuracy; if no, call it precision. This may require some thought, especially related to how the device will be calibrated, and how often it will be done.