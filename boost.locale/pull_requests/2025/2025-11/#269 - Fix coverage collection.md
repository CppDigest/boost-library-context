# #269 Fix coverage collection [Merged]

> Username: Flamefire  
> Created at: 2025-11-02 13:25:23 UTC  
> Updated at: 2025-11-02 16:02:33 UTC  
> Merged at: 2025-11-02 15:40:22 UTC  
> Closed at: 2025-11-02 15:40:22 UTC  
> Url: https://github.com/boostorg/locale/pull/269  

Exclude uncovered lines in test_encoding which are only run on macos-15 with faulty iconv implementation  
  
Show more information for the coverage run on Windows GHA to find why ICU isn't used.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-11-02 14:11:33 UTC  
> Updated_at: 2025-11-02 16:02:33 UTC  
> Url: https://github.com/boostorg/locale/pull/269#issuecomment-3477993950  

## [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/269?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 95.79%. Comparing base ([`57cda42`](https://app.codecov.io/gh/boostorg/locale/commit/57cda421b79bb1fe034cd92a6dca8cbe3972c1d9?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`b38a271`](https://app.codecov.io/gh/boostorg/locale/commit/b38a271b48dc5f6aff8c22bfbe4a5b625eb0bf06?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 5 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/locale/pull/269/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/locale/pull/269?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #269      +/-   ##  
===========================================  
- Coverage    95.93%   95.79%   -0.14%       
===========================================  
  Files          119      119                
  Lines        10370    10346      -24       
===========================================  
- Hits          9948     9911      -37       
- Misses         422      435      +13       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/locale/pull/269?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [test/show\_config.cpp](https://app.codecov.io/gh/boostorg/locale/pull/269?src=pr&el=tree&filepath=test%2Fshow_config.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9zaG93X2NvbmZpZy5jcHA=) | `100.00% <100.00%> (+1.78%)` | :arrow_up: |  
| [test/test\_encoding.cpp](https://app.codecov.io/gh/boostorg/locale/pull/269?src=pr&el=tree&filepath=test%2Ftest_encoding.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2VuY29kaW5nLmNwcA==) | `100.00% <ø> (+0.68%)` | :arrow_up: |  
  
... and [13 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/locale/pull/269/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/locale/pull/269?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/269?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [57cda42...b38a271](https://app.codecov.io/gh/boostorg/locale/pull/269?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
