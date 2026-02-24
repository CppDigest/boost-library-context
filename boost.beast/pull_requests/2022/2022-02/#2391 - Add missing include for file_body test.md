# #2391 Add missing include for file_body test [Closed]

> Username: omartijn  
> Created at: 2022-02-25 18:43:33 UTC  
> Updated at: 2022-03-09 16:02:58 UTC  
> Closed at: 2022-03-09 15:41:10 UTC  
> Url: https://github.com/boostorg/beast/pull/2391  

I tried running the tests on Linux and macOS. The `file_body` test did not compile on either of those platforms. Adding the `fstream` include fixes it.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-02-25 19:08:36 UTC  
> Url: https://github.com/boostorg/beast/pull/2391#issuecomment-1051119826  

![pullrequest](https://2391.beast.tracing.cppalliance.org/pullrequest-04fd2990.png)  
Timeline tracing charts: [https://2391.beast.tracing.cppalliance.org/index.html](https://2391.beast.tracing.cppalliance.org/index.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2022-02-25 19:36:11 UTC  
> Updated_at: 2022-02-25 19:37:29 UTC  
> Url: https://github.com/boostorg/beast/pull/2391#issuecomment-1051138292  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2391?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2391](https://codecov.io/gh/boostorg/beast/pull/2391?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (35c8ddb) into [develop](https://codecov.io/gh/boostorg/beast/commit/5bdd1efb8be740bb5425adc054c990818134f3e2?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (5bdd1ef) will **decrease** coverage by `0.03%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2391/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/beast/pull/2391?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2391      +/-   ##  
===========================================  
- Coverage    94.88%   94.85%   -0.04%       
===========================================  
  Files          151      151                
  Lines        12230    12230                
===========================================  
- Hits         11605    11601       -4       
- Misses         625      629       +4       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2391?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/impl/read.hpp](https://codecov.io/gh/boostorg/beast/pull/2391/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZWFkLmhwcA==) | `96.49% <0.00%> (-0.67%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2391?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2391?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [5bdd1ef...35c8ddb](https://codecov.io/gh/boostorg/beast/pull/2391?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---
