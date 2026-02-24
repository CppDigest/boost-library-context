# #1491 Allow the use of string_body and vector_body with -fno-exceptions [Closed]

> Username: djarek  
> Created at: 2019-03-01 22:08:16 UTC  
> Updated at: 2019-09-10 21:03:22 UTC  
> Closed at: 2019-03-05 00:15:51 UTC  
> Url: https://github.com/boostorg/beast/pull/1491  

`string_body` and `vector_body` will no longer translate all exceptions  
to "buffer_overflow" error code. `buffer_overflow` error can now only  
occur if the Body's max_size() is exceeded.  
  
Changes required:  
Code that relies on exceptions thrown from value_type's reserve/resize  
being translated into an error code must implement a mechanism to catch  
the exception.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2019-03-02 00:21:30 UTC  
> Url: https://github.com/boostorg/beast/pull/1491#issuecomment-468857927  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1491?src=pr&el=h1) Report  
> Merging [#1491](https://codecov.io/gh/boostorg/beast/pull/1491?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/723fceaf670f4a1cbd995969bca42e19f35869ac?src=pr&el=desc) will **increase** coverage by `0.02%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1491/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1491?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1491      +/-   ##  
===========================================  
+ Coverage    92.87%   92.89%   +0.02%       
===========================================  
  Files          147      147                
  Lines        12150    12147       -3       
===========================================  
  Hits         11284    11284                
+ Misses         866      863       -3  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1491?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/http/string\_body.hpp](https://codecov.io/gh/boostorg/beast/pull/1491/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL3N0cmluZ19ib2R5LmhwcA==) | `89.47% <100%> (+8.98%)` | :arrow_up: |  
| [include/boost/beast/zlib/detail/deflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/1491/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9kZWZsYXRlX3N0cmVhbS5pcHA=) | `80.1% <0%> (-0.11%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1491?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1491?src=pr&el=footer). Last update [723fcea...a5d5c75](https://codecov.io/gh/boostorg/beast/pull/1491?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: djarek  
> Created_at: 2019-03-05 00:15:51 UTC  
> Url: https://github.com/boostorg/beast/pull/1491#issuecomment-469480802  

Merged in 226.

---
