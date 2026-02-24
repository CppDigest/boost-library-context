# #628 Reduce for CPU [Merged]

> Username: jszuppe  
> Created at: 2016-07-09 18:28:24 UTC  
> Updated at: 2016-07-14 17:32:06 UTC  
> Merged at: 2016-07-12 03:16:19 UTC  
> Closed at: 2016-07-12 03:16:19 UTC  
> Url: https://github.com/boostorg/compute/pull/628  

```  
Device: Intel(R) Core(TM) i5-6600K CPU @ 3.50GHz (4/4 cores/threads)  
  
=== accumulate with stl ===  
size,time (ms)  
2,0.000133  
4,0.000133  
8,0.000133  
16,0.000135  
32,0.000136  
64,0.000139  
128,0.000151  
256,0.000165  
512,0.000189  
1024,0.000238  
2048,0.000463  
4096,0.000758  
8192,0.001401  
16384,0.002684  
32768,0.005183  
65536,0.009545  
131072,0.018869  
262144,0.033239  
524288,0.059656  
1048576,0.119746  
2097152,0.295889  
4194304,0.606165  
8388608,1.219240  
16777216,2.450350  
33554432,4.898130  
  
[master]  
  
=== accumulate with compute ===  
size,time (ms)  
2,0.022182  
4,0.021758  
8,0.021698  
16,0.021807  
32,0.024980  
64,0.021770  
128,0.025144  
256,0.025538  
512,0.025886  
1024,0.026272  
2048,0.026631  
4096,0.024762  
8192,0.030600  
16384,0.034140  
32768,0.043930  
65536,0.063510  
131072,0.102598  
262144,0.181727  
524288,0.377996  
1048576,0.655540  
2097152,1.303180  
4194304,2.582180  
8388608,5.143530  
16777216,10.262900  
33554432,20.502700  
  
[pr_reduce_on_cpu]  
  
=== accumulate with compute ===  
size,time (ms)  
2,0.022874  
4,0.022268  
8,0.022637  
16,0.022521  
32,0.022562  
64,0.023775  
128,0.025854  
256,0.025927  
512,0.026442  
1024,0.023142  
2048,0.026977  
4096,0.028208  
8192,0.031033  
16384,0.034424  
32768,0.043838  
65536,0.053461  
131072,0.055120  
262144,0.080476  
524288,0.092360  
1048576,0.131765  
2097152,0.228212  
4194304,0.421832  
8388608,0.832666  
16777216,1.697780  
33554432,3.461450  
```  
  
You can see that STL is very fast with the reduction (5x faster compare to our `serial_reduce` used currently in `master` for reduction on CPUs). I assume it uses SSE/AVX. When reducing vector of `int4_`s instead of vector of `int_`s, the performance of `serial_reduce` becomes almost identical to the performance of STL (for big inputs).  
  
New implementation is faster compared to STL for inputs bigger than 2097152 (for reduction with `plus<int>` function).

---

## Comment 1

> Username: coveralls  
> Created_at: 2016-07-09 20:36:13 UTC  
> Url: https://github.com/boostorg/compute/pull/628#issuecomment-231554600  

[![Coverage Status](https://coveralls.io/builds/6938139/badge)](https://coveralls.io/builds/6938139)  
  
Coverage decreased (-0.2%) to 80.4% when pulling **97f0225072bd4ec385cd80dc9a8d5b0b85e432ae on haahh:pr_reduce_cpu** into **a3f72e61916abb1a9d3213adff4d07888783f17a on boostorg:develop**.

---

## Comment 2

> Username: coveralls  
> Created_at: 2016-07-10 13:10:54 UTC  
> Url: https://github.com/boostorg/compute/pull/628#issuecomment-231588313  

[![Coverage Status](https://coveralls.io/builds/6941326/badge)](https://coveralls.io/builds/6941326)  
  
Coverage decreased (-0.2%) to 80.409% when pulling **409348192042c003dd2aeffe6b7e9ce570ec4778 on haahh:pr_reduce_cpu** into **a3f72e61916abb1a9d3213adff4d07888783f17a on boostorg:develop**.

---

## Comment 3

> Username: kylelutz  
> Created_at: 2016-07-12 03:16:22 UTC  
> Url: https://github.com/boostorg/compute/pull/628#issuecomment-231926836  

Looks good! Merged!

---
