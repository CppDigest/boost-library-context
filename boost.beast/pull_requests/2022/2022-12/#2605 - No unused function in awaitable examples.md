# #2605 No unused function in awaitable examples. [Merged]

> Username: klemens-morgenstern  
> Created at: 2022-12-24 01:46:19 UTC  
> Updated at: 2023-01-24 01:55:06 UTC  
> Merged at: 2023-01-24 01:55:05 UTC  
> Closed at: 2023-01-24 01:55:05 UTC  
> Url: https://github.com/boostorg/beast/pull/2605  

Closes #2604.

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2022-12-24 01:46:48 UTC  
> Url: https://github.com/boostorg/beast/pull/2605#issuecomment-1364435162  

Nice try but did you read my comment in the issue? This is not the correct fix.

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-12-24 02:15:04 UTC  
> Url: https://github.com/boostorg/beast/pull/2605#issuecomment-1364440568  

![pullrequest](https://2605.beast.tracing.cppalliance.org/pullrequest-6824356f.png)  
Timeline tracing charts: [https://2605.beast.tracing.cppalliance.org/index.html](https://2605.beast.tracing.cppalliance.org/index.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2022-12-24 02:36:47 UTC  
> Url: https://github.com/boostorg/beast/pull/2605#issuecomment-1364444736  

An automated preview of the documentation is available at [https://2605.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2605.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2022-12-24 02:53:48 UTC  
> Url: https://github.com/boostorg/beast/pull/2605#issuecomment-1364446991  

![pullrequest](https://2605.beast.tracing.cppalliance.org/pullrequest-98d7576f.png)  
Timeline tracing charts: [https://2605.beast.tracing.cppalliance.org/index.html](https://2605.beast.tracing.cppalliance.org/index.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2022-12-24 03:11:48 UTC  
> Url: https://github.com/boostorg/beast/pull/2605#issuecomment-1364449348  

An automated preview of the documentation is available at [https://2605.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2605.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2022-12-24 03:25:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2605#pullrequestreview-1229502922  

📁 example/http/client/awaitable-ssl/http_client_awaitable_ssl.cpp

```diff
 162 |+           [](std::exception_ptr e)
 163 |+           {
 164 |+             if (e)
```

> Username: vinniefalco  
> Created_at: 2022-12-24 03:25:12 UTC  
> Url: https://github.com/boostorg/beast/pull/2605#discussion_r1056722212  

Maybe you should write  
```  
if( ! e )  
    return;  
```

> Username: klemens-morgenstern  
> Created_at: 2023-01-23 09:51:13 UTC  
> Updated_at: 2023-01-23 09:51:55 UTC  
> Url: https://github.com/boostorg/beast/pull/2605#discussion_r1083839830  

```diff  
-            if (e)  
+            if (!e)  
+                return ;  
```


---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2022-12-24 03:29:21 UTC  
> Url: https://github.com/boostorg/beast/pull/2605#issuecomment-1364451201  

![pullrequest](https://2605.beast.tracing.cppalliance.org/pullrequest-31d6713e.png)  
Timeline tracing charts: [https://2605.beast.tracing.cppalliance.org/index.html](https://2605.beast.tracing.cppalliance.org/index.html)

---

## Comment 8

> Username: codecov[bot]  
> Created_at: 2022-12-24 03:59:01 UTC  
> Updated_at: 2023-01-23 11:08:33 UTC  
> Url: https://github.com/boostorg/beast/pull/2605#issuecomment-1364454635  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2605?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2605](https://codecov.io/gh/boostorg/beast/pull/2605?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (ec1264a) into [develop](https://codecov.io/gh/boostorg/beast/commit/334b9871bed6c348fcdda0c46aba74c46968c762?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (334b987) will **increase** coverage by `0.00%`.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2605/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/beast/pull/2605?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #2605   +/-   ##  
========================================  
  Coverage    94.60%   94.61%             
========================================  
  Files          154      154             
  Lines        12052    12062   +10       
========================================  
+ Hits         11402    11412   +10       
  Misses         650      650             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2605?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/impl/error.ipp](https://codecov.io/gh/boostorg/beast/pull/2605?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvZXJyb3IuaXBw) | `100.00% <0.00%> (ø)` | |  
| [include/boost/beast/http/impl/error.ipp](https://codecov.io/gh/boostorg/beast/pull/2605?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvZXJyb3IuaXBw) | `95.45% <0.00%> (+0.21%)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/error.ipp](https://codecov.io/gh/boostorg/beast/pull/2605?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9lcnJvci5pcHA=) | `88.33% <0.00%> (+0.83%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2605?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2605?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [334b987...ec1264a](https://codecov.io/gh/boostorg/beast/pull/2605?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2023-01-23 10:01:50 UTC  
> Url: https://github.com/boostorg/beast/pull/2605#issuecomment-1400083081  

An automated preview of the documentation is available at [https://2605.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2605.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2023-01-23 10:21:39 UTC  
> Url: https://github.com/boostorg/beast/pull/2605#issuecomment-1400110357  

![pullrequest](https://2605.beast.tracing.cppalliance.org/pullrequest-f9a0ae28.png)  
Timeline tracing charts: [https://2605.beast.tracing.cppalliance.org/index.html](https://2605.beast.tracing.cppalliance.org/index.html)

---
