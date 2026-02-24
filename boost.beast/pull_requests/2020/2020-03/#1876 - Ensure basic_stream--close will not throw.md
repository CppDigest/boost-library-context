# #1876 Ensure basic_stream::close will not throw [Closed]

> Username: madmongo1  
> Created at: 2020-03-13 17:47:55 UTC  
> Updated at: 2020-03-17 15:11:01 UTC  
> Closed at: 2020-03-17 15:11:01 UTC  
> Url: https://github.com/boostorg/beast/pull/1876  

refs: #1875

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2020-03-13 17:48:20 UTC  
> Url: https://github.com/boostorg/beast/pull/1876#issuecomment-598841448  

Suggested commit message:  
"close is noexcept"

---

## Comment 2

> Username: madmongo1  
> Created_at: 2020-03-13 18:09:47 UTC  
> Url: https://github.com/boostorg/beast/pull/1876#issuecomment-598849974  

> Suggested commit message:  
> "close is noexcept"  
  
This is not currently true.  
  
I have not modified the exception specification of the public interface, only the implementation.  
  
Do we want to also update the contract of the public interface to be incompatible with asio objects?  
  
There is probably no return from that decision if we change our minds later.

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2020-03-13 20:05:15 UTC  
> Url: https://github.com/boostorg/beast/pull/1876#issuecomment-598890870  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1876?src=pr&el=h1) Report  
> Merging [#1876](https://codecov.io/gh/boostorg/beast/pull/1876?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/c8a726f962b2fbf77d00b273b3c6fb0dd975a6b5&el=desc) will **increase** coverage by `0.00%`.  
> The diff coverage is `50.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1876/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF)](https://codecov.io/gh/boostorg/beast/pull/1876?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1876   +/-   ##  
========================================  
  Coverage    95.09%   95.10%             
========================================  
  Files          156      156             
  Lines        12011    12012    +1       
========================================  
+ Hits         11422    11424    +2       
+ Misses         589      588    -1       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1876?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/basic\_stream.hpp](https://codecov.io/gh/boostorg/beast/pull/1876/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2Jhc2ljX3N0cmVhbS5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/core/impl/basic\_stream.hpp](https://codecov.io/gh/boostorg/beast/pull/1876/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvYmFzaWNfc3RyZWFtLmhwcA==) | `86.09% <50.00%> (-0.33%)` | :arrow_down: |  
| [include/boost/beast/zlib/detail/deflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/1876/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9kZWZsYXRlX3N0cmVhbS5pcHA=) | `88.60% <0.00%> (+0.10%)` | :arrow_up: |  
| [include/boost/beast/zlib/detail/inflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/1876/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9pbmZsYXRlX3N0cmVhbS5pcHA=) | `94.57% <0.00%> (+0.19%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1876?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1876?src=pr&el=footer). Last update [c8a726f...d68b80a](https://codecov.io/gh/boostorg/beast/pull/1876?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
