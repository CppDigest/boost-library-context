# #150 Use SSE2 implementation when `uint128_t` is available [Merged]

> Username: Lastique  
> Created at: 2024-04-23 17:28:39 UTC  
> Updated at: 2024-04-23 23:36:46 UTC  
> Merged at: 2024-04-23 23:11:43 UTC  
> Closed at: 2024-04-23 23:11:43 UTC  
> Url: https://github.com/boostorg/uuid/pull/150  

The SSE-optimized routines are faster than `uint128_t`-based ones. Based on my tests on i7 12700K, with gcc 11.4, SSE4 `operator==` is 13% faster and `operator<` is 8% faster.  
  
See the attached benchmark:  
[uuid_operators2.cpp.gz](https://github.com/boostorg/uuid/files/15082637/uuid_operators2.cpp.gz)  
  
Compiler command line:  
  
```  
g++ -I. -std=gnu++17 -O3 -march=nehalem -o uuid_operators2 uuid_operators2.cpp  
```  
  
Test output on my machine:  
  
```  
equal_uint128: Duration: 64.463747 ms, 1551259500.940893 calls per second, 23670.341506 MiB/s  
less_uint128: Duration: 89.332442 ms, 1119414154.154658 calls per second, 17080.904452 MiB/s  
equal_x86: Duration: 56.973550 ms, 1755200439.502190 calls per second, 26782.233269 MiB/s  
less_x86: Duration: 82.242024 ms, 1215923382.430374 calls per second, 18553.518409 MiB/s  
less_x86_avx512: Duration: 68.365865 ms, 1462718273.220122 calls per second, 22319.309589 MiB/s  
```  
  
The AVX-512 version is the further optimized version for AVX-512 that I'm planning to integrate into Boost.UUID and submit separately, when https://github.com/boostorg/uuid/pull/149 is merged.  
  
Note: `test_perf_avx512` is exactly the same as `test_perf`, only marked to target AVX-512. Without it, `less_x86_avx512` would not be inlined, and the test results would be unfairly low.

---
