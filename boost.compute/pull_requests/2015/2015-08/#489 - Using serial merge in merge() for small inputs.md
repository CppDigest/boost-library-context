# #489 Using serial merge in merge() for small inputs [Merged]

> Username: jszuppe  
> Created at: 2015-08-01 17:45:44 UTC  
> Updated at: 2016-05-01 11:21:10 UTC  
> Merged at: 2015-08-01 20:44:52 UTC  
> Closed at: 2015-08-01 20:44:52 UTC  
> Url: https://github.com/boostorg/compute/pull/489  

It turns out that for relatively small serial merge algorithm outperforms merge with merge path, so I added `serial_merge_threshold` (what is considered "small" depends on used device). When input size is less or equal to `serial_merge_threshold` serial merge is used; otherwise merge with merge path is used.   
  
I thought I would have had to implement the serial merge, but it had been already implemented, just not used.  
  
Default `serial_merge_threshold` threshold value depends on device type - for CPUs it's 32768, for GPUs only 2048 (these values were obtained experimentally and are customizable).   
  
Benchmarks (thresholds are marked by `<----`):  
  
std::merge():  
  
```  
=== merge with stl ===  
size,time (ms)  
2,0.000229  
4,0.000233  
8,0.000236  
16,0.000250  
32,0.000279  
64,0.000394  
128,0.000617  
256,0.000846  
512,0.002174  
1024,0.004317  
2048,0.008136  
4096,0.015567  
8192,0.030530  
16384,0.061601  
32768,0.121734  
65536,0.244034  
131072,0.496816  
262144,0.981112  
524288,1.984900  
1048576,4.030950  
2097152,8.135040  
4194304,16.310800  
8388608,32.556800  
16777216,65.152000  
33554432,130.599000  
```  
  
boost::compute::merge() on a CPU (AMD Phenom(tm) II X3 710 Processor):  
  
```  
=== merge with compute === [ without serial_merge (only merge with merge path) ]  
size,time (ms)  
2,0.137814  
4,0.137454  
8,0.141248  
16,0.142791  
32,0.143798  
64,0.143406  
128,0.141068  
256,0.143525  
512,0.141164  
1024,0.169392  
2048,0.172788  
4096,0.180366  
8192,0.169614  
16384,0.182455  
32768,0.254819  <----   
65536,0.289103  
131072,0.412289  
262144,0.654884  
524288,1.164300  
1048576,2.134720  
2097152,4.380490  
4194304,8.719900  
8388608,17.663100  
16777216,38.439100  
33554432,72.113500  
  
=== merge with compute === [ only serial merge ]  
size,time (ms)  
2,0.058376  
4,0.057207  
8,0.062837  
16,0.058138  
32,0.062957  
64,0.061984  
128,0.059702  
256,0.062744  
512,0.062106  
1024,0.063178  
2048,0.071656  
4096,0.076726  
8192,0.095142  
16384,0.125700  
32768,0.195831  <----   
65536,0.339624  
131072,0.642904  
262144,1.202200  
524288,2.312620  
1048576,4.715070  
2097152,9.641130  
4194304,19.093200  
8388608,37.998500  
16777216,75.857500  
33554432,146.022000  
  
=== merge with compute === [ serial merge if total number of elements in merged arrays <= 32768 ]  
size,time (ms)  
2,0.058443  
4,0.063942  
8,0.063722  
16,0.064250  
32,0.060205  
64,0.060295  
128,0.064665  
256,0.065801  
512,0.066322  
1024,0.065031  
2048,0.072310  
4096,0.081465  
8192,0.094621  
16384,0.133090  
32768,0.202007 <----   
65536,0.284340  
131072,0.416663  
262144,0.648286  
524288,1.170250  
1048576,2.150280  
2097152,4.402920  
4194304,8.793680  
8388608,17.586700  
16777216,38.545000  
33554432,71.988800  
```  
  
boost::compute::merge() on a GPU (AMD Radeon HD7770 1GB / Capeverde):  
  
```  
=== merge with compute === [ without serial_merge ]  
size,time (ms)  
2,0.328907  
4,0.335311  
8,0.347259  
16,0.357102  
32,0.365325  
64,0.403433  
128,0.490579  
256,0.450781  
512,0.544281  
1024,0.952850  
2048,1.112820  <----   
4096,1.311940  
8192,1.522100  
16384,1.808190  
32768,2.086470  
65536,2.497830  
131072,2.580760  
262144,2.882870  
524288,2.946730  
1048576,3.831830  
2097152,10.249500  
4194304,25.594700  
8388608,36.070100  
16777216,57.824400  
33554432,106.386000  
  
=== merge with compute === [ serial merge if total number of elements in merged arrays <= 2048 ]  
size,time (ms)  
2,0.105126  
4,0.106514  
8,0.103712  
16,0.115010  
32,0.130541  
64,0.159552  
128,0.217301  
256,0.286446  
512,0.469712  
1024,0.350405  
2048,0.674871 <----   
4096,1.366230  
8192,1.519220  
16384,1.833550  
32768,2.160120  
65536,2.509000  
131072,2.604350  
262144,2.880300  
524288,2.940640  
1048576,3.862740  
2097152,11.878900  
4194304,26.006100  
8388608,35.588900  
16777216,60.163800  
33554432,106.400000  
```  
  
I also added `queue.finish()` to `perf_merge.cpp`, without execution time is measured wrongly as `boost::compute::merge()` is not synchronous.

---
