# #433 reverse_copy() performance improvement  [Merged]

> Username: jszuppe  
> Created at: 2015-03-11 20:53:43 UTC  
> Updated at: 2015-03-13 23:45:02 UTC  
> Merged at: 2015-03-13 23:26:17 UTC  
> Closed at: 2015-03-13 23:26:17 UTC  
> Url: https://github.com/boostorg/compute/pull/433  

I've added separate kernel for reverse_copy algorithm. Now copying and reversing is being done by one kernel, instead of performing it in two steps by using copy() and reverse() algorithms. Also thanks to this change, occupancy should be higher (it's important for smaller buffers) as global work size isn't halved like in reverse algorithm().   
  
I also added `perf_` benchmarks for reverse_copy() for compute, stl and thrust. I do not enclose results of `perf_thrust_reverse_copy` benchmark, but I've tested it.  
  
STL reverse_copy performance [10 trials]:  
  
```  
=== reverse_copy with stl ===  
size,time (ms)  
2,0.000304  
4,0.000355  
8,0.000459  
16,0.000676  
32,0.001086  
64,0.001918  
128,0.003568  
256,0.006875  
512,0.013491  
1024,0.026723  
2048,0.053190  
4096,0.106123  
8192,0.212049  
16384,0.424382  
32768,0.848584  
65536,1.700160  
131072,3.413210  
262144,6.834150  
524288,13.685900  
1048576,27.549400  
2097152,55.241600  
4194304,110.483000  
8388608,221.186000  
16777216,440.918000  
33554432,882.330000  
```  
  
Boost.Compute reverse_copy performance, master version (copying and then reversing) [10 trials]:   
  
```  
=== reverse_copy with compute ===  
size,time (ms)  
2,0.106061  
4,0.109403  
8,0.104448  
16,0.104588  
32,0.108357  
64,0.104847  
128,0.104672  
256,0.105536  
512,0.104203  
1024,0.105104  
2048,0.106114  
4096,0.108994  
8192,0.112387  
16384,0.122060  
32768,0.140064  
65536,0.183763  
131072,0.332581  
262144,0.746508  
524288,1.316890  
1048576,2.586030  
2097152,0.924608  
4194304,1.682100  
8388608,2.711950  
16777216,5.293470  
33554432,10.521900  
```  
  
https://github.com/haahh/compute/commit/f95b1ee682a66906e8a6ae175aa68261bdaf7972 version [10 trials]:  
  
```  
=== reverse_copy with compute ===  
size,time (ms)  
2,0.091911  
4,0.091229  
8,0.091228  
16,0.090202  
32,0.092085  
64,0.091543  
128,0.091647  
256,0.090898  
512,0.091761  
1024,0.092912  
2048,0.094556  
4096,0.094220  
8192,0.097956  
16384,0.106091  
32768,0.123238  
65536,0.159864  
131072,0.222912  
262144,0.392484  
524288,0.758905  
1048576,1.367340  
2097152,0.519979  
4194304,0.897952  
8388608,1.631120  
16777216,2.624290  
33554432,5.076140  
  
```

---

## Comment 1

> Username: coveralls  
> Created_at: 2015-03-11 21:22:47 UTC  
> Url: https://github.com/boostorg/compute/pull/433#issuecomment-78374805  

[![Coverage Status](https://coveralls.io/builds/2094077/badge)](https://coveralls.io/builds/2094077)  
  
Coverage increased (+0.01%) to 94.97% when pulling **f95b1ee682a66906e8a6ae175aa68261bdaf7972 on haahh:dev_reverse_copy** into **aa15cd6ac4cd76bf7129a0c653dde2e70e8c089c on kylelutz:develop**.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2015-03-13 22:44:23 UTC  
> Updated_at: 2015-03-13 23:16:28 UTC  
> Url: https://github.com/boostorg/compute/pull/433#discussion_r26428580  

You should put your name here, you're the author :-).

---

## Comment 3

> Username: kylelutz  
> Created_at: 2015-03-13 22:45:15 UTC  
> Updated_at: 2015-03-13 23:16:28 UTC  
> Url: https://github.com/boostorg/compute/pull/433#discussion_r26428617  

Same as above.

---

## Comment 4

> Username: kylelutz  
> Created_at: 2015-03-13 22:45:29 UTC  
> Updated_at: 2015-03-13 23:16:28 UTC  
> Url: https://github.com/boostorg/compute/pull/433#discussion_r26428628  

Same as above.

---

## Comment 5

> Username: kylelutz  
> Created_at: 2015-03-13 22:47:47 UTC  
> Url: https://github.com/boostorg/compute/pull/433#issuecomment-79510014  

Looks great! Left a few small comments, after those it should be good to merge. Thanks!

---

## Comment 6

> Username: jszuppe  
> Created_at: 2015-03-13 23:22:17 UTC  
> Url: https://github.com/boostorg/compute/pull/433#issuecomment-79522370  

OK, great! I fixed it.   
  
Do you want me to squash this commit  [Fixing author - https://github.com/kylelutz/compute/commit/5aace2dde96a1b7f51acdf871a942e6443f73807] into the previous one? I don't really familiar with github pull-request best practices and whether github will interpret it [git history rewriting] correctly.

---

## Comment 7

> Username: kylelutz  
> Created_at: 2015-03-13 23:25:46 UTC  
> Url: https://github.com/boostorg/compute/pull/433#issuecomment-79523701  

Multiple commits works fine. GitHub will also handle rebasing/squashing/etc correctly so whichever way you want to structure your pull-request is fine with me.

---

## Comment 8

> Username: kylelutz  
> Created_at: 2015-03-13 23:26:24 UTC  
> Url: https://github.com/boostorg/compute/pull/433#issuecomment-79524009  

Merged! Thanks!

---

## Comment 9

> Username: coveralls  
> Created_at: 2015-03-13 23:31:20 UTC  
> Url: https://github.com/boostorg/compute/pull/433#issuecomment-79528416  

[![Coverage Status](https://coveralls.io/builds/2111591/badge)](https://coveralls.io/builds/2111591)  
  
Coverage increased (+0.01%) to 94.97% when pulling **5aace2dde96a1b7f51acdf871a942e6443f73807 on haahh:dev_reverse_copy** into **aa15cd6ac4cd76bf7129a0c653dde2e70e8c089c on kylelutz:develop**.

---
