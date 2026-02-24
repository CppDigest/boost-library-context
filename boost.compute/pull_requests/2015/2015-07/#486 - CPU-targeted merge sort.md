# #486 CPU-targeted merge sort [Merged]

> Username: jszuppe  
> Created at: 2015-07-30 19:25:44 UTC  
> Updated at: 2015-08-01 16:36:59 UTC  
> Merged at: 2015-08-01 16:10:13 UTC  
> Closed at: 2015-08-01 16:10:13 UTC  
> Url: https://github.com/boostorg/compute/pull/486  

This adds CPU-targeted stable merge sort (bottom-up version). It does not use local memory, so it should work on OS X without problems.  
  
Algorithms works in to steps:  
1. Insertion sort for the blocks of 64 values is performed (the size of block is customizable).  
2. Sorted blocks are merged.  
  
It has O(n) space complexity.  
  
If input has 512 elements of less insertion sort is performed. Merge sort outperforms std::sort() on my AMD Phenom(tm) II X3 710 when input has more than 65536, but I think it should work better on a CPU with more cores.  
  
In order to test this algorithm I recommend running `export BOOST_COMPUTE_DEFAULT_DEVICE_TYPE=CPU` (or however it's done in you shell) before executing `perf_sort` or `test_sort`. This environment variable makes your CPU the default device for Boost.Compute. Otherwise you will probably end up with GPU being the default device and other sort algorithm will be chosen.  
  
```  
=== sort with stl ===  
size,time (ms)  
2,0.000257  
4,0.000274  
8,0.000418  
16,0.000595  
32,0.001082  
64,0.001757  
128,0.003570  
256,0.008004  
512,0.016945  
1024,0.035260  
2048,0.073832  
4096,0.163459  
8192,0.346311  
16384,0.738796  
32768,1.568840  
65536,3.363270  
131072,7.135330  
262144,15.017100  
524288,31.467200  
1048576,66.027500  
2097152,138.717000  
4194304,291.526000  
8388608,609.542000  
16777216,1269.250000  
33554432,2650.750000  
  
=== sort with compute [with usage of merge with merge path] ===  
size,time (ms)  
2,0.057767  
4,0.057516  
8,0.057271  
16,0.057569  
32,0.053032  
64,0.055073  
128,0.058193  
256,0.071131  
512,0.124001  
1024,0.304344  
2048,0.392302  
4096,0.525845  
8192,0.709185  
16384,0.951316  
32768,1.608410  
65536,2.833530 <-- from this point merge sort is better than std::sort()  
131072,5.294260  
262144,9.382830  
524288,19.141900  
1048576,40.889400  
2097152,83.336700  
4194304,162.947000  
8388608,350.989000  
16777216,704.070000  
33554432,1505.250000  
  
=== sort with compute  [without usage of merge with merge path] ===  
size,time (ms)  
2,0.056930  
4,0.057798  
8,0.056881  
16,0.061113  
32,0.054506  
64,0.055622  
128,0.059051  
256,0.072387  
512,0.123452  
1024,0.309484  
2048,0.399578  
4096,0.544659  
8192,0.688900  
16384,0.959716  
32768,1.669320  
65536,2.824090  
131072,5.257270  
262144,9.357230  
524288,19.151700  
1048576,40.593000  
2097152,88.478000  
4194304,177.051000  
8388608,388.458000  
16777216,761.811000  
33554432,1610.750000  
```  
  
If this PR is accepted, I'll soon add similar CPU-targeted sort-by-key algorithm.

---

## Comment 1

> Username: kylelutz  
> Created_at: 2015-08-01 16:10:17 UTC  
> Url: https://github.com/boostorg/compute/pull/486#issuecomment-126934491  

Awesome! Merged!

---

## Comment 2

> Username: jszuppe  
> Created_at: 2015-08-01 16:36:59 UTC  
> Url: https://github.com/boostorg/compute/pull/486#issuecomment-126935851  

Thanks!

---
