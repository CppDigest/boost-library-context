# #2539 unquote takes `s` by reference. [Merged]

> Username: klemens-morgenstern  
> Created at: 2022-10-11 18:00:37 UTC  
> Updated at: 2022-10-13 01:27:52 UTC  
> Merged at: 2022-10-12 01:53:43 UTC  
> Closed at: 2022-10-12 01:53:43 UTC  
> Url: https://github.com/boostorg/beast/pull/2539  

Closes #471.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-10-11 18:27:18 UTC  
> Url: https://github.com/boostorg/beast/pull/2539#issuecomment-1275104946  

![pullrequest](https://2539.beast.tracing.cppalliance.org/pullrequest-7fa9dcb0.png)  
Timeline tracing charts: [https://2539.beast.tracing.cppalliance.org/index.html](https://2539.beast.tracing.cppalliance.org/index.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2022-10-11 18:49:29 UTC  
> Updated_at: 2022-10-12 01:52:29 UTC  
> Url: https://github.com/boostorg/beast/pull/2539#issuecomment-1275129828  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2539?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2539](https://codecov.io/gh/boostorg/beast/pull/2539?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (915d568) into [develop](https://codecov.io/gh/boostorg/beast/commit/8f74e0d46470c1c34acf81dec2347a201631de71?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (8f74e0d) will **decrease** coverage by `0.03%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2539/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/beast/pull/2539?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2539      +/-   ##  
===========================================  
- Coverage    94.72%   94.68%   -0.04%       
===========================================  
  Files          152      152                
  Lines        11882    11882                
===========================================  
- Hits         11255    11251       -4       
- Misses         627      631       +4       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2539?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/http/impl/rfc7230.hpp](https://codecov.io/gh/boostorg/beast/pull/2539/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvcmZjNzIzMC5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/http/impl/rfc7230.ipp](https://codecov.io/gh/boostorg/beast/pull/2539/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvcmZjNzIzMC5pcHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/beast/websocket/impl/read.hpp](https://codecov.io/gh/boostorg/beast/pull/2539/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZWFkLmhwcA==) | `96.49% <0.00%> (-0.67%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2539?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2539?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [8f74e0d...915d568](https://codecov.io/gh/boostorg/beast/pull/2539?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2022-10-12 01:32:21 UTC  
> Url: https://github.com/boostorg/beast/pull/2539#issuecomment-1275469858  

![pullrequest](https://2539.beast.tracing.cppalliance.org/pullrequest-ad03d158.png)  
Timeline tracing charts: [https://2539.beast.tracing.cppalliance.org/index.html](https://2539.beast.tracing.cppalliance.org/index.html)

---
