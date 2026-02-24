# #2673 Remove unused failure reporting code [Merged]

> Username: slowriot  
> Created at: 2023-04-24 16:50:54 UTC  
> Updated at: 2023-05-12 10:46:36 UTC  
> Merged at: 2023-05-12 10:46:36 UTC  
> Closed at: 2023-05-12 10:46:36 UTC  
> Url: https://github.com/boostorg/beast/pull/2673  

The `fail` function is used in the other websocket examples, and presumably was included here for that reason.  However, it is not actually used in this example.  
  
This PR removes the unused function.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2023-04-24 17:21:27 UTC  
> Url: https://github.com/boostorg/beast/pull/2673#issuecomment-1520555009  

![pullrequest](https://2673.beast.tracing.cppalliance.org/pullrequest-437b18cc.png)  
Timeline tracing charts: [https://2673.beast.tracing.cppalliance.org/index.html](https://2673.beast.tracing.cppalliance.org/index.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2023-05-12 08:18:59 UTC  
> Updated_at: 2023-05-12 08:21:27 UTC  
> Url: https://github.com/boostorg/beast/pull/2673#issuecomment-1545363167  

## [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2673?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2673](https://app.codecov.io/gh/boostorg/beast/pull/2673?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (6d19ce5) into [develop](https://app.codecov.io/gh/boostorg/beast/commit/b0996f099ac4cb024675b0b320f07e2297c3130c?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (b0996f0) will **decrease** coverage by `0.03%`.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/beast/pull/2673/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/beast/pull/2673?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2673      +/-   ##  
===========================================  
- Coverage    92.96%   92.94%   -0.03%       
===========================================  
  Files          177      177                
  Lines        13651    13658       +7       
===========================================  
+ Hits         12691    12694       +3       
- Misses         960      964       +4       
```  
  
  
[see 3 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/beast/pull/2673/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/beast/pull/2673?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2673?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [b0996f0...6d19ce5](https://app.codecov.io/gh/boostorg/beast/pull/2673?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
