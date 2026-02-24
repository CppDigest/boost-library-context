# #626 Transform for multicore CPUs [Merged]

> Username: jszuppe  
> Created at: 2016-07-06 17:33:02 UTC  
> Updated at: 2016-07-12 03:16:53 UTC  
> Merged at: 2016-07-12 03:16:53 UTC  
> Closed at: 2016-07-12 03:16:53 UTC  
> Url: https://github.com/boostorg/compute/pull/626  

This improves `transform()` (`copy_on_device()`) performance for multicore CPUs.  
  
```  
=== saxpy with stl ===  
size,time (ms)  
2,0.000160  
4,0.000173  
8,0.000200  
16,0.000214  
32,0.000225  
64,0.000260  
128,0.000944  
256,0.001346  
512,0.002352  
1024,0.004416  
2048,0.008575  
4096,0.012101  
8192,0.023922  
16384,0.048170  
524288,0.962686  
1048576,1.929100  
2097152,3.858070  
4194304,7.719810  
8388608,15.428000  
16777216,30.873400  
33554432,61.704100  
  
[master]  
  
=== saxpy with compute (CPU, Intel i5-6600K, 4/4 cores/threads) ===  
size,time (ms)  
2,0.024186  
4,0.024106  
8,0.024319  
16,0.023895  
32,0.024225  
64,0.024235  
128,0.024309  
256,0.027449  
512,0.027538  
1024,0.028694  
2048,0.029796  
4096,0.031381  
8192,0.028765  
16384,0.039954  
32768,0.045763  
65536,0.063319  
131072,0.087354  
262144,0.130503  
524288,0.292780  
1048576,0.643207  
2097152,1.271910  
4194304,2.321300  
8388608,4.728590  
16777216,9.394120  
33554432,18.629600  
  
[pr_transform_cpu]  
  
=== saxpy with compute (CPU, Intel i5-6600K, 4/4 cores/threads) ===  
size,time (ms)  
2,0.029375  
4,0.028846  
8,0.029399  
16,0.028959  
32,0.029304  
64,0.029015  
128,0.028879  
256,0.029418  
512,0.029383  
1024,0.029599  
2048,0.029605  
4096,0.029974  
8192,0.030838  
16384,0.032885  
32768,0.036190  
65536,0.044674  
131072,0.053115  
262144,0.073374  
524288,0.222535  
1048576,0.468357  
2097152,1.182020  
4194304,2.110340  
8388608,4.113700  
16777216,8.209680  
33554432,16.529000   
  
No difference in GPU performance.  
```  
  
This update is turned off for Apple OpenCL Platform as its compiler for CPU does not work correctly and can not compile kernel  in `copy_on_device_cpu()` - see https://travis-ci.org/boostorg/compute/jobs/142560405. In other words, for Apple we keep the old performance.

---

## Comment 1

> Username: coveralls  
> Created_at: 2016-07-06 20:33:55 UTC  
> Url: https://github.com/boostorg/compute/pull/626#issuecomment-230897648  

[![Coverage Status](https://coveralls.io/builds/6895832/badge)](https://coveralls.io/builds/6895832)  
  
Coverage decreased (-0.3%) to 80.277% when pulling **f58352fb44f642f32b22fa71d2b3f89f8fee7f4a on haahh:pr_transform_cpu** into **a3f72e61916abb1a9d3213adff4d07888783f17a on boostorg:develop**.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2016-07-08 04:32:03 UTC  
> Updated_at: 2016-07-10 14:18:10 UTC  
> Url: https://github.com/boostorg/compute/pull/626#discussion_r70024248  

Hmm, I wonder if it would be better to use run-time dispatching (something like `device.platform().name() == "Apple"`) rather than compile-time dispatching (probably unlikely, but something that is compiled on Apple host may be executed on a non-Apple compute device or vice-versa)?

---

## Comment 3

> Username: kylelutz  
> Created_at: 2016-07-08 04:37:07 UTC  
> Updated_at: 2016-07-10 14:18:10 UTC  
> Url: https://github.com/boostorg/compute/pull/626#discussion_r70024430  

Either way, could you add a comment explaining why we have this special case for Apple?

---

## Comment 4

> Username: jszuppe  
> Created_at: 2016-07-08 18:33:54 UTC  
> Updated_at: 2016-07-10 14:18:10 UTC  
> Url: https://github.com/boostorg/compute/pull/626#discussion_r70120095  

> `device.platform().name() == "Apple"`  
  
I can do this. I was just worried about using those kind of conditions in code. `__APPLE__` seemed more reliable.  
  
> Either way, could you add a comment explaining why we have this special case for Apple?  
  
Sure. Apple OpenCL platform (at least its compiler for the CPU) has some kind of bug that makes some kernels impossible to compile. I've figured out that conditions for the bug to show itself are: loop with condition with two variables (e.g. comparing two variables `index < last_index`; if you have `index < 10000` everything is fine) + in this loop you have to write constant or result of function into a buffer (if you're copying value from one buffer to another, e.g. `buf1[idx] = buf2[idx];`, bug does not appear). And that's why some tests work and some don't.   
  
Examples: [_buf0[i]=42;](https://travis-ci.org/boostorg/compute/jobs/142560405#L2309), [_buf0[i]=ret42();](https://travis-ci.org/boostorg/compute/jobs/142560405#L2365).  
  
The same bug is a problem for vexcl - https://github.com/ddemidov/vexcl/issues/92.

---

## Comment 5

> Username: jszuppe  
> Created_at: 2016-07-09 12:17:53 UTC  
> Url: https://github.com/boostorg/compute/pull/626#issuecomment-231531680  

I wonder if there is a way to mock `device` for tests, so it returns `device::gpu` as its type while actually being a CPU device. That way we would be able to run both CPU and GPU dedicated algorithms and increase the coverage. It should work perfectly fine on all OpenCL platforms except Apple platform.

---

## Comment 6

> Username: coveralls  
> Created_at: 2016-07-10 14:05:04 UTC  
> Url: https://github.com/boostorg/compute/pull/626#issuecomment-231590733  

[![Coverage Status](https://coveralls.io/builds/6941547/badge)](https://coveralls.io/builds/6941547)  
  
Coverage decreased (-0.2%) to 80.394% when pulling **2ce959a4c7ec8d53d32eab948b5b62d548944727 on haahh:pr_transform_cpu** into **a3f72e61916abb1a9d3213adff4d07888783f17a on boostorg:develop**.

---

## Comment 7

> Username: coveralls  
> Created_at: 2016-07-10 15:29:35 UTC  
> Url: https://github.com/boostorg/compute/pull/626#issuecomment-231594759  

[![Coverage Status](https://coveralls.io/builds/6941856/badge)](https://coveralls.io/builds/6941856)  
  
Coverage decreased (-0.3%) to 80.287% when pulling **a10e7d31f746ecc85db59a12e2c8a2aa7037e93a on haahh:pr_transform_cpu** into **a3f72e61916abb1a9d3213adff4d07888783f17a on boostorg:develop**.

---
