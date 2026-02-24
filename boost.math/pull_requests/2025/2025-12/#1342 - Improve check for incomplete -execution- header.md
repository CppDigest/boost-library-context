# #1342 Improve check for incomplete <execution> header [Merged]

> Username: mbs-c  
> Created at: 2025-12-18 12:11:46 UTC  
> Updated at: 2025-12-18 17:14:02 UTC  
> Merged at: 2025-12-18 16:52:29 UTC  
> Closed at: 2025-12-18 16:52:29 UTC  
> Url: https://github.com/boostorg/math/pull/1342  

All versions of libc++ currently include a useless <execution> header by default, not just the version shipped with Apple Clang. For example, IBM Open XL C/C++ 17.1.3 on AIX is affected as well.

---

## Review 1 [Changes requested]

> Username: mborland  
> Created_at: 2025-12-18 14:22:58 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/math/pull/1342#pullrequestreview-3593255914  

📁 include/boost/math/tools/config.hpp

```diff
  85 | #else // Things from boost/config that are required, and easy to replicate
  86 | 
  87 |+ #include <version>
```

> Username: mborland  
> Created_at: 2025-12-18 14:22:36 UTC  
> Updated_at: 2025-12-18 14:22:58 UTC  
> Url: https://github.com/boostorg/math/pull/1342#discussion_r2631293768  

```diff  
-#include <version>  
+#if __has_include(<version>)  
+#include <version>  
+#endif  
```  
  
This wasn't standardized until C++20. I think it's unofficially available earlier, but looks like at least some of the older clangs don't have it.


---

## Review 2 [Approved]

> Username: mborland  
> Created_at: 2025-12-18 16:52:14 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/math/pull/1342#pullrequestreview-3593953361  

All of the CI failures are on the CI's side. Merging.

---
