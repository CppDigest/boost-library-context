# #491 Using serial_reduce() when reducing on CPU device [Merged]

> Username: jszuppe  
> Created at: 2015-08-08 16:14:09 UTC  
> Updated at: 2015-08-13 08:39:45 UTC  
> Merged at: 2015-08-12 15:30:14 UTC  
> Closed at: 2015-08-12 15:30:14 UTC  
> Url: https://github.com/boostorg/compute/pull/491  

There is a bug which causes using `reduce_on_gpu` whenever reduction function is `plus<T>`, even if device is not a GPU. Now for CPU devices `serial_reduce` is always used.   
  
This should fix https://github.com/boostorg/compute/issues/476.  
  
Also this change improves performance of reduce algorithm as `reduce_on_gpu` is not efficient on CPU.   
  
```  
Device: AMD Phenom(tm) II X3 710 Processor (CPU)  
  
=== accumulate with compute === [ MASTER, reduce_on_gpu is used]  
size,time (ms)  
2,0.179798  
4,0.186708  
8,0.172284  
16,0.187684  
32,0.163798  
64,0.185535  
128,0.173303  
256,0.186923  
512,0.168256  
1024,0.173956  
2048,0.200156  
4096,0.235499  
8192,0.282913  
16384,0.664291  
32768,0.699428  
65536,1.259930  
131072,2.511260  
262144,4.673290  
524288,9.389420  
1048576,17.946400  
2097152,38.079900  
4194304,76.685300  
8388608,123.566000  
16777216,296.414000  
33554432,603.888000  
  
=== accumulate with compute === [ FIX, serial_reduce is used ]  
size,time (ms)  
2,0.075449  
4,0.075187  
8,0.076612  
16,0.074292  
32,0.073260  
64,0.072319  
128,0.072425  
256,0.077508  
512,0.068213  
1024,0.077216  
2048,0.074187  
4096,0.077526  
8192,0.083586  
16384,0.087925  
32768,0.103001  
65536,0.149885  
131072,0.201135  
262144,0.307403  
524288,0.557656  
1048576,1.031420  
2097152,2.347310  
4194304,4.742430  
8388608,9.330300  
16777216,18.533900  
33554432,36.776200  
```  
  
(This is `perf_accumulate` but accumulate uses reduce algorithm in this benchmark.)

---

## Comment 1

> Username: jszuppe  
> Created_at: 2015-08-13 08:39:45 UTC  
> Url: https://github.com/boostorg/compute/pull/491#issuecomment-130575356  

https://github.com/boostorg/compute/issues/476 can be closed (I assume you tested it on OSX like you said you'd do).

---
