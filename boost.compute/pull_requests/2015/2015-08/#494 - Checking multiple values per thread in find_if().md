# #494 Checking multiple values per thread in find_if() [Closed]

> Username: jszuppe  
> Created at: 2015-08-15 14:17:43 UTC  
> Updated at: 2016-05-01 11:21:14 UTC  
> Closed at: 2015-08-15 14:29:42 UTC  
> Url: https://github.com/boostorg/compute/pull/494  

This addresses https://github.com/boostorg/compute/issues/27.  
  
I added `find_if_with_atomics_multiple_vpt()` which checks multiple values per thread. I kept original algorithm (it's renamed to `find_if_with_atomics_one_vpt()`) as I had found out that it's better for small and medium-sized inputs (depends on GPU; by default it's used for buffers with elements or less 1048576, but it's "tunable"). Probably it happens so because its kernel is much simpler.   
  
`find_if_with_atomics_multiple_vpt()` has two kernels - one for GPUs, one for CPUs or most generally for devices which have relativity big cache per compute unit. In the first reads from global memory are coalesced, in the second one reads from global memory are order so the big cache is efficiently used.  
  
For GPUs default values per thread parameter (vpt) equals 32.  For CPUs work is split equally between compute units.  
  
Below I present performance results for `find` algorithm which directly uses `find_if`. Benchmark `perf_find` checks algorithm performance in the worst case scenario when value we're looking for is the last element of the input.  
  
std::find() on CPU:  
  
```  
=== find with stl ===  
size,time (ms)  
2,0.000224  
4,0.000222  
8,0.000223  
16,0.000227  
32,0.000233  
64,0.000248  
128,0.000276  
256,0.000325  
512,0.000422  
1024,0.000620  
2048,0.001021  
4096,0.001805  
8192,0.003395  
16384,0.006668  
32768,0.013275  
65536,0.026349  
131072,0.055138  
262144,0.119968  
524288,0.241183  
1048576,0.519777  
2097152,1.379100  
4194304,3.389980  
8388608,6.701620  
16777216,13.807100  
33554432,28.022900  
```  
  
boost::compute::find() on CPU:  
- up to 7x faster  
  
```  
device: AMD Phenom(tm) II X3 710 Processor  
  
=== find with compute === [ PROPOSED ]  
size,time (ms)  
2,0.059621  
4,0.059610  
8,0.064822  
16,0.066171  
32,0.067660  
64,0.064405  
128,0.067676  
256,0.066502  
512,0.064754  
1024,0.074256  
2048,0.065423  
4096,0.070290  
8192,0.063756  
16384,0.073174  
32768,0.095522  
65536,0.084603  
131072,0.106276  
262144,0.165904  
524288,0.295915  
1048576,0.507413  
2097152,0.907767  
4194304,1.724090  
8388608,3.737880  
16777216,7.384920  
33554432,14.449600  
  
=== find with compute === [ MASTER ]   
size,time (ms)  
2,0.058339  
4,0.055977  
8,0.054589  
16,0.065270  
32,0.066115  
64,0.057937  
128,0.055955  
256,0.055025  
512,0.060878  
1024,0.066771  
2048,0.083266  
4096,0.067656  
8192,0.115239  
16384,0.115189  
32768,0.163517  
65536,0.266130  
131072,0.477942  
262144,0.897890  
524288,1.767940  
1048576,3.457340  
2097152,6.816540  
4194304,13.590800  
8388608,27.148400  
16777216,54.304600  
33554432,108.861000  
```  
  
boost::compute::find() on GPU:  
- up to 8% faster for big inputs (when multiple vpt version is used),   
- equally fast for small and medium-sized inputs  
- for big inputs (when multiple vpt version is used) it's faster in non-worst-case scenarios as every started thread check if value fulfilling given predicate is already found and if it has lower index than value that will be checked by this thread (because the algorithm should return the first fulfilling the predicate).  
  
```  
device: Capeverde [ Radeon HD7770 1GB]  
  
=== find with compute === [ PROPOSED ]  
size,time (ms)  
2,0.163925  
4,0.172390  
8,0.163876  
16,0.149378  
32,0.161240  
64,0.163684  
128,0.161740  
256,0.160055  
512,0.154372  
1024,0.140492  
2048,0.152486  
4096,0.161708  
8192,0.143643  
16384,0.160091  
32768,0.160668  
65536,0.145711  
131072,0.168451  
262144,0.179424  
524288,0.180423  
1048576,0.227733  
2097152,0.305461  
4194304,0.428461  
8388608,0.720791  
16777216,1.248160  
33554432,2.257180  
  
=== find with compute === [ MASTER ]   
size,time (ms)  
2,0.162994  
4,0.169244  
8,0.171470  
16,0.162829  
32,0.169513  
64,0.160942  
128,0.162011  
256,0.169082  
512,0.161847  
1024,0.161064  
2048,0.161721  
4096,0.159014  
8192,0.162864  
16384,0.159541  
32768,0.170088  
65536,0.163592  
131072,0.169682  
262144,0.176525  
524288,0.188048  
1048576,0.251718  
2097152,0.317693  
4194304,0.464674  
8388608,0.733724  
16777216,1.339440  
33554432,2.456310  
```  
  
```  
***********************************************  
What happens when searched value is not placed  
at the end of the buffer (worst-cast scenario), but in the middle?  
***********************************************  
  
=== find with compute ===  [ PROPOSED ]  
size,time (ms)  
4194304,0.359969  
8388608,0.526133  
16777216,0.790212  
33554432,1.321340  
  
=== find with compute ===  [ MASTER ]   
size,time (ms)  
4194304,0.463821  
8388608,0.733334  
16777216,1.345700  
33554432,2.449840  
```

---
