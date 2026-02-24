# #60 Docs: retry ui-bundle download [Merged]

> Username: sdarwin  
> Created at: 2026-01-05 17:51:00 UTC  
> Updated at: 2026-01-24 17:23:57 UTC  
> Merged at: 2026-01-24 17:23:46 UTC  
> Closed at: 2026-01-24 17:23:46 UTC  
> Url: https://github.com/boostorg/openmethod/pull/60  

Sometimes the superproject build fails because of a remote download.  This pull request adds retries to the antora ui-bundle download.        
On a related topic, very recently Joaquin updated boostorg/unordered, removing duplicate copies of the documentation, and he has another plan to remove the node_modules folder after docs builds.       
Keep an eye on boostorg/unordered, copy these modifications over.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2026-01-05 19:40:54 UTC  
> Updated_at: 2026-01-24 17:23:57 UTC  
> Url: https://github.com/boostorg/openmethod/pull/60#issuecomment-3711840992  

## [Codecov](https://app.codecov.io/gh/boostorg/openmethod/pull/60?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 92.02%. Comparing base ([`e0ead3b`](https://app.codecov.io/gh/boostorg/openmethod/commit/e0ead3bf7106f614bb8ba8419e0c35fdd95c2ea4?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`8f003b5`](https://app.codecov.io/gh/boostorg/openmethod/commit/8f003b5681a423e3a5af53303952bbba91e4fc8c?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 9 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/openmethod/pull/60/graphs/tree.svg?width=650&height=150&src=pr&token=YLF1dcp5xA&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/openmethod/pull/60?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #60      +/-   ##  
===========================================  
- Coverage    92.03%   92.02%   -0.01%       
===========================================  
  Files           40       40                
  Lines         2838     2835       -3       
  Branches      1354     1354                
===========================================  
- Hits          2612     2609       -3       
  Misses         187      187                
  Partials        39       39                
```  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/openmethod/pull/60?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/openmethod/pull/60?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [f53122e...8f003b5](https://app.codecov.io/gh/boostorg/openmethod/pull/60?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2026-01-06 15:06:34 UTC  
> Url: https://github.com/boostorg/openmethod/pull/60#issuecomment-3715055066  

An automated preview of the documentation is available at [https://60.openmethod.prtest3.cppalliance.org/libs/openmethod/doc/html/index.html](https://60.openmethod.prtest3.cppalliance.org/libs/openmethod/doc/html/index.html)  
  
If more commits are pushed to the pull request, the docs will rebuild at the same URL.  
  
2026-01-06 15:06:33 UTC

---

## Comment 3

> Username: sdarwin  
> Created_at: 2026-01-08 14:05:04 UTC  
> Url: https://github.com/boostorg/openmethod/pull/60#issuecomment-3724029047  

Update: the newest doc/Jamfile from unordered succeeds in removing `node_modules`.

---
