# #1595 Fix UB in websocket close tests [Closed]

> Username: djarek  
> Created at: 2019-04-29 11:38:43 UTC  
> Updated at: 2019-05-04 16:31:18 UTC  
> Closed at: 2019-05-04 16:31:18 UTC  
> Url: https://github.com/boostorg/beast/pull/1595  

Extend the lifetime of buffers in tests to avoid use-after-free.

---

## Review 1 [Commented]

> Username: Eelis  
> Created_at: 2019-04-29 11:40:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1595#pullrequestreview-231629366  

📁 test/beast/websocket/close.cpp

```diff
 474 |         {
 475 |             echo_server es{log};
 476 |+              std::string const s = "Hello, world!";
```

> Username: Eelis  
> Created_at: 2019-04-29 11:40:48 UTC  
> Updated_at: 2019-04-29 12:16:25 UTC  
> Url: https://github.com/boostorg/beast/pull/1595#discussion_r279328262  

Extra indentation


---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2019-04-30 01:24:04 UTC  
> Url: https://github.com/boostorg/beast/pull/1595#issuecomment-487796493  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1595?src=pr&el=h1) Report  
> Merging [#1595](https://codecov.io/gh/boostorg/beast/pull/1595?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/41040a21f0b0e12537beb1262c2d538a023efe65?src=pr&el=desc) will **increase** coverage by `<.01%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1595/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1595?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1595      +/-   ##  
===========================================  
+ Coverage     92.6%   92.61%   +<.01%       
===========================================  
  Files          151      151                
  Lines        12131    12131                
===========================================  
+ Hits         11234    11235       +1       
+ Misses         897      896       -1  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1595?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/zlib/detail/deflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/1595/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9kZWZsYXRlX3N0cmVhbS5pcHA=) | `80.2% <0%> (+0.1%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1595?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1595?src=pr&el=footer). Last update [41040a2...1163ee5](https://codecov.io/gh/boostorg/beast/pull/1595?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
