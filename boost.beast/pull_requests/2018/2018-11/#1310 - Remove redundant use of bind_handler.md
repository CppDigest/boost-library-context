# #1310 Remove redundant use of bind_handler [Closed]

> Username: djarek  
> Created at: 2018-11-21 00:17:39 UTC  
> Updated at: 2019-09-10 21:02:22 UTC  
> Closed at: 2018-11-21 14:13:58 UTC  
> Url: https://github.com/boostorg/beast/pull/1310  

bind_handler with 0 arguments is equivalent to passing directly to post().

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2018-11-21 01:01:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1310#pullrequestreview-177040459  

📁 include/boost/beast/http/impl/write.ipp

```diff
 295 |                 s_.get_executor(),
 296 |-                 bind_handler(std::move(*this)));
 296 |+                 std::move(*this));
```

> Username: vinniefalco  
> Created_at: 2018-11-21 01:01:17 UTC  
> Updated_at: 2018-11-21 01:01:18 UTC  
> Url: https://github.com/boostorg/beast/pull/1310#discussion_r235223358  

I don't know why I did this, looks like a blind copy/paste/edit.


---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2018-11-21 01:24:20 UTC  
> Url: https://github.com/boostorg/beast/pull/1310#issuecomment-440493492  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1310?src=pr&el=h1) Report  
> Merging [#1310](https://codecov.io/gh/boostorg/beast/pull/1310?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/1ca690db0615a170c155683968071ee9eb609bc0?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `0%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1310/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1310?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1310   +/-   ##  
========================================  
  Coverage    95.36%   95.36%             
========================================  
  Files          113      113             
  Lines        11117    11117             
========================================  
  Hits         10602    10602             
  Misses         515      515  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1310?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/http/impl/write.ipp](https://codecov.io/gh/boostorg/beast/pull/1310/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvd3JpdGUuaXBw) | `93.69% <0%> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1310?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1310?src=pr&el=footer). Last update [1ca690d...2a4156b](https://codecov.io/gh/boostorg/beast/pull/1310?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
