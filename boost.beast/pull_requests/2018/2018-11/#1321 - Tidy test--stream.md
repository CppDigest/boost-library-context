# #1321 Tidy test::stream [Closed]

> Username: djarek  
> Created at: 2018-11-24 21:41:49 UTC  
> Updated at: 2019-09-10 21:02:19 UTC  
> Closed at: 2018-11-27 16:35:04 UTC  
> Url: https://github.com/boostorg/beast/pull/1321  

test::stream will no longer include asio::tcp::ip transitively.  
Resolves #1308

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2018-11-24 22:15:17 UTC  
> Url: https://github.com/boostorg/beast/pull/1321#issuecomment-441399284  

I'm not entirely sure this is right. Doesn't the compiler have to see the primary function template first, before a specialization?

---

## Comment 2

> Username: djarek  
> Created_at: 2018-11-24 22:39:57 UTC  
> Url: https://github.com/boostorg/beast/pull/1321#issuecomment-441400443  

I don't believe these are specializations, they are regular overloads. https://en.cppreference.com/w/cpp/language/template_specialization

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2018-11-25 02:45:15 UTC  
> Url: https://github.com/boostorg/beast/pull/1321#issuecomment-441411007  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1321?src=pr&el=h1) Report  
> Merging [#1321](https://codecov.io/gh/boostorg/beast/pull/1321?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/4a2fd11fa9c4442d3d102e3a82018197b25eed4c?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1321/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1321?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1321   +/-   ##  
========================================  
  Coverage    94.48%   94.48%             
========================================  
  Files          124      124             
  Lines        11469    11469             
========================================  
  Hits         10836    10836             
  Misses         633      633  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1321?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/\_experimental/test/stream.hpp](https://codecov.io/gh/boostorg/beast/pull/1321/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9fZXhwZXJpbWVudGFsL3Rlc3Qvc3RyZWFtLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1321?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1321?src=pr&el=footer). Last update [4a2fd11...17e5098](https://codecov.io/gh/boostorg/beast/pull/1321?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
