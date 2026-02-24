# #477 Reduce by key algorithm  [Merged]

> Username: jszuppe  
> Created at: 2015-07-14 23:28:17 UTC  
> Updated at: 2015-07-19 13:29:41 UTC  
> Merged at: 2015-07-15 15:32:22 UTC  
> Closed at: 2015-07-15 15:32:22 UTC  
> Url: https://github.com/boostorg/compute/pull/477  

This addresses issue https://github.com/boostorg/compute/issues/116.  
  
This PR adds reduce by key algorithm (both serial and parallel version). Benchmarks for Boost.Compute, Thrust and BOLT are also included.   
  
Performance test:  
  
```  
=== reduce_by_key with compute ===  
size,time (ms)  
2,0.154890  
4,0.155084  
8,0.155598  
16,0.160392  
32,0.171135  
64,0.193295  
128,0.232472  
256,0.222300  
512,0.727985  
1024,0.722171  
2048,0.712100  
4096,0.709299  
8192,0.711192  
16384,0.713282  
32768,0.744579  
65536,0.813159  
131072,1.015370  
262144,1.135500  
524288,1.864210  
1048576,2.444740  
2097152,3.390430  
4194304,6.6587400  
8388608,12.988500  
16777216,31.645700  
33554432,90.473600  
=== reduce_by_key with bolt ===  
size,time (ms)  
2,0.462535  
4,0.465382  
8,0.466054  
16,0.465169  
32,0.465071  
64,0.460544  
128,0.460158  
256,0.469034  
512,0.487111  
1024,0.464353  
2048,0.435261  
4096,0.471321  
8192,0.469523  
16384,0.480998  
32768,0.501238  
65536,0.556485  
131072,0.631357  
262144,0.732189  
524288,2.281240  
1048576,2.793540  
2097152,2.672990  
4194304,6.687320  
8388608,12.697700  
16777216,24.959100  
33554432,53.312000  
```  
  
I checked in CodeXL and lower performance compared to the Bolt for big buffers is caused mainly by inclusive scan operation (used in reduce by key). At least on my HD7770.

---

## Comment 1

> Username: kylelutz  
> Created_at: 2015-07-15 15:32:34 UTC  
> Url: https://github.com/boostorg/compute/pull/477#issuecomment-121653582  

Merged! Great work on this!

---

## Comment 2

> Username: jszuppe  
> Created_at: 2015-07-15 17:18:13 UTC  
> Url: https://github.com/boostorg/compute/pull/477#issuecomment-121684399  

Thanks!

---
