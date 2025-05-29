+++
date = "2023-10-08"
draft = false
tags = ["dsp"]
title = "DST 6: Signal vs. Underlying Process"
+++

Big chunk today! Did a bunch of extra research on basic descriptive statistics I've forgotten over the years. Banner image credit to [NStatum](https://www.youtube.com/@nstatum8290).

Covered:

- [Chapter 2: Signal vs. Underlying Process](https://www.dspguide.com/ch2/3.htm) 
- [The Law of Large Numbers - Explained](https://www.youtube.com/watch?v=ycuPP72_DVU)
- [Variance and Standard Deviation: Why divide by n-1?](https://www.youtube.com/watch?v=wpY9o_OyxoQ) 

A coin flip as a process has a mean vale of 0.5, where 0 is tails and 1 is heads. But if you flip a coin 1000 times, your mean will not be 0.5. Make sure you understand this difference: **statistics of an actual signal vs. statistics of the underlying process.**

Take standard deviation. Can we get the standard deviation of the measured signal, and then use it to analyze the standard deviation of the underlying process?

All signals have error; this error is measured as:

![Error formula](004b14_f3a9c83cd2cc447a8244140dcf7e1f4a~mv2.png)

The [Law of Large Numbers](https://en.wikipedia.org/wiki/Law_of_large_numbers) proves that this error goes to zero as N goes to infinity:

> According to the law, the average of the results obtained from a large number of trials should be close to the expected value and tends to become closer to the expected value as more trials are performed.

A little more on the Law of Large Numbers per [this video](https://www.youtube.com/watch?v=ycuPP72_DVU) - there are actually two forms of the law: **Weak** and **Strong**. Both state the same basic idea, which is as you perform more trials, your average converges to something truer to the underlying process. But the weak form gives some wiggle room for your experimental mean to converge to within some delta of the underlying mean, whereas the strong form says you will definitely converge to the mean directly. Expressed mathematically:

![Law of Large Numbers formulas](004b14_486dd617a5e343fab8c912cc892cc318~mv2.png)

Also important here are the concepts of **sample vs. population**, and the statistics calculated from each. A population is a fully-representative data set, which is, by and large, impossible to obtain in the wild. A sample is typically what we're working with. When we have a sample, and calculate statistics, what we get are the **sample mean**, the **sample standard deviation**, and so on. Since we're working with samples, our statistics need to reflect the inherent inaccuracy built in to sampling.

This difference in sample vs. population helps explain the **N - 1** mystery. [This video](https://www.youtube.com/watch?v=wpY9o_OyxoQ) goes into more detail. The main takeaways seem to be: if we calculate variance via population mean, we don't have to include the minus-one term, because we're using a more representative statistic. x-bar, **x̄**, is the sample mean, but **μ**, or mu, is the population mean.

Importantly, when we use the sample mean, we actually end up calculating the smallest possible sum of squares in our variance. This makes immediate sense - the sample mean comes from our sample data points, and so the variance will always be calculated with a sample mean that lies directly in the middle of our sample points. Any other value used for the sample mean will always yield a larger sum of squares, because it won't sit simply right in the middle of all values, like a good sample mean should. So we've essentially baked in an underestimate of our variance when using sample mean, and we try to account for that by adding the minus-one term. But why not minus-two, you might ask? Empirically, N - 1 just seems to work best when estimating variance using a sample mean.

Note - when I say "sum of squares", I'm referring to the numerator here:

![Sum of squares](004b14_118bde3717b140d4b624a0dd49cd5b67~mv2.png)

One more thing - the sample variance has one less **degree of freedom** than the population variance, because they use the sample vs. population means, respectively. When you use the sample mean, your variances have to ultimately add up to zero, because the sample mean is definitionally in between all your sample points. That means that exactly one variance value is predetermined, because all the variances can be whatever, but that last variance value has to be something that ensures the variances sum to zero! So you can think of the **N - 1** term as kinda reflecting that loss of one degree of freedom. Also note - apparently the **N - 1** term falls out of the mathematical proof, so it's not just an empirical thing!

Finally - **σ^2** is the population variance (calculated with the population mean), whereas **s^2 ** is used to signify the sample variance.