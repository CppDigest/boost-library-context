# #944 Docs: retry ui-bundle downloads [Merged]

> Username: sdarwin  
> Created at: 2025-11-07 14:06:14 UTC  
> Updated at: 2025-11-13 12:17:37 UTC  
> Merged at: 2025-11-13 05:31:53 UTC  
> Closed at: 2025-11-13 05:31:53 UTC  
> Url: https://github.com/boostorg/url/pull/944  

Any download during a CI build might potentially fail so it's convenient to retry. This PR includes a new extension "@cppalliance/antora-downloads-extension" with a retry loop for the ui-bundle.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2025-11-07 14:09:16 UTC  
> Updated_at: 2025-11-13 05:21:06 UTC  
> Url: https://github.com/boostorg/url/pull/944#issuecomment-3502775711  

An automated preview of the documentation is available at [https://944.url.prtest2.cppalliance.org/index.html](https://944.url.prtest2.cppalliance.org/index.html)  
  
If more commits are pushed to the pull request, the docs will rebuild at the same URL.  
  
2025-11-13 05:21:05 UTC

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2025-11-07 14:26:01 UTC  
> Updated_at: 2025-11-13 05:31:37 UTC  
> Url: https://github.com/boostorg/url/pull/944#issuecomment-3502867830  

GCOVR code coverage report [https://944.url.prtest2.cppalliance.org/gcovr/index.html](https://944.url.prtest2.cppalliance.org/gcovr/index.html)  
LCOV code coverage report [https://944.url.prtest2.cppalliance.org/genhtml/index.html](https://944.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://944.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://944.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)  
  
Build time: 2025-11-13 05:31:36 UTC

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2025-11-07 20:21:46 UTC  
> Updated_at: 2025-11-13 12:17:37 UTC  
> Url: https://github.com/boostorg/url/pull/944#issuecomment-3504755851  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/944?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 99.21%. Comparing base ([`bbe9700`](https://app.codecov.io/gh/boostorg/url/commit/bbe9700fedb4897e46bc14a64de4088543054b7c?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`da6edae`](https://app.codecov.io/gh/boostorg/url/commit/da6edaefbd4f37b7b108e873f341caa76ca2b558?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/944/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/944?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #944   +/-   ##  
========================================  
  Coverage    99.21%   99.21%             
========================================  
  Files          159      159             
  Lines         8684     8684             
========================================  
  Hits          8616     8616             
  Misses          68       68             
```  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/944?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/944?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [bbe9700...da6edae](https://app.codecov.io/gh/boostorg/url/pull/944?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
