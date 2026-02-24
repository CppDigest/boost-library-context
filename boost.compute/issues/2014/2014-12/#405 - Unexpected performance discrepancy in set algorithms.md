# #405 - Unexpected performance discrepancy in set algorithms [Closed]

> Username: jbytheway  
> Created at: 2014-12-31 14:03:47 UTC  
> Updated at: 2017-06-14 16:16:46 UTC  
> Closed at: 2017-06-14 16:16:46 UTC  
> Url: https://github.com/boostorg/compute/issues/405  

On my system (using an NVidia GeForce GTX 660), I get a huge discrepancy between the performance of the various set_\* algorithms.  set_intersection and set_difference are _very_ fast; for example:  
  
```  
=== set_intersection with stl ===  
size,time (ms)  
2,0.000698  
4,0.000768  
8,0.000838  
16,0.000769  
32,0.001118  
64,0.000838  
128,0.001048  
256,0.001187  
512,0.001955  
1024,0.002723  
2048,0.004260  
4096,0.007193  
8192,0.012920  
16384,0.024305  
32768,0.047003  
65536,0.092749  
131072,0.184521  
262144,0.367295  
524288,0.735636  
1048576,1.607250  
2097152,3.364600  
4194304,7.629370  
8388608,13.173300  
16777216,26.183100  
33554432,52.908400  
=== set_intersection with compute ===  
size,time (ms)  
2,0.473732  
4,0.475967  
8,0.459625  
16,0.470938  
32,0.465002  
64,0.462488  
128,0.462348  
256,0.452012  
512,0.453619  
1024,0.491263  
2048,0.546367  
4096,0.694710  
8192,0.531910  
16384,0.493218  
32768,0.578773  
65536,0.523530  
131072,0.521364  
262144,0.824754  
524288,0.784455  
1048576,0.788227  
2097152,0.847941  
4194304,0.821052  
8388608,0.786342  
16777216,0.815046  
33554432,0.959408  
```  
  
This is about 50 times faster than STL.  
  
On the other hand, set_union and set_symmetric_difference are rather slow.  For example:  
  
```  
=== set_union with stl ===  
size,time (ms)  
2,0.000838  
4,0.000698  
8,0.000838  
16,0.000769  
32,0.000768  
64,0.000838  
128,0.000908  
256,0.001257  
512,0.001188  
1024,0.001676  
2048,0.002654  
4096,0.003912  
8192,0.007194  
16384,0.012571  
32768,0.024095  
65536,0.047073  
131072,0.092540  
262144,0.183892  
524288,0.367783  
1048576,0.784456  
2097152,1.638820  
4194304,3.238810  
8388608,6.572260  
16777216,13.024300  
33554432,26.403500  
=== set_union with compute ===  
size,time (ms)  
2,0.472266  
4,0.509141  
8,0.491751  
16,0.475758  
32,0.485187  
64,0.746532  
128,0.575072  
256,0.522341  
512,0.549720  
1024,0.664678  
2048,1.182900  
4096,1.145460  
8192,1.549360  
16384,1.812800  
32768,2.984660  
65536,5.497330  
131072,5.033870  
262144,4.798710  
524288,4.750030  
1048576,5.041970  
2097152,7.866690  
4194304,10.583900  
8388608,22.466900  
16777216,48.658900  
33554432,91.906000  
```  
  
This is about 3 times slower than STL.  The implementations of the algorithms are very similar, so I wonder what could cause this 100-fold difference in their performance.  
  
I suspect that the test data used in the performance test could be to blame.  I haven't looked, but it would be easy to generate data in such a way that the intersection of the two sets was extremely small, which might cause this (although even then I wouldn't expect quite such a huge discrepancy as this...)  
##

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-12-31 17:38:28 UTC  
> Url: https://github.com/boostorg/compute/issues/405#issuecomment-68456831  

@roshanr95 Can you look into this? What are your thoughts?

---

## Comment 2

> Username: roshanrags  
> Created at: 2014-12-31 20:41:05 UTC  
> Url: https://github.com/boostorg/compute/issues/405#issuecomment-68467990  

Um, there is an error in `perf_set_intersection.cpp`(by mistake, zero size range has been passed to `set_intersection`) which I'll correct...  
  
Can you post the performance results for `set_difference` as well? I get similar timings for all the set algorithms on my setup which are about 1-1.5 times that of STL algorithms.   
  
However, a huge difference exists between the set algorithms and the `includes` algorithm which is 80-100 times faster than STL. The only major difference seems to be far more memory operations done in set algorithms. I'm looking into optimising the set algorithms based on this...

---

## Comment 3

> Username: jbytheway  
> Created at: 2015-01-03 00:46:59 UTC  
> Url: https://github.com/boostorg/compute/issues/405#issuecomment-68576715  

Here are the set_difference timings:  
  
```  
=== set_difference with stl ===  
size,time (ms)  
2,0.000698  
4,0.000768  
8,0.000699  
16,0.000908  
32,0.000908  
64,0.000978  
128,0.000978  
256,0.001118  
512,0.001397  
1024,0.002444  
2048,0.003632  
4096,0.005937  
8192,0.011454  
16384,0.019696  
32768,0.038273  
65536,0.074451  
131072,0.151974  
262144,0.294520  
524288,0.586526  
1048576,1.274950  
2097152,2.673660  
4194304,5.288230  
8388608,10.705000  
16777216,21.418700  
33554432,42.538900  
=== set_difference with compute ===  
size,time (ms)  
2,0.449358  
4,0.456063  
8,0.494685  
16,0.506209  
32,0.472545  
64,0.471637  
128,0.458926  
256,0.459694  
512,0.446634  
1024,0.519129  
2048,0.490913  
4096,0.730958  
8192,0.518361  
16384,0.517453  
32768,0.497758  
65536,0.538056  
131072,0.657624  
262144,0.773351  
524288,0.796888  
1048576,0.785294  
2097152,0.768741  
4194304,0.798563  
8388608,0.819934  
16777216,0.846265  
33554432,1.018000  
```

---

## Comment 4

> Username: roshanrags  
> Created at: 2015-01-03 08:20:56 UTC  
> Url: https://github.com/boostorg/compute/issues/405#issuecomment-68587295  

Thanks, I'll look into this...

---

## Comment 5

> Username: jbytheway  
> Created at: 2015-01-03 17:31:07 UTC  
> Url: https://github.com/boostorg/compute/issues/405#issuecomment-68602475  

I fixed the perf_set_intersection bug locally and indeed now it's slow like set_union.  I checked, and set_difference has a similar bug.  Fixing that also made that slow.  
  
So I guess these discrepancies were just test bugs.  One is rather important because it's making the documentation performance page misleading.  
  
Still, it would of course be nice to have faster versions if you think you can manage to develop them :).

---

## Comment 6

> Username: kylelutz  
> Created at: 2015-01-21 04:08:34 UTC  
> Url: https://github.com/boostorg/compute/issues/405#issuecomment-70781650  

Could either of you submit a patch with the fixes? It would be good to get these benchmarks reporting the correct values. Thanks!

---

## Comment 7

> Username: jszuppe  
> Created at: 2017-04-25 15:05:48 UTC  
> Url: https://github.com/boostorg/compute/issues/405#issuecomment-297060143  

I'm not sure if https://github.com/boostorg/compute/issues/417 fixed it. I'll check and close if it's fixed.
