# #497 Support for custom comparison function in find_extrema() [Merged]

> Username: jszuppe  
> Created at: 2015-08-19 11:18:49 UTC  
> Updated at: 2015-08-20 07:54:13 UTC  
> Merged at: 2015-08-20 03:24:04 UTC  
> Closed at: 2015-08-20 03:24:04 UTC  
> Url: https://github.com/boostorg/compute/pull/497  

This resolves https://github.com/boostorg/compute/issues/460.  
  
[1] I fixed perf_max_element benchmark. It needed a fix because `max_element` was called in this way:  
  
``` cpp  
max = *boost::compute::max_element(device_vector.begin(), device_vector.end(), queue);  
```  
  
It includes reading found maximum from device memory using `*` dereference operator (it's very inefficient way to read data as it includes creating command queue which takes almost 2 ms). Now execution time does not contain time of reading maximum value from memory.  
  
[2] I added support for custom comparison function in `find_extrema()` (therefore for `max_element()`, `min_element()` and `minmax_element()` functions).  
  
[3] I renamed `find_extema_reduce()` to `find_extrema_with_reduce()` so it matches naming convention.  
  
Benchmark results on GPU (AMD Radeon HD7770 1GB /  Capeverde):  
  
```  
=== max_element with compute === [ MASTER ]  
size,time (ms)  
2,0.130710  
4,0.131326  
8,0.115282  
16,0.130115  
32,0.130315  
64,0.214163  
128,0.212712  
256,0.202653  
512,0.214832  
1024,0.202007  
2048,0.204027  
4096,0.210289  
8192,0.216632  
16384,0.204227  
32768,0.184873  
65536,0.210868  
131072,0.238884  
262144,0.257263  
524288,0.246357  
1048576,0.273122  
2097152,0.338420  
4194304,0.486059  
8388608,0.754712  
16777216,1.285120  
33554432,2.315750  
  
=== max_element with compute === [ PROPOSED - less<input_type> ]  
size,time (ms)  
2,0.136226  
2,0.136226  
8,0.116484  
16,0.120873  
32,0.143008  
64,0.142799  
128,0.127439  
256,0.134189  
512,0.208293  
1024,0.209421  
2048,0.210098  
4096,0.213982  
8192,0.217343  
16384,0.212064  
32768,0.201985  
65536,0.225200  
131072,0.240630  
262144,0.259257  
524288,0.248761  
1048576,0.285929  
2097152,0.337838  
4194304,0.490419  
8388608,0.767009  
16777216,1.299080  
33554432,2.317440  
  
  
=== max_element with compute === [ PROPOSED - Custom comparison function identical to less<input_type> ]  
2,0.142715  
4,0.137454  
8,0.140237  
16,0.136328  
32,0.139495  
64,0.147547  
128,0.151114  
256,0.167200  
512,0.307548  
1024,0.309549  
2048,0.314838  
4096,0.325505  
8192,0.307371  
16384,0.309083  
32768,0.307153  
65536,0.316564  
131072,0.307099  
262144,0.306305  
524288,0.304013  
1048576,0.324055  
2097152,0.382526  
4194304,0.531185  
8388608,0.799406  
16777216,1.337700  
33554432,2.356150  
```

---
