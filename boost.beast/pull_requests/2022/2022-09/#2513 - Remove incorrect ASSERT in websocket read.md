# #2513 Remove incorrect ASSERT in websocket read [Merged]

> Username: madmongo1  
> Created at: 2022-09-01 13:46:34 UTC  
> Updated at: 2022-09-01 15:42:51 UTC  
> Merged at: 2022-09-01 15:42:51 UTC  
> Closed at: 2022-09-01 15:42:51 UTC  
> Url: https://github.com/boostorg/beast/pull/2513  

fix #2264

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-09-01 14:12:15 UTC  
> Url: https://github.com/boostorg/beast/pull/2513#issuecomment-1234339868  

![pullrequest](https://2513.beast.tracing.cppalliance.org/pullrequest-4307a48a.png)  
Timeline tracing charts: [https://2513.beast.tracing.cppalliance.org/index.html](https://2513.beast.tracing.cppalliance.org/index.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2022-09-01 14:34:45 UTC  
> Updated_at: 2022-09-01 14:44:53 UTC  
> Url: https://github.com/boostorg/beast/pull/2513#issuecomment-1234369588  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2513?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2513](https://codecov.io/gh/boostorg/beast/pull/2513?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (d86a61f) into [develop](https://codecov.io/gh/boostorg/beast/commit/af79b4d0fdbb3edf842c9181253d62cc71f33e77?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (af79b4d) will **decrease** coverage by `0.04%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2513/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/beast/pull/2513?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2513      +/-   ##  
===========================================  
- Coverage    94.75%   94.71%   -0.05%       
===========================================  
  Files          152      152                
  Lines        11868    11867       -1       
===========================================  
- Hits         11246    11240       -6       
- Misses         622      627       +5       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2513?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/impl/read.hpp](https://codecov.io/gh/boostorg/beast/pull/2513/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZWFkLmhwcA==) | `96.49% <ø> (-0.68%)` | :arrow_down: |  
| [include/boost/beast/websocket/impl/ping.hpp](https://codecov.io/gh/boostorg/beast/pull/2513/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmhwcA==) | `94.73% <0.00%> (-0.88%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2513?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2513?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [af79b4d...d86a61f](https://codecov.io/gh/boostorg/beast/pull/2513?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 3

> Username: gopalak  
> Created_at: 2022-09-01 15:35:14 UTC  
> Url: https://github.com/boostorg/beast/pull/2513#issuecomment-1234450128  

@madmongo1 @vinniefalco how can I get notification when this is merged to develop so I can pull and spin a local build  
appreciate the help.

---
