# #26 Changes for Embarcadero C++ clang-based compilers, targeting Boost 1.74. Change __BORLANDC__ to BOOST_BORLANDC, which is defined in Boost conf… [Merged]

> Username: eldiener  
> Created at: 2020-04-08 23:00:36 UTC  
> Updated at: 2020-04-16 14:49:48 UTC  
> Merged at: 2020-04-16 14:49:47 UTC  
> Closed at: 2020-04-16 14:49:48 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/26  

…ig for the Embarcadero non-clang-based compilers.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-04-10 14:37:51 UTC  
> Updated_at: 2020-04-10 14:43:31 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/26#issuecomment-612057106  

# [Codecov](https://codecov.io/gh/boostorg/ptr_container/pull/26?src=pr&el=h1) Report  
> :exclamation: No coverage uploaded for pull request base (`develop@c7b6a7e`). [Click here to learn what that means](https://docs.codecov.io/docs/error-reference#section-missing-base-commit).  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/ptr_container/pull/26/graphs/tree.svg?width=650&height=150&src=pr&token=D5x8WKDJ88)](https://codecov.io/gh/boostorg/ptr_container/pull/26?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##             develop      #26   +/-   ##  
==========================================  
  Coverage           ?   63.97%             
==========================================  
  Files              ?       35             
  Lines              ?     1471             
  Branches           ?      548             
==========================================  
  Hits               ?      941             
  Misses             ?       34             
  Partials           ?      496             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/ptr_container/pull/26?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [.../ptr\_container/detail/reversible\_ptr\_container.hpp](https://codecov.io/gh/boostorg/ptr_container/pull/26/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wdHJfY29udGFpbmVyL2RldGFpbC9yZXZlcnNpYmxlX3B0cl9jb250YWluZXIuaHBw) | `61.23% <ø> (ø)` | |  
| [...ude/boost/ptr\_container/detail/static\_move\_ptr.hpp](https://codecov.io/gh/boostorg/ptr_container/pull/26/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wdHJfY29udGFpbmVyL2RldGFpbC9zdGF0aWNfbW92ZV9wdHIuaHBw) | `91.30% <ø> (ø)` | |  
| [...st/ptr\_container/serialize\_ptr\_circular\_buffer.hpp](https://codecov.io/gh/boostorg/ptr_container/pull/26/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wdHJfY29udGFpbmVyL3NlcmlhbGl6ZV9wdHJfY2lyY3VsYXJfYnVmZmVyLmhwcA==) | `50.00% <0.00%> (ø)` | |  
| [...nclude/boost/ptr\_container/serialize\_ptr\_array.hpp](https://codecov.io/gh/boostorg/ptr_container/pull/26/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wdHJfY29udGFpbmVyL3NlcmlhbGl6ZV9wdHJfYXJyYXkuaHBw) | `75.00% <0.00%> (ø)` | |  
| [...nclude/boost/ptr\_container/detail/map\_iterator.hpp](https://codecov.io/gh/boostorg/ptr_container/pull/26/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wdHJfY29udGFpbmVyL2RldGFpbC9tYXBfaXRlcmF0b3IuaHBw) | `82.35% <0.00%> (ø)` | |  
| [...ptr\_container/detail/associative\_ptr\_container.hpp](https://codecov.io/gh/boostorg/ptr_container/pull/26/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wdHJfY29udGFpbmVyL2RldGFpbC9hc3NvY2lhdGl2ZV9wdHJfY29udGFpbmVyLmhwcA==) | `61.45% <0.00%> (ø)` | |  
| [include/boost/ptr\_container/serialize\_ptr\_set.hpp](https://codecov.io/gh/boostorg/ptr_container/pull/26/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wdHJfY29udGFpbmVyL3NlcmlhbGl6ZV9wdHJfc2V0LmhwcA==) | `100.00% <0.00%> (ø)` | |  
| [include/boost/ptr\_container/ptr\_unordered\_map.hpp](https://codecov.io/gh/boostorg/ptr_container/pull/26/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wdHJfY29udGFpbmVyL3B0cl91bm9yZGVyZWRfbWFwLmhwcA==) | `92.30% <0.00%> (ø)` | |  
| [...e/boost/ptr\_container/detail/void\_ptr\_iterator.hpp](https://codecov.io/gh/boostorg/ptr_container/pull/26/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wdHJfY29udGFpbmVyL2RldGFpbC92b2lkX3B0cl9pdGVyYXRvci5ocHA=) | `85.71% <0.00%> (ø)` | |  
| ... and [27 more](https://codecov.io/gh/boostorg/ptr_container/pull/26/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/ptr_container/pull/26?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/ptr_container/pull/26?src=pr&el=footer). Last update [c7b6a7e...51ecf6e](https://codecov.io/gh/boostorg/ptr_container/pull/26?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
