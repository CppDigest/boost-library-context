# #25 - Improve reverse() algorithm performance [Closed]

> Username: kylelutz  
> Created at: 2013-12-21 19:34:29 UTC  
> Updated at: 2015-03-10 03:41:55 UTC  
> Closed at: 2015-03-10 03:41:53 UTC  
> Url: https://github.com/boostorg/compute/issues/25  

Take a tile-based approach, reverse blocks in local memory and then write to global memory.

---

## Comment 1

> Username: roshanrags  
> Created at: 2014-03-14 09:27:51 UTC  
> Url: https://github.com/boostorg/compute/issues/25#issuecomment-37629396  

Any performance gain would mostly come from the fact that global memory is faster when memory locations are accessed contiguously. Will this outweigh the extra reads/writes done on local memory?

---

## Comment 2

> Username: kylelutz  
> Created at: 2014-03-14 15:23:16 UTC  
> Updated at: 2014-03-14 17:28:00 UTC  
> Url: https://github.com/boostorg/compute/issues/25#issuecomment-37658953  

I think there is may be a gain in pulling tiles of global memory to local memory, reversing it in a work-group, and writing the reversed tile back to global memory (which would then be contiguous). But, like any performance improvements, it should be benchmarked. I think a good first step would be to write a `perf_reverse` test (similar to the other algorithm `perf_*` tests in the `perf/` directory) which measures the current performance and then can be used to evaluate any future improvements.

---

## Comment 3

> Username: jszuppe  
> Created at: 2015-03-08 19:22:04 UTC  
> Updated at: 2015-03-09 21:36:37 UTC  
> Url: https://github.com/boostorg/compute/issues/25#issuecomment-77768867  

I've implemented local memory solution (mentioned above) - https://github.com/haahh/compute/commit/7479fa4e73c7272d4f1e31cbf05528558ac16afc. It is just first version and ofc needs refactor etc.  
  
More important is that the performance is virtually the same (sometimes lower, sometimes higher) compared to current solution (only global memory) on my device (AMD Radeon HD7770). Also global memory solution is more stable in terms of performance. When buffer is small (<10000) difference is noticeable to the disadvantage of local memory solution.  
  
I tested it with CodeXL GPU Performance counters (running perf_reverse), 10 tests for each version of kernel.  
  
I think it's because with current solution reading and writing from global memory is already coalesced/contiguous/adjacent.   
  
``` c++  
   decl<cl_uint>("i") << " = get_global_id(0);\n" <<  
   decl<cl_uint>("j") << " = size - get_global_id(0) - 1;\n" <<  
   decl<value_type>("tmp") << "=" << first[var<cl_uint>("i")] << ";\n" <<  
   first[var<cl_uint>("i")] << "=" << first[var<cl_uint>("j")] << ";\n" <<  
   first[var<cl_uint>("j")] << "= tmp;\n";  
```  
  
I mean this part `decl<value_type>("tmp") << "=" << first[var<cl_uint>("i")] << ";\n"` is obviously coalesced, textbook example. And this part `first[var<cl_uint>("i")] << "=" << first[var<cl_uint>("j")] << ";\n"` is read/write in reversed manner. Device OpenCL compiler and/or device drivers (I dont know details) know what to do so it would be coalesced.  
  
Probably with older devices (NVIDIA GPU with CC 1.0/1.1) using local memory would make some difference. I will test it later.   
**Update:** I cannot test it  - my laptop's NVIDIA GPU has CC 1.2.  
**Update 2:** New commit - code refactor, fix, tests now run on local kernel.  
**Update 3:**  
  
I've just discovered pref.py script... :neutral_face: It's certainly better way to test performance compared to what I've been doing, but results are the same - devices know how to handle global memory access pattern present in current solution.   
  
current master performance (20 trials)  
  
```  
=== reverse with compute ===  
size,time (ms)  
2,0.089776  
4,0.089753  
8,0.086176  
16,0.092829  
32,0.090977  
64,0.090615  
128,0.090403  
256,0.090353  
512,0.089974  
1024,0.089263  
2048,0.090476  
4096,0.091755  
8192,0.096906  
16384,0.104873  
32768,0.122258  
65536,0.157439  
131072,0.217474  
262144,0.396446  
524288,0.787806  
1048576,0.274227  
2097152,0.453932  
4194304,0.912999  
8388608,1.530450  
16777216,2.845040  
33554432,5.477610  
```  
  
https://github.com/haahh/compute/commit/7479fa4e73c7272d4f1e31cbf05528558ac16afc performance (20 trials):  
  
```  
=== reverse with compute ===  
size,time (ms)  
2,0.110501  
4,0.111839  
8,0.112056  
16,0.111535  
32,0.112302  
64,0.112528  
128,0.113221  
256,0.112125  
512,0.116040  
1024,0.115111  
2048,0.114388  
4096,0.113263  
8192,0.117490  
16384,0.128465  
32768,0.142770  
65536,0.178416  
131072,0.250299  
262144,0.415767  
524288,0.757610  
1048576,0.294105  
2097152,0.541047  
4194304,0.913123  
8388608,1.556500  
16777216,2.902590  
33554432,5.510650  
```

---

## Comment 4

> Username: kylelutz  
> Created at: 2015-03-10 03:41:52 UTC  
> Url: https://github.com/boostorg/compute/issues/25#issuecomment-77990974  

I guess we should stick with the current naive implementation as it performs well. Thanks for trying this out and getting the performance numbers!
