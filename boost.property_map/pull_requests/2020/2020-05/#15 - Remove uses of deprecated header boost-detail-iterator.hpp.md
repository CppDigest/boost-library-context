# #15 Remove uses of deprecated header boost/detail/iterator.hpp [Merged]

> Username: Lastique  
> Created at: 2020-05-11 16:03:49 UTC  
> Updated at: 2020-06-23 19:34:52 UTC  
> Merged at: 2020-05-13 13:45:10 UTC  
> Closed at: 2020-05-13 13:45:10 UTC  
> Url: https://github.com/boostorg/property_map/pull/15  

This header was deprecated in favor of `<iterator>`. It generates compiler warnings and will be removed in a future release.

---

## Comment 1

> Username: mclow  
> Created_at: 2020-05-11 16:11:16 UTC  
> Url: https://github.com/boostorg/property_map/pull/15#issuecomment-626800261  

LGTM, though the whitespace changes are distracting.

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2020-05-11 17:33:25 UTC  
> Updated_at: 2020-05-11 17:34:59 UTC  
> Url: https://github.com/boostorg/property_map/pull/15#issuecomment-626845016  

# [Codecov](https://codecov.io/gh/boostorg/property_map/pull/15?src=pr&el=h1) Report  
> Merging [#15](https://codecov.io/gh/boostorg/property_map/pull/15?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/property_map/commit/ef61e3d2254ccc0864fbd0bcfbab11bff8b12cd9&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/property_map/pull/15/graphs/tree.svg?width=650&height=150&src=pr&token=IAO17unWyC)](https://codecov.io/gh/boostorg/property_map/pull/15?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #15   +/-   ##  
========================================  
  Coverage    53.07%   53.07%             
========================================  
  Files            5        5             
  Lines          130      130             
  Branches        47       47             
========================================  
  Hits            69       69             
  Misses          17       17             
  Partials        44       44             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/property_map/pull/15?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/property\_map/property\_map.hpp](https://codecov.io/gh/boostorg/property_map/pull/15/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9wZXJ0eV9tYXAvcHJvcGVydHlfbWFwLmhwcA==) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/property_map/pull/15?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/property_map/pull/15?src=pr&el=footer). Last update [ef61e3d...e41600e](https://codecov.io/gh/boostorg/property_map/pull/15?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 3

> Username: Lastique  
> Created_at: 2020-06-23 19:34:52 UTC  
> Url: https://github.com/boostorg/property_map/pull/15#issuecomment-648374755  

Please, merge to master.

---
