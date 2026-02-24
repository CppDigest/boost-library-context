# #401 add missing assert header in pthreads_helpers.hpp [Merged]

> Username: zaucy  
> Created at: 2024-04-30 07:20:14 UTC  
> Updated at: 2026-02-06 10:57:07 UTC  
> Merged at: 2026-02-06 10:57:07 UTC  
> Closed at: 2026-02-06 10:57:07 UTC  
> Url: https://github.com/boostorg/thread/pull/401  

`BOOST_VERIFY` is used in this header, but doesn't include `<boost/assert.hpp>`. Discovered while trying to get tests to compile with Bazel.

---
