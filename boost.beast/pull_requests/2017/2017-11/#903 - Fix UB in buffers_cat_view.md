# #903 Fix UB in buffers_cat_view [Closed]

> Username: djarek  
> Created at: 2017-11-23 23:19:43 UTC  
> Updated at: 2019-09-10 21:02:38 UTC  
> Closed at: 2017-11-26 21:16:24 UTC  
> Url: https://github.com/boostorg/beast/pull/903  

- `std::array<char, N>` is not a valid type for in-place polymorphic storage, due to the fact that it might not have the required alignment.  
- Removed unnecessary casts to `void*` in `iter()`.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-11-24 02:02:38 UTC  
> Updated_at: 2017-11-26 03:55:42 UTC  
> Url: https://github.com/boostorg/beast/pull/903#issuecomment-346723338  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/903?src=pr&el=h1) Report  
> Merging [#903](https://codecov.io/gh/boostorg/beast/pull/903?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/6e6e1f6fbc93b92c8b21d673933e3b77951b4fa0?src=pr&el=desc) will **increase** coverage by `<.01%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/903/graphs/tree.svg?token=Gq6MFA9JRF&src=pr&width=650&height=150)](https://codecov.io/gh/boostorg/beast/pull/903?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #903      +/-   ##  
===========================================  
+ Coverage    95.69%   95.69%   +<.01%       
===========================================  
  Files          104      104                
  Lines        10454    10455       +1       
===========================================  
+ Hits         10004    10005       +1       
  Misses         450      450  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/903?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/detail/type\_traits.hpp](https://codecov.io/gh/boostorg/beast/pull/903?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC90eXBlX3RyYWl0cy5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/core/detail/variant.hpp](https://codecov.io/gh/boostorg/beast/pull/903?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC92YXJpYW50LmhwcA==) | `95.52% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/core/impl/buffers\_cat.ipp](https://codecov.io/gh/boostorg/beast/pull/903?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvYnVmZmVyc19jYXQuaXBw) | `98.87% <100%> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/903?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/903?src=pr&el=footer). Last update [6e6e1f6...c6e71a7](https://codecov.io/gh/boostorg/beast/pull/903?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2017-11-24 11:49:46 UTC  
> Url: https://github.com/boostorg/beast/pull/903#issuecomment-346811536  

`std::aligned_union` is not available in some versions of the stdlib even though they claim to be C++11, try `std::aligned_storage`. Otherwise, I like where you are going with this!

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2017-11-26 21:16:24 UTC  
> Url: https://github.com/boostorg/beast/pull/903#issuecomment-347038987  

Merged some of it

---
