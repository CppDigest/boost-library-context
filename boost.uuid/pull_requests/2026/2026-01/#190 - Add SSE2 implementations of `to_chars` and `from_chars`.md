# #190 Add SSE2 implementations of `to_chars` and `from_chars` [Merged]

> Username: Lastique  
> Created at: 2026-01-09 15:42:02 UTC  
> Updated at: 2026-01-13 16:17:40 UTC  
> Merged at: 2026-01-12 14:54:55 UTC  
> Closed at: 2026-01-12 14:54:55 UTC  
> Url: https://github.com/boostorg/uuid/pull/190  

1. This adds SSE2 code path to to_chars_x86.hpp. The performance effect on Intel Golden Cove (Core i7-12700K), gcc 13.3, in millions of `to_chars()` calls per second with a 16-byte aligned output buffer:  
  
```  
Char     | Generic | SSE2            | SSE4.1           | AVX2             | AVX10.1  
=========+=========+=================+==================+==================+=================  
char     | 202.314 | 564.857 (2.79x) | 1194.772 (5.91x) | 1192.094 (5.89x) | 1191.838 (5.89x)  
char16_t | 188.532 | 457.281 (2.43x) |  795.798 (4.22x) |  935.016 (4.96x) |  938.368 (4.98x)  
char32_t | 193.151 | 345.612 (1.79x) |  489.620 (2.53x) |  688.829 (3.57x) |  689.617 (3.57x)  
```  
  
Test source code: [uuid_to_chars_perftest.cpp](https://github.com/user-attachments/files/24530146/uuid_to_chars_perftest.cpp)  
  
Compile with:  
  
```  
g++ -O3 -march=x86-64 -DBOOST_UUID_NO_SIMD -std=gnu++17 -I. -o uuid_to_chars_perftest_generic uuid_to_chars_perftest.cpp  
g++ -O3 -march=x86-64 -std=gnu++17 -I. -o uuid_to_chars_perftest_sse2 uuid_to_chars_perftest.cpp  
g++ -O3 -march=nehalem -std=gnu++17 -I. -o uuid_to_chars_perftest_sse41 uuid_to_chars_perftest.cpp  
g++ -O3 -march=haswell -std=gnu++17 -I. -o uuid_to_chars_perftest_avx2 uuid_to_chars_perftest.cpp  
g++ -O3 -march=icelake-client -std=gnu++17 -I. -o uuid_to_chars_perftest_avx10_1 uuid_to_chars_perftest.cpp  
```  
  
Full results: [uuid_to_chars_perftest.txt](https://github.com/user-attachments/files/24530609/uuid_to_chars_perftest.txt)  
  
2. This adds SSE2 and SSSE3 code paths to from_chars_x86.hpp. The performance effect on Intel Golden Cove (Core i7-12700K), gcc 13.3, in millions of successful from_chars() calls per second:  
  
```  
Char     | Generic | SSE2            | SSSE3           | SSE4.1          | AVX2            | AVX512v1  
=========+=========+=================+=================+=================+=================+================  
char     |  40.475 | 327.791 (8.10x) | 465.857 (11.5x) | 555.346 (13.7x) | 504.648 (12.5x) | 539.700 (13.3x)  
char16_t |  38.757 | 292.048 (7.54x) | 401.117 (10.3x) | 478.574 (12.3x) | 426.188 (11.0x) | 416.205 (10.7x)  
char32_t |  50.200 | 150.900 (3.01x) | 204.588 (4.08x) | 389.882 (7.77x) | 359.591 (7.16x) | 349.663 (6.97x)  
```  
  
Test source code: [uuid_from_chars_perftest.cpp](https://github.com/user-attachments/files/24530562/uuid_from_chars_perftest.cpp)  
  
Compile with:  
  
```  
g++ -O3 -march=x86-64 -DBOOST_UUID_NO_SIMD -std=gnu++17 -I. -o uuid_from_chars_perftest_generic uuid_from_chars_perftest.cpp  
g++ -O3 -march=x86-64 -std=gnu++17 -I. -o uuid_from_chars_perftest_sse2 uuid_from_chars_perftest.cpp  
g++ -O3 -march=core2 -std=gnu++17 -I. -o uuid_from_chars_perftest_ssse3 uuid_from_chars_perftest.cpp  
g++ -O3 -march=nehalem -std=gnu++17 -I. -o uuid_from_chars_perftest_sse41 uuid_from_chars_perftest.cpp  
g++ -O3 -march=haswell -std=gnu++17 -I. -o uuid_from_chars_perftest_avx2 uuid_from_chars_perftest.cpp  
g++ -O3 -march=skylake-avx512 -std=gnu++17 -I. -o uuid_from_chars_perftest_avx512v1 uuid_from_chars_perftest.cpp  
g++ -O3 -march=icelake-client -std=gnu++17 -I. -o uuid_from_chars_perftest_avx10_1 uuid_from_chars_perftest.cpp  
```  
  
Full results: [uuid_from_chars_perftest.txt](https://github.com/user-attachments/files/24530618/uuid_from_chars_perftest.txt)  
  
In addition, the workarounds to avoid `(v)pblendvb` instructions have been extended to Intel Haswell and Broadwell, as these microarchitectures have poor performance with these instructions (including the SSE4.1 `pblendvb`).  
  
Two new experimental control macros added: `BOOST_UUID_FROM_CHARS_X86_SLOW_PBLENDVB` and `BOOST_UUID_FROM_CHARS_X86_USE_PBLENDVB`. The former indicates that `(v)pblendvb` instructions are slow and should be avoided on the target microarchitectures. The latter indicates that `(v)pblendvb` should be used by the implementation. The latter macro is derived from the former and takes precedence. As before, these macros can be used for experimenting and fine tuning performance for specific target CPUs. By default, `BOOST_UUID_FROM_CHARS_X86_SLOW_PBLENDVB` is defined for Haswell/Broadwell or if AVX is detected.  
  
Also, made selection between blend-based and shuffle-based character code conversion in various places unified, controlled by a single internal macro `BOOST_UUID_DETAIL_FROM_CHARS_X86_USE_BLENDS`.  
  
NOTE: The tests used in this PR were modified, so the performance numbers presented here may not be comparable with the previous PRs.  
  
3. Updated CI and test Jamfile/CMakeLists.txt to compile tests without running, if the CPU does not support the required ISA extensions. This allows for checking whether the code at least compiles.  
  
As a side effect of this, CMakeLists.txt no longer uses `boost_test_jamfile` as it doesn't support custom logic for test type selection.  
  
4. Added GitHub Actions jobs for SSSE3 and AVX targets. The targets verify the respective code paths in SIMD algorithms. The added SSE2 paths are already tested in the other, unspecialized jobs.  
  
Also added jobs to compile tests with `BOOST_UUID_TO_FROM_CHARS_X86_USE_ZMM` and `BOOST_UUID_FROM_CHARS_X86_USE_VPERMI2B` experimental macros defined.

---

## Comment 1

> Username: pdimov  
> Created_at: 2026-01-09 17:23:59 UTC  
> Url: https://github.com/boostorg/uuid/pull/190#issuecomment-3729884632  

Thanks very much for all the work you've been doing.  
  
> As a side effect of this, CMakeLists.txt no longer uses boost_test_jamfile  
  
... but please let's not do that. `boost_test_jamfile` is much easier to maintain and we're not even using the functionality this adds. If in the future we drop b2 testing and move excusively to CMake we'll do that, but for now, there's no need.

---

## Comment 2

> Username: Lastique  
> Created_at: 2026-01-09 18:05:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/190#issuecomment-3730017298  

Ok, I've removed the changes to CMakeLists.txt.

---
