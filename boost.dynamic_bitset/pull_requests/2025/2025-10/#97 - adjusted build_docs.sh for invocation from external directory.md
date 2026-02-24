# #97 adjusted build_docs.sh for invocation from external directory [Merged]

> Username: joaquintides  
> Created at: 2025-10-22 17:57:42 UTC  
> Updated at: 2025-10-24 18:19:38 UTC  
> Merged at: 2025-10-24 16:01:01 UTC  
> Closed at: 2025-10-24 16:01:01 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/97  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-10-22 18:22:36 UTC  
> Updated_at: 2025-10-23 00:07:05 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/97#issuecomment-3433658409  

## [Codecov](https://app.codecov.io/gh/boostorg/dynamic_bitset/pull/97?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 96.56%. Comparing base ([`3fab9df`](https://app.codecov.io/gh/boostorg/dynamic_bitset/commit/3fab9dfdf7219faff800daf4ec694890ca2e0718?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`9b95bef`](https://app.codecov.io/gh/boostorg/dynamic_bitset/commit/9b95befbfce628f80b434d1c0cb7325b81eb3d98?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/dynamic_bitset/pull/97/graphs/tree.svg?width=650&height=150&src=pr&token=PVG5jth1ez&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/dynamic_bitset/pull/97?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #97   +/-   ##  
========================================  
  Coverage    96.56%   96.56%             
========================================  
  Files            5        5             
  Lines          786      786             
  Branches       233      233             
========================================  
  Hits           759      759             
  Misses          27       27             
```  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/dynamic_bitset/pull/97?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/dynamic_bitset/pull/97?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [3fab9df...9b95bef](https://app.codecov.io/gh/boostorg/dynamic_bitset/pull/97?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Review 2 [Changes requested]

> Username: gennaroprota  
> Created_at: 2025-10-24 14:22:04 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/dynamic_bitset/pull/97#pullrequestreview-3377132922  

I guess  
  
`SCRIPT_DIR=$( cd -- "$( dirname -- "${BASH_SOURCE[0]}" )" &> /dev/null && pwd )`  
  
is a way to get the *absolute* path of the script. But we don't need that, so the simpler  
  
`SCRIPT_DIR=$( dirname -- "${BASH_SOURCE[0]}" )`  
  
would just be the same for us.

---
