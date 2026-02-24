# #634 Scan for multicore CPUs [Merged]

> Username: jszuppe  
> Created at: 2016-07-25 19:35:36 UTC  
> Updated at: 2016-07-28 06:58:36 UTC  
> Merged at: 2016-07-28 04:13:05 UTC  
> Closed at: 2016-07-28 04:13:05 UTC  
> Url: https://github.com/boostorg/compute/pull/634  

This adds parallel scan for multicore CPUs.   
  
Unfortunately, it's not so much faster compared to `serial_scan` as you may think (at least in the simplest version of scan). Turns out that scan (partial_sum) is very memory bound operation and that's what limits the performance. If your scan is more computational intensive, like using multiplication as binary operator (instead of addition) or using `transform_iterator`, new implementation is much faster.  
  
There is a parameter, a knob for `serial_scan`, so if anyone prefers that it's easy to force it.   
### Benchmarks:  
  
1st test:  
- Integers (4 bytes)  
- Output is a different buffer than input  
- Addition as binary operator  
  
```  
=== partial_sum with stl ===  
size,time (ms)  
2,0.000133  
4,0.000142  
8,0.000134  
16,0.000138  
32,0.000143  
64,0.000160  
128,0.000182  
256,0.000231  
512,0.000318  
1024,0.000523  
2048,0.000905  
4096,0.001682  
8192,0.003258  
16384,0.006367  
32768,0.012164  
65536,0.024296  
131072,0.048424  
262144,0.096473  
524288,0.193014  
1048576,0.416432  
2097152,0.918738  
4194304,1.922140  
8388608,3.920030  
16777216,7.937260  
33554432,16.039500  
=== partial_sum with compute === [master]  
size,time (ms)  
2,0.024574  
4,0.024136  
8,0.024373  
16,0.024759  
32,0.023966  
64,0.024143  
128,0.024506  
256,0.024866  
512,0.024680  
1024,0.024701  
2048,0.024969  
4096,0.027860  
8192,0.029132  
16384,0.031862  
32768,0.037636  
65536,0.050316  
131072,0.072422  
262144,0.119914  
524288,0.262027  
1048576,0.454836  
2097152,0.913986  
4194304,1.948580  
8388608,3.856160  
16777216,7.720050  
33554432,15.435100  
=== partial_sum with compute ===  [pr_scan_on_cpu]  
size,time (ms)  
2,0.024880  
4,0.058831  
8,0.058118  
16,0.059220  
32,0.058700  
64,0.058996  
128,0.059202  
256,0.059067  
512,0.059627  
1024,0.059803  
2048,0.059085  
4096,0.061478  
8192,0.063023  
16384,0.063771  
32768,0.067773  
65536,0.075397  
131072,0.087798  
262144,0.118187  
524288,0.182055  
1048576,0.379444  
2097152,0.878609  
4194304,1.758310  
8388608,3.607530  
16777216,7.295870  
33554432,14.755100  
```  
  
2nd test:  
- Integers  
- Input is a counting_iterator (less reads)  
- Output is a different buffer than input  
- Addition as binary operator  
  
```  
=== partial_sum with stl ===  
size,time (ms)  
2,0.000132  
4,0.000134  
8,0.000137  
16,0.000136  
32,0.000142  
64,0.000159  
128,0.000180  
256,0.000226  
512,0.000313  
1024,0.000485  
2048,0.000849  
4096,0.001546  
8192,0.002999  
16384,0.005815  
32768,0.011389  
65536,0.022623  
131072,0.045092  
262144,0.089912  
524288,0.179732  
1048576,0.360201  
2097152,0.727174  
4194304,1.464850  
8388608,2.940750  
16777216,5.890150  
33554432,11.794500  
=== partial_sum with compute === [master]  
size,time (ms)  
2,0.022248  
4,0.022358  
8,0.022066  
16,0.022683  
32,0.022127  
64,0.021975  
128,0.022936  
256,0.022848  
512,0.022470  
1024,0.023087  
2048,0.023167  
4096,0.024145  
8192,0.025960  
16384,0.028900  
32768,0.034864  
65536,0.045685  
131072,0.067478  
262144,0.110432  
524288,0.196010  
1048576,0.368173  
2097152,0.775608  
4194304,1.448320  
8388608,2.817400  
16777216,5.583590  
33554432,11.205700  
=== partial_sum with compute === [pr_scan_on_cpu]  
size,time (ms)  
2,0.022960  
4,0.055819  
8,0.050561  
16,0.055357  
32,0.055002  
64,0.055797  
128,0.054942  
256,0.054740  
512,0.055719  
1024,0.055811  
2048,0.056499  
4096,0.057108  
8192,0.058888  
16384,0.061926  
32768,0.065484  
65536,0.074273  
131072,0.090985  
262144,0.127229  
524288,0.195081  
1048576,0.331918  
2097152,0.611572  
4194304,1.292590  
8388608,2.716510  
16777216,5.600410  
33554432,11.391500  
```  
  
3rd test:  
- Integers  
- Output is a different buffer than input  
- Multiplication as a binary operator  
  
```  
=== partial_sum with stl ===  
size,time (ms)  
2,0.000133  
4,0.000133  
8,0.000135  
16,0.000142  
32,0.000152  
64,0.000177  
128,0.000226  
256,0.000325  
512,0.000523  
1024,0.000917  
2048,0.001710  
4096,0.003286  
8192,0.006449  
16384,0.012752  
32768,0.025388  
65536,0.050613  
131072,0.101045  
262144,0.201858  
524288,0.403534  
1048576,0.810035  
2097152,1.621640  
4194304,3.246170  
8388608,6.496410  
16777216,12.992500  
33554432,25.989900  
=== partial_sum with compute === [master]  
size,time (ms)  
2,0.024059  
4,0.025767  
8,0.024349  
16,0.024042  
32,0.024415  
64,0.025828  
128,0.024376  
256,0.024326  
512,0.024497  
1024,0.025059  
2048,0.027538  
4096,0.029281  
8192,0.032379  
16384,0.038732  
32768,0.051514  
65536,0.077118  
131072,0.127380  
262144,0.229353  
524288,0.433761  
1048576,0.839425  
2097152,1.653850  
4194304,3.286110  
8388608,6.531480  
16777216,13.031700  
33554432,26.011600  
=== partial_sum with compute === [pr_scan_on_cpu]  
size,time (ms)  
2,0.025172  
4,0.058407  
8,0.058469  
16,0.059471  
32,0.058983  
64,0.058180  
128,0.059334  
256,0.054043  
512,0.059721  
1024,0.058306  
2048,0.059765  
4096,0.060937  
8192,0.065578  
16384,0.069580  
32768,0.076213  
65536,0.080058  
131072,0.102037  
262144,0.178779  
524288,0.225017  
1048576,0.416907  
2097152,0.884288  
4194304,1.775860  
8388608,3.635050  
16777216,7.364730  
33554432,14.820500  
```  
  
4th test:  
- Integers  
- Output is a different buffer than input,  
- Input is a transform iterator with x^6 as unary function  
- Addition as binary operator  
  
``` cpp  
BOOST_COMPUTE_FUNCTION(int_, function, (int_ x),  
{  
  return x * x * x * x * x * x;  
});  
  
boost::compute::partial_sum(  
  boost::compute::make_transform_iterator(device_vector.begin(), function),  
  boost::compute::make_transform_iterator(device_vector.end(), function),  
  device_res.begin(),  
  queue  
);  
```  
  
```  
=== partial_sum with stl ===  
size,time (ms)  
2,0.000134  
4,0.000134  
8,0.000138  
16,0.000143  
32,0.000155  
64,0.000247  
128,0.000315  
256,0.000446  
512,0.000711  
1024,0.001241  
2048,0.002307  
4096,0.004441  
8192,0.006463  
16384,0.012770  
32768,0.025404  
65536,0.050651  
131072,0.101047  
262144,0.201890  
524288,0.403569  
1048576,0.810233  
2097152,1.624730  
4194304,3.256530  
8388608,6.510080  
16777216,13.027200  
33554432,26.051600  
=== partial_sum with compute === [master]  
size,time (ms)  
2,0.027124  
4,0.027188  
8,0.026980  
16,0.027497  
32,0.027166  
64,0.026947  
128,0.027341  
256,0.027279  
512,0.027517  
1024,0.027665  
2048,0.030651  
4096,0.032276  
8192,0.035523  
16384,0.041941  
32768,0.054685  
65536,0.081043  
131072,0.130577  
262144,0.231692  
524288,0.436274  
1048576,0.843986  
2097152,1.655320  
4194304,3.277980  
8388608,6.551160  
16777216,13.076400  
33554432,26.125700  
=== partial_sum with compute === [pr_scan_on_cpu]  
size,time (ms)  
2,0.028195  
4,0.064608  
8,0.064084  
16,0.063037  
32,0.064090  
64,0.063634  
128,0.063385  
256,0.065218  
512,0.064119  
1024,0.064406  
2048,0.065563  
4096,0.065181  
8192,0.069219  
16384,0.071021  
32768,0.072427  
65536,0.086480  
131072,0.110898  
262144,0.154487  
524288,0.242798  
1048576,0.523602  
2097152,0.957052  
4194304,1.960660  
8388608,4.072160  
16777216,8.256710  
33554432,16.574400  
```

---

## Comment 1

> Username: coveralls  
> Created_at: 2016-07-25 20:28:55 UTC  
> Url: https://github.com/boostorg/compute/pull/634#issuecomment-235074550  

[![Coverage Status](https://coveralls.io/builds/7155228/badge)](https://coveralls.io/builds/7155228)  
  
Coverage increased (+0.2%) to 83.344% when pulling **08a28b4a23cce39c5f87b2751d95959ee8075e17 on haahh:pr_scan_on_cpu** into **1d2f8cfbd967626c6fa905d9fcf6ca4667afda47 on boostorg:develop**.

---

## Comment 2

> Username: coveralls  
> Created_at: 2016-07-25 23:09:28 UTC  
> Url: https://github.com/boostorg/compute/pull/634#issuecomment-235113993  

[![Coverage Status](https://coveralls.io/builds/7158188/badge)](https://coveralls.io/builds/7158188)  
  
Coverage increased (+0.2%) to 83.344% when pulling **979ec52351771afd374bb8e77f0d6739dc1e5f9c on haahh:pr_scan_on_cpu** into **1d2f8cfbd967626c6fa905d9fcf6ca4667afda47 on boostorg:develop**.

---

## Comment 3

> Username: kylelutz  
> Created_at: 2016-07-28 04:13:10 UTC  
> Url: https://github.com/boostorg/compute/pull/634#issuecomment-235796415  

Looks good! Merged!

---
