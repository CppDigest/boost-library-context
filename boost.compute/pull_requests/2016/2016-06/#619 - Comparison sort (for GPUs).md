# #619 Comparison sort (for GPUs) [Merged]

> Username: jszuppe  
> Created at: 2016-06-22 15:08:27 UTC  
> Updated at: 2016-06-25 12:09:44 UTC  
> Merged at: 2016-06-24 03:24:38 UTC  
> Closed at: 2016-06-24 03:24:38 UTC  
> Url: https://github.com/boostorg/compute/pull/619  

This adds comparison sort,  sort-by-key, stable sort, stable sort-by-key algorithms. They're all based on counting merge sort. Additionally, for non-stable sorts there is a block sort step (i.e. sorting subarrays of elements) based on bitonic sorter which uses local memory.   
  
Tests included.  
  
Later this week I'll test stable block sort.  
### Performance  
  
I couldn't make a working `perf_bolt_comparison_sort`. I think there's something wrong with Bolt or my installation of Bolt. However, I've manged to make a change inside Bolt library that forces it to use comparison sort in sort().  
  
```  
=== comparison_sort with compute (it's not stable) ===  
size,time (ms)  
2,0.090120  
4,0.100572  
8,0.095840  
16,0.124584  
32,0.253873  
64,0.154097  
128,0.135110  
256,0.161665  
512,0.328393  
1024,0.421878  
2048,0.562983  
4096,0.666007  
8192,0.826941  
16384,0.943715  
32768,1.160150  
65536,1.135010  
131072,1.387090  
262144,1.277340  
524288,2.092640  
1048576,3.362030  
2097152,6.242780  
4194304,12.996700  
8388608,28.479800  
16777216,61.555000  
33554432,134.799000  
  
=== sort with bolt (forced to use comparison sort, stable) ===  
size,time (ms)  
2,0.062625  
4,0.049883  
8,0.053240  
16,0.063964  
32,0.064557  
64,0.059619  
128,0.998828  
256,1.116950  
512,1.208020  
1024,1.242320  
2048,1.148320  
4096,1.205540  
8192,1.148740  
16384,1.215990  
32768,1.295780  
65536,1.522320  
131072,2.452030  
262144,1.685660  
524288,2.143900  
1048576,3.853150  
2097152,8.349480  
4194304,17.739800  
8388608,38.839500  
16777216,83.740300  
33554432,182.363000  
  
=== comparison_sort with compute (stable) ===  
size,time (ms)  
2,0.078412  
4,0.093083  
8,0.113689  
16,0.132868  
32,0.625694  
64,0.739580  
128,0.875305  
256,0.968895  
512,1.103770  
1024,1.185820  
2048,1.333790  
4096,1.417630  
8192,1.587050  
16384,1.886840  
32768,1.879140  
65536,1.955670  
131072,2.141840  
262144,2.393540  
524288,3.265350  
1048576,4.328050  
2097152,7.674590  
4194304,15.662900  
8388608,33.965800  
16777216,73.278500  
33554432,159.330000  
```

---

## Comment 1

> Username: coveralls  
> Created_at: 2016-06-22 16:10:10 UTC  
> Url: https://github.com/boostorg/compute/pull/619#issuecomment-227794351  

[![Coverage Status](https://coveralls.io/builds/6707632/badge)](https://coveralls.io/builds/6707632)  
  
Coverage decreased (-0.3%) to 80.579% when pulling **5d31bd0f0797c0fb66b55d12079c6a1ededff7d2 on haahh:pr_comp_sort** into **04e7dde2204fb30eb223679ddefe437757a6feb9 on boostorg:develop**.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2016-06-24 03:24:41 UTC  
> Url: https://github.com/boostorg/compute/pull/619#issuecomment-228249686  

Thanks for implementing this, it's been sorely missing for a long time! And the performance looks great! Awesome work!

---
