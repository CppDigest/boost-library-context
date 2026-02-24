# #970 File error code consistency [Closed]

> Username: TBBle  
> Created at: 2018-01-08 03:26:40 UTC  
> Updated at: 2018-02-15 03:24:50 UTC  
> Closed at: 2018-02-15 03:24:50 UTC  
> Url: https://github.com/boostorg/beast/pull/970  

While playing with `std::error_code`, I found a couple of places using a `boost::system::error_code` API that doesn't work with `std::error_code`, and replaced them with a cross-compatible approach.  
  
I also replaced in-passing a couple of places that were directly calling `::GetLastError()` instead of `boost::winapi::GetLastError()`. I was hoping to get rid of the `static_cast<int>` in a few places, but that's built-in to the way `error_code` has been defined, sadly.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-01-08 04:01:19 UTC  
> Url: https://github.com/boostorg/beast/pull/970#issuecomment-355880794  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/970?src=pr&el=h1) Report  
> Merging [#970](https://codecov.io/gh/boostorg/beast/pull/970?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/6f08814a0c291eb9f03aaa1daefffdc45c1b9087?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/970/graphs/tree.svg?src=pr&width=650&token=Gq6MFA9JRF&height=150)](https://codecov.io/gh/boostorg/beast/pull/970?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #970   +/-   ##  
========================================  
  Coverage    95.54%   95.54%             
========================================  
  Files          105      105             
  Lines        10543    10543             
========================================  
  Hits         10073    10073             
  Misses         470      470  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/970?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/impl/file\_posix.ipp](https://codecov.io/gh/boostorg/beast/pull/970/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvZmlsZV9wb3NpeC5pcHA=) | `69.73% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/core/impl/file\_stdio.ipp](https://codecov.io/gh/boostorg/beast/pull/970/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvZmlsZV9zdGRpby5pcHA=) | `75.47% <100%> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/970?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/970?src=pr&el=footer). Last update [6f08814...b2f4165](https://codecov.io/gh/boostorg/beast/pull/970?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2018-01-08 06:07:53 UTC  
> Url: https://github.com/boostorg/beast/pull/970#issuecomment-355889688  

Seems pretty reasonable, thanks!

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2018-01-08 06:09:24 UTC  
> Updated_at: 2018-01-08 06:09:33 UTC  
> Url: https://github.com/boostorg/beast/pull/970#issuecomment-355889831  

@TBBle Since you're on the subject of error codes, would you mind taking a peek at this pull request? It could use a code review. Error codes and error conditions are pretty arcane, so an extra set of eyes on it might help me avoid doing something silly! Thanks  
  
Here it is: https://github.com/boostorg/beast/pull/969

---

## Comment 4

> Username: TBBle  
> Created_at: 2018-01-08 14:36:32 UTC  
> Url: https://github.com/boostorg/beast/pull/970#issuecomment-355983033  

@vinniefalco Sure, happy to have a look.

---

## Review 5 [Approved]

> Username: djarek  
> Created_at: 2018-01-09 20:59:00 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/beast/pull/970#pullrequestreview-87655766  

LGTM

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2018-01-09 21:32:18 UTC  
> Url: https://github.com/boostorg/beast/pull/970#issuecomment-356420829  

This will go into 153

---

## Comment 7

> Username: vinniefalco  
> Created_at: 2018-02-15 03:24:50 UTC  
> Url: https://github.com/boostorg/beast/pull/970#issuecomment-365815747  

Merged into 153

---
