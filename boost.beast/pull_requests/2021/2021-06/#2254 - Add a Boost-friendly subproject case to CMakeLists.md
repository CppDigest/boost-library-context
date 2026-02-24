# #2254 Add a Boost-friendly subproject case to CMakeLists [Closed]

> Username: pdimov  
> Created at: 2021-06-04 17:35:23 UTC  
> Updated at: 2023-01-01 18:22:34 UTC  
> Closed at: 2021-06-06 17:51:18 UTC  
> Url: https://github.com/boostorg/beast/pull/2254  



---

## Review 1 [Commented]

> Username: madmongo1  
> Created_at: 2021-06-04 17:58:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2254#pullrequestreview-676540909  

I'm surprised `cmake_minimum_required (VERSION 3.5.1)` is allowed to come after an `if()`!  
  
Thank you for the PR

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2021-06-04 18:19:10 UTC  
> Updated_at: 2021-06-04 18:42:54 UTC  
> Url: https://github.com/boostorg/beast/pull/2254#issuecomment-854917246  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2254?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2254](https://codecov.io/gh/boostorg/beast/pull/2254?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (0d54fc0) into [develop](https://codecov.io/gh/boostorg/beast/commit/30abfdb9f789534bc920ff9f09b7cf213243e989?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (30abfdb) will **decrease** coverage by `0.00%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2254/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/beast/pull/2254?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2254      +/-   ##  
===========================================  
- Coverage    95.09%   95.08%   -0.01%       
===========================================  
  Files          153      153                
  Lines        11977    11977                
===========================================  
- Hits         11389    11388       -1       
- Misses         588      589       +1       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2254?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/impl/ping.hpp](https://codecov.io/gh/boostorg/beast/pull/2254/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmhwcA==) | `96.42% <0.00%> (-0.90%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2254?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2254?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [30abfdb...0d54fc0](https://codecov.io/gh/boostorg/beast/pull/2254?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 3

> Username: pdimov  
> Created_at: 2021-06-04 18:32:45 UTC  
> Url: https://github.com/boostorg/beast/pull/2254#issuecomment-854926087  

> I'm surprised `cmake_minimum_required (VERSION 3.5.1)` is allowed to come after an `if()`!  
  
It's not good practice because the policies can affect the behavior of `if`, but in this case it should be fine.

---
