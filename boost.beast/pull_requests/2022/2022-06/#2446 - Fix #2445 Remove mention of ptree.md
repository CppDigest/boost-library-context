# #2446 Fix #2445 Remove mention of ptree [Merged]

> Username: sehe  
> Created at: 2022-06-06 22:39:12 UTC  
> Updated at: 2022-06-08 22:10:28 UTC  
> Merged at: 2022-06-08 22:10:21 UTC  
> Closed at: 2022-06-08 22:10:21 UTC  
> Url: https://github.com/boostorg/beast/pull/2446  

Actually, one mention still remains, which I cannot remove [unless  
DigiCert changes their G4 Trusted Root CA certificate](https://github.com/boostorg/beast/blob/develop/example/common/root_certificates.hpp#L1134:~:text=pTREE)...

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-06-06 22:46:53 UTC  
> Url: https://github.com/boostorg/beast/pull/2446#issuecomment-1148007792  

An automated preview of the documentation is available at [https://2446.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2446.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-06-06 23:06:57 UTC  
> Url: https://github.com/boostorg/beast/pull/2446#issuecomment-1148018235  

![pullrequest](https://2446.beast.tracing.cppalliance.org/pullrequest-14589d41.png)  
Timeline tracing charts: [https://2446.beast.tracing.cppalliance.org/index.html](https://2446.beast.tracing.cppalliance.org/index.html)

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2022-06-06 23:34:10 UTC  
> Updated_at: 2022-06-08 21:30:45 UTC  
> Url: https://github.com/boostorg/beast/pull/2446#issuecomment-1148036266  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2446?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2446](https://codecov.io/gh/boostorg/beast/pull/2446?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (77226ce) into [develop](https://codecov.io/gh/boostorg/beast/commit/925043e1e8df7474165f23c9a60223b0ab886be4?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (925043e) will **decrease** coverage by `0.03%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2446/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/beast/pull/2446?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2446      +/-   ##  
===========================================  
- Coverage    94.75%   94.72%   -0.04%       
===========================================  
  Files          152      152                
  Lines        11868    11868                
===========================================  
- Hits         11246    11242       -4       
- Misses         622      626       +4       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2446?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/impl/read.hpp](https://codecov.io/gh/boostorg/beast/pull/2446/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZWFkLmhwcA==) | `96.50% <0.00%> (-0.67%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2446?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2446?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [925043e...77226ce](https://codecov.io/gh/boostorg/beast/pull/2446?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 4

> Username: sehe  
> Created_at: 2022-06-08 15:49:09 UTC  
> Url: https://github.com/boostorg/beast/pull/2446#issuecomment-1150094282  

Huh. How did [code coverage change](https://github.com/boostorg/beast/pull/2446/checks?check_run_id=6765165461) in ... ping.hpp when the only file changed was qbk

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2022-06-08 20:26:55 UTC  
> Url: https://github.com/boostorg/beast/pull/2446#issuecomment-1150377545  

An automated preview of the documentation is available at [https://2446.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2446.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2022-06-08 20:41:56 UTC  
> Url: https://github.com/boostorg/beast/pull/2446#issuecomment-1150392962  

![pullrequest](https://2446.beast.tracing.cppalliance.org/pullrequest-6bf43013.png)  
Timeline tracing charts: [https://2446.beast.tracing.cppalliance.org/index.html](https://2446.beast.tracing.cppalliance.org/index.html)

---

## Comment 7

> Username: vinniefalco  
> Created_at: 2022-06-08 22:00:41 UTC  
> Url: https://github.com/boostorg/beast/pull/2446#issuecomment-1150458860  

This is good I suppose

---
