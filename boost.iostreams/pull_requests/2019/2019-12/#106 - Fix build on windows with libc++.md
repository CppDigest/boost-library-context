# #106 Fix build on windows with libc++ [Merged]

> Username: jcelerier  
> Created at: 2019-12-22 09:26:54 UTC  
> Updated at: 2020-09-10 22:26:16 UTC  
> Merged at: 2020-09-10 22:26:15 UTC  
> Closed at: 2020-09-10 22:26:15 UTC  
> Url: https://github.com/boostorg/iostreams/pull/106  

Proposed by @SquallATF in #67

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2019-12-22 10:18:09 UTC  
> Url: https://github.com/boostorg/iostreams/pull/106#issuecomment-568249228  

# [Codecov](https://codecov.io/gh/boostorg/iostreams/pull/106?src=pr&el=h1) Report  
> Merging [#106](https://codecov.io/gh/boostorg/iostreams/pull/106?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/iostreams/commit/601244b26aba14a2524ab1307243d0c5ea566206?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/iostreams/pull/106/graphs/tree.svg?width=650&token=LBEfwtNfca&height=150&src=pr)](https://codecov.io/gh/boostorg/iostreams/pull/106?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #106   +/-   ##  
========================================  
  Coverage    68.96%   68.96%             
========================================  
  Files           80       80             
  Lines         3444     3444             
  Branches      1027     1027             
========================================  
  Hits          2375     2375             
  Misses         452      452             
  Partials       617      617  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/iostreams/pull/106?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/iostreams/pull/106?src=pr&el=footer). Last update [601244b...4e76f73](https://codecov.io/gh/boostorg/iostreams/pull/106?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: mclow  
> Created_at: 2019-12-23 15:21:44 UTC  
> Url: https://github.com/boostorg/iostreams/pull/106#issuecomment-568502750  

Checking for `_LIBCPP_VERSION` is the correct way to check to see if you're using libc++.  
(Yes, you're doing that).

---

## Comment 3

> Username: jcelerier  
> Created_at: 2020-03-30 20:22:06 UTC  
> Url: https://github.com/boostorg/iostreams/pull/106#issuecomment-606226814  

ping, any way to get that in at some point ?

---

## Comment 4

> Username: Gei0r  
> Created_at: 2020-09-04 11:49:15 UTC  
> Url: https://github.com/boostorg/iostreams/pull/106#issuecomment-687095220  

Can you please merge this pull request? I just came across the same problem and this PR fixes it.

---
