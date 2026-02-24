# #335 WIN32: fix comparison of different sizes into compute_cpu_set() [Open]

> Username: carlo-bramini  
> Created at: 2025-12-12 14:14:49 UTC  
> Updated at: 2025-12-12 14:14:49 UTC  
> Url: https://github.com/boostorg/fiber/pull/335  

Compiling with MinGW for x86_64/Aarch64 prints this warning:  
```  
libs/fiber/src/numa/windows/topology.cpp: In function ‘std::set<unsigned int> {anonymous}::compute_cpu_set(WORD, KAFFINITY)’:  
libs/fiber/src/numa/windows/topology.cpp:93:28: warning: comparison of integer expressions of different signedness: ‘int’ and ‘long long unsigned int’ [-Wsign-compare]  
   93 |         for ( int i = 0; i < sizeof( mask) * 8; ++i) {  
      |                          ~~^~~~~~~~~~~~~~~~~~~  
```  
This is correct because `sizeof()` returns `size_t`, but hopefully it can be fixed with a tiny change, by simply replacing `int` with `size_t`.  
This is not dangerous, however it's still useful for removing this annoying warning when compiling, if possible.

---
