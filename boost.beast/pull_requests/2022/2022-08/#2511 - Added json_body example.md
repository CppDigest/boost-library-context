# #2511 Added json_body example. [Merged]

> Username: klemens-morgenstern  
> Created at: 2022-08-30 15:55:35 UTC  
> Updated at: 2022-10-03 01:53:12 UTC  
> Merged at: 2022-10-03 01:53:05 UTC  
> Closed at: 2022-10-03 01:53:05 UTC  
> Url: https://github.com/boostorg/beast/pull/2511  



---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-08-30 16:23:08 UTC  
> Url: https://github.com/boostorg/beast/pull/2511#issuecomment-1231891412  

![pullrequest](https://2511.beast.tracing.cppalliance.org/pullrequest-c504199d.png)  
Timeline tracing charts: [https://2511.beast.tracing.cppalliance.org/index.html](https://2511.beast.tracing.cppalliance.org/index.html)

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-31 00:51:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2511#pullrequestreview-1091121295  

📁 example/http/client/Jamfile

```diff
  18 | build-project sync-ssl ;
  19 |+ 
  20 |+ build-project body ;
```

> Username: vinniefalco  
> Created_at: 2022-08-31 00:51:02 UTC  
> Url: https://github.com/boostorg/beast/pull/2511#discussion_r959046662  

json-body ?

> Username: klemens-morgenstern  
> Created_at: 2022-08-31 01:26:55 UTC  
> Url: https://github.com/boostorg/beast/pull/2511#discussion_r959060009  

I named it body in case we get more body examples.


---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2022-08-31 01:36:38 UTC  
> Url: https://github.com/boostorg/beast/pull/2511#issuecomment-1232348385  

An automated preview of the documentation is available at [https://2511.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2511.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2022-08-31 01:51:52 UTC  
> Url: https://github.com/boostorg/beast/pull/2511#issuecomment-1232356331  

![pullrequest](https://2511.beast.tracing.cppalliance.org/pullrequest-79d9d315.png)  
Timeline tracing charts: [https://2511.beast.tracing.cppalliance.org/index.html](https://2511.beast.tracing.cppalliance.org/index.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2022-08-31 07:36:36 UTC  
> Url: https://github.com/boostorg/beast/pull/2511#issuecomment-1232573916  

An automated preview of the documentation is available at [https://2511.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2511.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2022-08-31 07:59:35 UTC  
> Url: https://github.com/boostorg/beast/pull/2511#issuecomment-1232595936  

![pullrequest](https://2511.beast.tracing.cppalliance.org/pullrequest-2037d144.png)  
Timeline tracing charts: [https://2511.beast.tracing.cppalliance.org/index.html](https://2511.beast.tracing.cppalliance.org/index.html)

---

## Comment 7

> Username: codecov[bot]  
> Created_at: 2022-08-31 08:18:59 UTC  
> Updated_at: 2022-08-31 08:38:18 UTC  
> Url: https://github.com/boostorg/beast/pull/2511#issuecomment-1232618329  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2511?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2511](https://codecov.io/gh/boostorg/beast/pull/2511?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (3405906) into [develop](https://codecov.io/gh/boostorg/beast/commit/76043dec2cf67a2ba33b32bdcc129f5f0027b8be?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (76043de) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2511/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/beast/pull/2511?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #2511   +/-   ##  
========================================  
  Coverage    94.72%   94.72%             
========================================  
  Files          152      152             
  Lines        11868    11868             
========================================  
  Hits         11242    11242             
  Misses         626      626             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2511?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/impl/multi\_buffer.hpp](https://codecov.io/gh/boostorg/beast/pull/2511/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvbXVsdGlfYnVmZmVyLmhwcA==) | `97.59% <0.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2511?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2511?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [76043de...3405906](https://codecov.io/gh/boostorg/beast/pull/2511?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 8

> Username: klemens-morgenstern  
> Created_at: 2022-10-02 11:57:00 UTC  
> Url: https://github.com/boostorg/beast/pull/2511#issuecomment-1264625542  

@vinniefalco waiting for your approval.

---

## Comment 9

> Username: vinniefalco  
> Created_at: 2022-10-02 19:30:54 UTC  
> Url: https://github.com/boostorg/beast/pull/2511#issuecomment-1264716063  

Commit messages should not describe a past event but should instead a sentence using a form of the verb to-be. For example: "streaming JSON body is an example"

---

## Comment 10

> Username: vinniefalco  
> Created_at: 2022-10-02 19:31:42 UTC  
> Url: https://github.com/boostorg/beast/pull/2511#issuecomment-1264716232  

Seems ok

---
