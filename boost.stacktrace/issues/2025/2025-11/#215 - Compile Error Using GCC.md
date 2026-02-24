# #215 - Compile Error Using GCC [Closed]

> Username: zhanghaoxvan  
> Created at: 2025-11-14 07:05:45 UTC  
> Updated at: 2025-12-04 16:02:47 UTC  
> Closed at: 2025-12-04 16:02:47 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/215  

The 'addr_base_msvc.hpp' file in the 'stacktrace' module is missing the `<cstdint>` header include, which causes 'std::uintptr_t' to be undefined.  
  
'addr_base_msvc.hpp' is located here:  
```  
boost\boost\stacktrace\detail\addr_base_msvc.hpp  
```  
  
The error occurs on line 26.

---

## Comment 1

> Username: zhanghaoxvan  
> Created at: 2025-11-14 07:16:03 UTC  
> Updated at: 2025-11-14 07:35:23 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/215#issuecomment-3533064253  

The version I use is v1.89.0  
The GCC version I use is v15.2.0

---

## Comment 2

> Username: apolukhin  
> Created at: 2025-12-04 16:02:47 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/215#issuecomment-3612969380  

Many thanks for the bug report!  
  
The issue was fixed here https://github.com/boostorg/stacktrace/commit/07bc960cfead55ca3f4a970f66f3980cd95ee128  
The fix will appear in Boost 1.90 release
