# #625 Transform for multicore CPUs [Closed]

> Username: jszuppe  
> Created at: 2016-07-05 15:27:52 UTC  
> Updated at: 2016-07-05 20:54:06 UTC  
> Closed at: 2016-07-05 20:54:06 UTC  
> Url: https://github.com/boostorg/compute/pull/625  

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

---

## Comment 1

> Username: pavanky  
> Created_at: 2016-07-05 15:42:31 UTC  
> Updated_at: 2016-07-05 19:28:40 UTC  
> Url: https://github.com/boostorg/compute/pull/625#discussion_r69586431  

This is the same for every thread. You can pass this as an additional parameter to avoid a divide.

---

## Comment 2

> Username: jszuppe  
> Created_at: 2016-07-05 15:54:40 UTC  
> Updated_at: 2016-07-05 19:28:40 UTC  
> Url: https://github.com/boostorg/compute/pull/625#discussion_r69588808  

It does not make a difference in terms of performance.

---

## Comment 3

> Username: coveralls  
> Created_at: 2016-07-05 16:41:14 UTC  
> Url: https://github.com/boostorg/compute/pull/625#issuecomment-230533178  

[![Coverage Status](https://coveralls.io/builds/6874551/badge)](https://coveralls.io/builds/6874551)  
  
Coverage decreased (-0.3%) to 80.277% when pulling **8cccef134a55f4e949da00ff8f49fd66be5beb0b on haahh:pr_transform_cpu** into **e5a9017d6952f97e7d4723f34aa6264106e3303e on boostorg:develop**.

---

## Comment 4

> Username: coveralls  
> Created_at: 2016-07-05 19:01:24 UTC  
> Url: https://github.com/boostorg/compute/pull/625#issuecomment-230571055  

[![Coverage Status](https://coveralls.io/builds/6877133/badge)](https://coveralls.io/builds/6877133)  
  
Coverage decreased (-0.3%) to 80.28% when pulling **eb6c7bf41500e26b3350896f766571840f320d88 on haahh:pr_transform_cpu** into **e5a9017d6952f97e7d4723f34aa6264106e3303e on boostorg:develop**.

---

## Comment 5

> Username: coveralls  
> Created_at: 2016-07-05 20:40:44 UTC  
> Url: https://github.com/boostorg/compute/pull/625#issuecomment-230596309  

[![Coverage Status](https://coveralls.io/builds/6878940/badge)](https://coveralls.io/builds/6878940)  
  
Coverage decreased (-0.3%) to 80.28% when pulling **26aba9bb47b61f62eb57ca922f6da6e811c2adeb on haahh:pr_transform_cpu** into **e5a9017d6952f97e7d4723f34aa6264106e3303e on boostorg:develop**.

---
