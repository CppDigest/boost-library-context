# #278 Fix issue with newer PPC64 platforms aliasing `long double` to `__float128` [Merged]

> Username: mborland  
> Created at: 2025-07-30 13:20:58 UTC  
> Updated at: 2025-08-06 19:28:21 UTC  
> Merged at: 2025-08-06 14:03:21 UTC  
> Closed at: 2025-08-06 14:03:21 UTC  
> Url: https://github.com/boostorg/charconv/pull/278  

See: https://github.com/boostorg/boost/issues/1035

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-07-30 13:59:51 UTC  
> Updated_at: 2025-08-06 19:28:21 UTC  
> Url: https://github.com/boostorg/charconv/pull/278#issuecomment-3136484249  

## [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/278?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 94.89%. Comparing base ([`6bd2747`](https://app.codecov.io/gh/boostorg/charconv/commit/6bd274764d3473d577078d0a6e310c7c91ecf02a?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`8fc1ee0`](https://app.codecov.io/gh/boostorg/charconv/commit/8fc1ee04f215eb68c48ad26f0b8dbb395eea19f4?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/charconv/pull/278/graphs/tree.svg?width=650&height=150&src=pr&token=jTnxtbJoTv&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/charconv/pull/278?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #278   +/-   ##  
========================================  
  Coverage    94.89%   94.89%             
========================================  
  Files           71       71             
  Lines         9123     9123             
========================================  
  Hits          8657     8657             
  Misses         466      466             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/charconv/pull/278?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [src/from\_chars.cpp](https://app.codecov.io/gh/boostorg/charconv/pull/278?src=pr&el=tree&filepath=src%2Ffrom_chars.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Zyb21fY2hhcnMuY3Bw) | `83.78% <ø> (ø)` | |  
| [src/to\_chars.cpp](https://app.codecov.io/gh/boostorg/charconv/pull/278?src=pr&el=tree&filepath=src%2Fto_chars.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3RvX2NoYXJzLmNwcA==) | `92.94% <ø> (ø)` | |  
| [src/to\_chars\_float\_impl.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/278?src=pr&el=tree&filepath=src%2Fto_chars_float_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3RvX2NoYXJzX2Zsb2F0X2ltcGwuaHBw) | `83.39% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/charconv/pull/278?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/278?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [6bd2747...8fc1ee0](https://app.codecov.io/gh/boostorg/charconv/pull/278?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
