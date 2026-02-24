# #2008 Refactor crawl [Closed]

> Username: madmongo1  
> Created at: 2020-07-07 13:25:52 UTC  
> Updated at: 2020-07-07 19:51:35 UTC  
> Closed at: 2020-07-07 19:49:27 UTC  
> Url: https://github.com/boostorg/beast/pull/2008  

* Applies Chris' recommendation re work guard.  
* One strand per worker rather than two.  
* Fixes lockup due to scheduler race.

---

## Comment 1

> Username: madmongo1  
> Created_at: 2020-07-07 13:33:04 UTC  
> Url: https://github.com/boostorg/beast/pull/2008#issuecomment-654862756  

Note, I intend to squash the commits after review

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2020-07-07 14:19:10 UTC  
> Url: https://github.com/boostorg/beast/pull/2008#issuecomment-654896183  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2008?src=pr&el=h1) Report  
> Merging [#2008](https://codecov.io/gh/boostorg/beast/pull/2008?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/03d43aa397cdf2b8bea182affa9e82187106c293&el=desc) will **decrease** coverage by `0.00%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2008/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF)](https://codecov.io/gh/boostorg/beast/pull/2008?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2008      +/-   ##  
===========================================  
- Coverage    95.09%   95.08%   -0.01%       
===========================================  
  Files          153      153                
  Lines        11939    11939                
===========================================  
- Hits         11353    11352       -1       
- Misses         586      587       +1       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2008?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/zlib/detail/deflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/2008/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9kZWZsYXRlX3N0cmVhbS5pcHA=) | `88.50% <0.00%> (-0.11%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2008?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2008?src=pr&el=footer). Last update [03d43aa...bff8ea8](https://codecov.io/gh/boostorg/beast/pull/2008?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2020-07-07 14:41:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2008#pullrequestreview-443960203  

📁 example/http/client/crawl/http_crawl.cpp

```diff
 151 | 
 152 |     crawl_report& report_;
 153 |+     net::strand<net::io_context::executor_type> exec_;
```

> Username: vinniefalco  
> Created_at: 2020-07-07 14:41:40 UTC  
> Url: https://github.com/boostorg/beast/pull/2008#discussion_r450917914  

The canonical name for variables that are executors is `ex_`


---

## Comment 4

> Username: vinniefalco  
> Created_at: 2020-07-07 14:44:32 UTC  
> Url: https://github.com/boostorg/beast/pull/2008#issuecomment-654913797  

If you intend to squash the commits, consider labelling them thusly in the future:  
  
```  
Fix usage message  
[FOLD] Refactor executor use  
[FOLD] Remove redundant ioc reference in crawl_report  
[FOLD] Fix lockup in http-crawl  
```  
  
Or some variation

---
