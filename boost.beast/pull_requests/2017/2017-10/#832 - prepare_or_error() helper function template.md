# #832 prepare_or_error() helper function template [Closed]

> Username: djarek  
> Created at: 2017-10-24 22:54:05 UTC  
> Updated at: 2019-09-10 21:02:10 UTC  
> Closed at: 2018-05-01 17:32:30 UTC  
> Url: https://github.com/boostorg/beast/pull/832  

Attempts to retrieve a mutable buffer sequence from a DynamicBuffer. If its maximal size has been exceeded it will set the error_code to buffer_overflow from the user-supplied enumeration type.  
Will be used in #810.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-10-25 16:17:03 UTC  
> Url: https://github.com/boostorg/beast/pull/832#issuecomment-339385504  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/832?src=pr&el=h1) Report  
> Merging [#832](https://codecov.io/gh/boostorg/beast/pull/832?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/1e9fcbad61dd9e6dc1bc0be1b5882c2a9a8d1b4d?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/832/graphs/tree.svg?token=Gq6MFA9JRF&src=pr&width=650&height=150)](https://codecov.io/gh/boostorg/beast/pull/832?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #832   +/-   ##  
========================================  
  Coverage    95.67%   95.67%             
========================================  
  Files          104      104             
  Lines        10447    10447             
========================================  
  Hits          9995     9995             
  Misses         452      452  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/832?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/detail/type\_traits.hpp](https://codecov.io/gh/boostorg/beast/pull/832?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC90eXBlX3RyYWl0cy5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/832?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/832?src=pr&el=footer). Last update [1e9fcba...a738477](https://codecov.io/gh/boostorg/beast/pull/832?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2017-10-29 00:44:33 UTC  
> Url: https://github.com/boostorg/beast/pull/832#issuecomment-340229520  

Unfortunately this function exposes a problem with the way that beast's error codes are declared...

---

## Comment 3

> Username: stale[bot]  
> Created_at: 2018-04-24 16:52:21 UTC  
> Url: https://github.com/boostorg/beast/pull/832#issuecomment-384003483  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 4

> Username: stale[bot]  
> Created_at: 2018-05-01 17:32:24 UTC  
> Url: https://github.com/boostorg/beast/pull/832#issuecomment-385733347  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!

---
