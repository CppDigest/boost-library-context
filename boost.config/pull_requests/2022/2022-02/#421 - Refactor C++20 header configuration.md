# #421 Refactor C++20 header configuration. [Merged]

> Username: jzmaddock  
> Created at: 2022-02-03 18:55:03 UTC  
> Updated at: 2022-11-20 17:45:16 UTC  
> Merged at: 2022-02-04 11:16:35 UTC  
> Closed at: 2022-02-04 11:16:35 UTC  
> Url: https://github.com/boostorg/config/pull/421  



---

## Comment 1

> Username: pdimov  
> Created_at: 2022-02-03 19:28:50 UTC  
> Url: https://github.com/boostorg/config/pull/421#issuecomment-1029326909  

You can fix the `mt.exe` errors by adding `embed-manifest-via=linker` to the `b2` command line. `clang-win.jam` doesn't detect the path to `mt.exe` properly for the Windows 10 Platform SDK, and even though I wrote `clang-win.jam`, I don't quite know how to fix it, but embedding via the linker works. :-)

---

## Review 2 [Commented]

> Username: pdimov  
> Created_at: 2022-02-03 19:33:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/config/pull/421#pullrequestreview-872320933  

📁 include/boost/config/detail/suffix.hpp

```diff
1202 |+ #if !__has_include(<version>)
1203 |+ #  define BOOST_NO_CXX20_HDR_VERSION
1204 |+ #endif
```

> Username: pdimov  
> Created_at: 2022-02-03 19:33:16 UTC  
> Url: https://github.com/boostorg/config/pull/421#discussion_r798903693  

I think that it would be useful to have an `#else` here that does `#include <version>` so that we can rely on it being always included if available, even if `select_stdlib_config` didn't include it because of a user override.


---
