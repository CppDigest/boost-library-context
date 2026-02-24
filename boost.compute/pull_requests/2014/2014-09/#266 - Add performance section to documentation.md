# #266 Add performance section to documentation [Merged]

> Username: kylelutz  
> Created at: 2014-09-22 01:38:31 UTC  
> Updated at: 2014-09-23 06:05:16 UTC  
> Merged at: 2014-09-23 05:15:19 UTC  
> Closed at: 2014-09-23 05:15:19 UTC  
> Url: https://github.com/boostorg/compute/pull/266  



---

## Comment 1

> Username: kylelutz  
> Created_at: 2014-09-22 01:42:57 UTC  
> Url: https://github.com/boostorg/compute/pull/266#issuecomment-56320924  

See: http://kylelutz.github.io/compute/boost_compute/performance.html

---

## Comment 2

> Username: coveralls  
> Created_at: 2014-09-22 01:59:30 UTC  
> Url: https://github.com/boostorg/compute/pull/266#issuecomment-56321648  

[![Coverage Status](https://coveralls.io/builds/1238477/badge)](https://coveralls.io/builds/1238477)  
  
Coverage remained the same when pulling **f3f1c390de71539d55f668eadc2e84453066c52e on perf-docs** into **0b0cbd399e01f183dfbe980750f02d20bbe409b3 on develop**.

---

## Comment 3

> Username: ddemidov  
> Created_at: 2014-09-22 04:56:12 UTC  
> Url: https://github.com/boostorg/compute/pull/266#issuecomment-56328815  

It would be nice to see Thrust results plotted there together with Boost.Compute. I can run some tests on Nvidia hardware (Tesla K40c) if you interested.

---

## Comment 4

> Username: ddemidov  
> Created_at: 2014-09-22 05:01:59 UTC  
> Updated_at: 2014-09-23 05:15:44 UTC  
> Url: https://github.com/boostorg/compute/pull/266#issuecomment-56329034  

There is a paper by @bmerry that compares scan and sort performance of several GPGPU libraries (including Boost.Compute). The benchmark sources are here: https://github.com/bmerry/ssbench.  
  
Edit: forgot to include the [link](http://www.brucemerry.org.za/publications.html#a-performance-comparison-of-sort-and-scan-libraries-for-gpus) to preprint.  
  
Another idea for more realistic tests: since there is Boost.Compute backend for Boost.odeint, it would be easy to add Boost.Compute tests to this suite of tests: https://github.com/ddemidov/gpgpu_with_modern_cpp.

---

## Comment 5

> Username: kylelutz  
> Created_at: 2014-09-23 05:35:04 UTC  
> Url: https://github.com/boostorg/compute/pull/266#issuecomment-56478034  

Yeah, this is really just a first pass to get some performance figures in the documentation.  
  
I'd be interested in seeing the results on the K40. Also adding the results from Boost.odeint would be great to have. I'm still not sure on the best way to display all of the performance information. There are lots of variables (algorithm, input type/size, device, platform, library, etc.) which leads to a huge number of charts/tables which could be generated. Displaying all this on a static HTML page (all we can generate with boostbook) is a bit difficult.  
  
I've been thinking about setting up another page which would allow interactive selection of various parameters (such as libraries, algorithms, and input sizes) to compare and generating the chart from timing information stored in a database somewhere. I think it would be a good resource for comparing many of the available GPGPU libraries.  
  
However, something like this may be better suited to a more "neutral" host (rather than as a part of the Boost.Compute documentation itself). One possibility is the [http://streamcomputing.eu/](http://streamcomputing.eu/) site which already hosts a large number of resources for GPGPU computing. @VincentSC would you be interesting in hosting something like this?

---

## Comment 6

> Username: ddemidov  
> Created_at: 2014-09-23 05:54:47 UTC  
> Url: https://github.com/boostorg/compute/pull/266#issuecomment-56478954  

For me, as a potential user, the relative performance on the same hardware is more interesting than performance of a specific device. For example, if one of Boost.Compute algorithms behaves similar to Thrust on a K40, it should probably be the case for the rest of Nvidia hardware.  
  
Further, it seems that some plots on the current performance page are redundant. For example, I  expect implementation of `accumulate`, `count`, and `inner_product` to be very similar and hence, show similar behaviour. If that is indeed the case, you could omit some of the results to make it easier to comprehend.  
  
I would group the algorithms into the ones that are clearly bandwidth-limited, like `transform` or `rotate`, and the ones that have non-trivial implementation like `sort`, `scan`, or `reduce`. Also, making the axes logarithmic could be a good idea.  
  
Regarding K40 tests: if you provide something similar to `perf/perf.py` driver for Thrust-based tests, I'll run it and report the results back to you.

---

## Comment 7

> Username: ddemidov  
> Created_at: 2014-09-23 06:05:16 UTC  
> Url: https://github.com/boostorg/compute/pull/266#issuecomment-56479473  

And the idea of independent resource with benchmarks for different libraries/SDKs/compute devices sounds really great!

---
