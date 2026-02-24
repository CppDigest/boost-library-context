# #721 Modernize visualizer doc and maybe future plan [Merged]

> Username: ckormanyos  
> Created at: 2025-07-11 17:44:25 UTC  
> Updated at: 2025-07-12 20:54:43 UTC  
> Merged at: 2025-07-12 20:09:05 UTC  
> Closed at: 2025-07-12 20:09:05 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/721  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-07-11 18:19:40 UTC  
> Updated_at: 2025-07-12 20:54:43 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/721#issuecomment-3063282272  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/721?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.7%. Comparing base [(`d2f202c`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/d2f202cf81f3fb0c56d53fe8e42796b29c0ed3fd?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`50901d3`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/50901d32104cc8b6bf18d5e6ab931de1ef5dd64a?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 4 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/721/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/721?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff           @@  
##           develop    #721   +/-   ##  
=======================================  
  Coverage     94.7%   94.7%             
=======================================  
  Files          296     296             
  Lines        30881   30881             
=======================================  
  Hits         29233   29233             
  Misses        1648    1648             
```  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/721?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/721?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [d2f202c...50901d3](https://app.codecov.io/gh/boostorg/multiprecision/pull/721?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2025-07-11 18:32:22 UTC  
> Updated_at: 2025-07-11 18:32:39 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/721#issuecomment-3063324216  

So I took a stab at modernizing the Visualizer docs.  
  
Any comments on how I reworded the section in this PR?  
  
For the future, which is not now, I even created #720 and believe I can chew through the necessary `C#` to modernize at least one visualizer. I've done a few things like this in the past. But uniting MP's radical templates with managed code may prove challenging.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2025-07-12 16:09:48 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/721#issuecomment-3065759140  

IMO the old visualizers are basically obsolete, maybe just remove for now?

---
