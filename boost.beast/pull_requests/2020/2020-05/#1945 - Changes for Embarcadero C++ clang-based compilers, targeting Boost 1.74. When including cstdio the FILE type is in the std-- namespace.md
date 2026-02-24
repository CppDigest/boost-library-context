# #1945 Changes for Embarcadero C++ clang-based compilers, targeting Boost 1.74. When including cstdio the FILE type is in the std:: namespace. [Closed]

> Username: eldiener  
> Created at: 2020-05-07 01:52:38 UTC  
> Updated at: 2020-05-20 14:18:33 UTC  
> Closed at: 2020-05-20 14:18:33 UTC  
> Url: https://github.com/boostorg/beast/pull/1945  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-05-07 15:57:14 UTC  
> Url: https://github.com/boostorg/beast/pull/1945#issuecomment-625342296  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1945?src=pr&el=h1) Report  
> Merging [#1945](https://codecov.io/gh/boostorg/beast/pull/1945?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/fc7c645f64192976acfb6a88a8fdbd5d92bc44fc&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1945/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF)](https://codecov.io/gh/boostorg/beast/pull/1945?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1945   +/-   ##  
========================================  
  Coverage    95.11%   95.11%             
========================================  
  Files          156      156             
  Lines        12032    12032             
========================================  
  Hits         11444    11444             
  Misses         588      588             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1945?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/file\_stdio.hpp](https://codecov.io/gh/boostorg/beast/pull/1945/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ZpbGVfc3RkaW8uaHBw) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1945?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1945?src=pr&el=footer). Last update [fc7c645...1ae833b](https://codecov.io/gh/boostorg/beast/pull/1945?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: eldiener  
> Created_at: 2020-05-10 21:24:03 UTC  
> Url: https://github.com/boostorg/beast/pull/1945#issuecomment-626391919  

I submitted a PR to zlib for adding Embarcadero C++ clang-based support ( https://github.com/madler/zlib/pull/490 ). The open PRs for zlib are currently at 142 going back to 2013. This is why I think that sometimes Boost needs to fix things for themselves rather than wait for some 3rd party library to do so.

---
