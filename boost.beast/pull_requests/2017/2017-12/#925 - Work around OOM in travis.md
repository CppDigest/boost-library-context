# #925 Work around OOM in travis [Closed]

> Username: djarek  
> Created at: 2017-12-06 21:26:29 UTC  
> Updated at: 2017-12-09 23:33:11 UTC  
> Closed at: 2017-12-09 23:33:04 UTC  
> Url: https://github.com/boostorg/beast/pull/925  

Split the websocket read test to reduce compile time memory usage  by a small amount, which ought to be enough to let it compile in a constrained environment like travis.

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2017-12-07 14:32:17 UTC  
> Updated_at: 2017-12-07 14:32:33 UTC  
> Url: https://github.com/boostorg/beast/pull/925#issuecomment-349983822  

![image](https://user-images.githubusercontent.com/1503976/33720306-6658c6b6-db18-11e7-9bad-490e9cfe87d2.png)

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2017-12-07 14:32:47 UTC  
> Url: https://github.com/boostorg/beast/pull/925#issuecomment-349983968  

OH....sorry, you said OOM.

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2017-12-08 22:14:12 UTC  
> Url: https://github.com/boostorg/beast/pull/925#issuecomment-350385501  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/925?src=pr&el=h1) Report  
> Merging [#925](https://codecov.io/gh/boostorg/beast/pull/925?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/8414dbf73b50d4b67bd933f2fe20863dd73c3de5?src=pr&el=desc) will **decrease** coverage by `<.01%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/925/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF)](https://codecov.io/gh/boostorg/beast/pull/925?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##           develop    #925      +/-   ##  
==========================================  
- Coverage    95.71%   95.7%   -0.01%       
==========================================  
  Files          104     104                
  Lines        10453   10453                
==========================================  
- Hits         10005   10004       -1       
- Misses         448     449       +1  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/925?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/stream.hpp](https://codecov.io/gh/boostorg/beast/pull/925/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvc3RyZWFtLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/close.ipp](https://codecov.io/gh/boostorg/beast/pull/925/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9jbG9zZS5pcHA=) | `99.02% <0%> (-0.49%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/925?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/925?src=pr&el=footer). Last update [8414dbf...cbefae6](https://codecov.io/gh/boostorg/beast/pull/925?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2017-12-09 20:47:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/925#pullrequestreview-82334965  

📁 test/beast/websocket/read2.cpp

```diff
  15 |+ #include <boost/asio/write.hpp>
  16 |+ 
  17 |+ #include <boost/beast/core/buffers_to_string.hpp>
```

> Username: vinniefalco  
> Created_at: 2017-12-09 20:47:07 UTC  
> Url: https://github.com/boostorg/beast/pull/925#discussion_r155931928  

This include is not needed


---

## Comment 5

> Username: djarek  
> Created_at: 2017-12-09 23:33:04 UTC  
> Url: https://github.com/boostorg/beast/pull/925#issuecomment-350513262  

In #928

---
