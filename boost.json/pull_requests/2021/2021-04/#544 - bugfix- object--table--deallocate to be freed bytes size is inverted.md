# #544 bugfix: object::table::deallocate to be freed bytes size is inverted [Closed]

> Username: KoaLaYT  
> Created at: 2021-04-23 09:03:11 UTC  
> Updated at: 2021-04-26 02:18:35 UTC  
> Closed at: 2021-04-25 16:53:27 UTC  
> Url: https://github.com/boostorg/json/pull/544  

When deallocate, the `bytes` to be freed is not consistent with allocation.  
Maybe just a typo.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2021-04-23 09:20:14 UTC  
> Updated_at: 2021-04-24 08:47:33 UTC  
> Url: https://github.com/boostorg/json/pull/544#issuecomment-825525063  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/544?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#544](https://codecov.io/gh/boostorg/json/pull/544?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (54265a1) into [develop](https://codecov.io/gh/boostorg/json/commit/9403530b598b271be004597a1d6bb7755ecf09dd?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (9403530) will **not change** coverage.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/544/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/544?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #544   +/-   ##  
========================================  
  Coverage    99.10%   99.10%             
========================================  
  Files           67       67             
  Lines         6049     6049             
========================================  
  Hits          5995     5995             
  Misses          54       54             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/544?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/impl/object.hpp](https://codecov.io/gh/boostorg/json/pull/544/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvb2JqZWN0LmhwcA==) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/544?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/544?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [9403530...54265a1](https://codecov.io/gh/boostorg/json/pull/544?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2021-04-23 11:24:03 UTC  
> Url: https://github.com/boostorg/json/pull/544#issuecomment-825591891  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/544/pullrequest-condensed-18d914c.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/544/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/544/pullrequest.html)

---

## Comment 3

> Username: grisumbras  
> Created_at: 2021-04-23 12:55:28 UTC  
> Url: https://github.com/boostorg/json/pull/544#issuecomment-825638830  

That's a great catch! Please, squash the commits, though.

---

## Comment 4

> Username: grisumbras  
> Created_at: 2021-04-23 12:55:33 UTC  
> Url: https://github.com/boostorg/json/pull/544#issuecomment-825638894  

@vinniefalco if you're OK with that, I can add a test and a changelog entry. Would you prefer all that to be in one commit?

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2021-04-23 15:21:16 UTC  
> Url: https://github.com/boostorg/json/pull/544#issuecomment-825732123  

How did this get past testing???!!!   
  
> I can add a test and a changelog entry. Would you prefer all that to be in one commit?  
  
Yes please

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2021-04-23 15:21:43 UTC  
> Url: https://github.com/boostorg/json/pull/544#issuecomment-825732388  

Please preserve the authorship even if you squash and add the test and CHANGELOG entry

---

## Comment 7

> Username: grisumbras  
> Created_at: 2021-04-23 15:50:28 UTC  
> Url: https://github.com/boostorg/json/pull/544#issuecomment-825750518  

Certainly

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2021-04-24 09:44:00 UTC  
> Url: https://github.com/boostorg/json/pull/544#issuecomment-826066144  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/544/pullrequest-condensed-af3d38e.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/544/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/544/pullrequest.html)

---

## Comment 9

> Username: grisumbras  
> Created_at: 2021-04-25 16:53:23 UTC  
> Url: https://github.com/boostorg/json/pull/544#issuecomment-826354539  

The fix is merged in a53e218. Thanks again for your contribution @KoaLaYT.

---
