# #98 renamed `build_docs.sh` to `build_antora.sh` as per `ci_boost_release.py` requisites [Merged]

> Username: joaquintides  
> Created at: 2025-10-24 18:37:04 UTC  
> Updated at: 2025-10-30 09:45:47 UTC  
> Merged at: 2025-10-30 09:45:24 UTC  
> Closed at: 2025-10-30 09:45:24 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/98  

Antora currently has a limitation by which it must be run in a proper Git repo (as opposed to a submodule of a superproject). The Boost release script fixes that by doing some Git adjustments on those submodules it identifies as bulding Antora docs, which identification is done heuristically by looking for an `antora_docs.sh` or `build_antora.sh` file:  
  
https://github.com/boostorg/release-tools/blob/d27012edbe2516ce8c38798244a153a216ccba95/ci_boost_release.py#L495-L536

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-10-25 12:21:18 UTC  
> Updated_at: 2025-10-30 09:45:47 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/98#issuecomment-3446638879  

## [Codecov](https://app.codecov.io/gh/boostorg/dynamic_bitset/pull/98?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 96.56%. Comparing base ([`354c63c`](https://app.codecov.io/gh/boostorg/dynamic_bitset/commit/354c63cb24a59f283c9fcb9cd11ab970b7ddfeca?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`1235291`](https://app.codecov.io/gh/boostorg/dynamic_bitset/commit/12352912bed0a0b2a4fba68970cc586828284a61?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/dynamic_bitset/pull/98/graphs/tree.svg?width=650&height=150&src=pr&token=PVG5jth1ez&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/dynamic_bitset/pull/98?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #98   +/-   ##  
========================================  
  Coverage    96.56%   96.56%             
========================================  
  Files            5        5             
  Lines          785      785             
  Branches       233      233             
========================================  
  Hits           758      758             
  Misses          27       27             
```  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/dynamic_bitset/pull/98?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/dynamic_bitset/pull/98?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [354c63c...1235291](https://app.codecov.io/gh/boostorg/dynamic_bitset/pull/98?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
