# #3027 Annotate facade header with IWYU export annotation [Closed]

> Username: hzeller  
> Created at: 2025-08-30 15:21:57 UTC  
> Updated at: 2026-02-09 10:23:18 UTC  
> Closed at: 2026-02-09 10:23:06 UTC  
> Url: https://github.com/boostorg/beast/pull/3027  

Without that annotation, tools such as `clang-tidy` or the `clangd` language server (as well as many other tools) will complain about headers not directly providing a symbol if users include `beast.hpp`; With this annotation, they know.  
  
Documentation IWYU  
https://github.com/include-what-you-use/include-what-you-use/blob/master/docs/IWYUPragmas.md#iwyu-pragma-begin_exportsend_exports  
  
Documentation llvm include cleaner/clang-tidy/clangd https://clangd.llvm.org/design/include-cleaner#iwyu-pragmas

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-08-30 16:39:17 UTC  
> Updated_at: 2026-02-09 10:23:18 UTC  
> Url: https://github.com/boostorg/beast/pull/3027#issuecomment-3239391962  

## [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/3027?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 93.11%. Comparing base ([`9b24b28`](https://app.codecov.io/gh/boostorg/beast/commit/9b24b28d502946583300d3e58767cb30a8ec8486?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`17604c3`](https://app.codecov.io/gh/boostorg/beast/commit/17604c3417a88a8d65b1bb68c3bc674c41a26e9e?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 24 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/beast/pull/3027/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/beast/pull/3027?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #3027      +/-   ##  
===========================================  
+ Coverage    93.09%   93.11%   +0.02%       
===========================================  
  Files          176      176                
  Lines        13647    13647                
===========================================  
+ Hits         12704    12708       +4       
+ Misses         943      939       -4       
```  
[see 1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/beast/pull/3027/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/beast/pull/3027?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/3027?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [9b24b28...17604c3](https://app.codecov.io/gh/boostorg/beast/pull/3027?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 2

> Username: sehe  
> Created_at: 2025-08-30 17:27:09 UTC  
> Url: https://github.com/boostorg/beast/pull/3027#issuecomment-3239416305  

Does IWYU not honour -isystem includes (e.g. via CMake SYSTEM includes)?

---

## Comment 3

> Username: hzeller  
> Created_at: 2025-08-30 22:14:52 UTC  
> Url: https://github.com/boostorg/beast/pull/3027#issuecomment-3239576860  

`-isystem` does not make a difference here.  
  
Often, warnings for `-isystem`-provided headers would be suppressed.  
  
But here we have a different situation, and want to guide warnings in the project itself: IWYU marks includes relevant or not given if they provide a symbol directly in a particular header (and by default it does not follow further includes). That makes sure that symbols don't come from transitive dependencies (accidental visibility of symbols due to the textual concatenation of C headers, the very thing that IWYU wants to help prevent).  
  
So if we have a header that is indeed meant to provide a symbol one nest-level deeper, we can annotate it like what I've done here.

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2025-08-31 01:20:25 UTC  
> Url: https://github.com/boostorg/beast/pull/3027#issuecomment-3239650458  

This looks benign

---

## Comment 5

> Username: sehe  
> Created_at: 2025-09-01 16:17:46 UTC  
> Url: https://github.com/boostorg/beast/pull/3027#issuecomment-3242857460  

> But here we have a different situation  
  
Thank you. It's more clear to me now that this is not "silencing warnings" but annotating the _facade header(s)_ specifically.

---

## Comment 6

> Username: ashtum  
> Created_at: 2025-09-04 05:44:10 UTC  
> Url: https://github.com/boostorg/beast/pull/3027#issuecomment-3251991284  

@hzeller, I'm using clangd-20 and clang-tidy-20 with the `-misc-include-cleaner` check, but I wasn't able to reproduce the warning you mentioned. Could you please provide more details on how to reproduce the issue?

---

## Comment 7

> Username: ashtum  
> Created_at: 2026-02-09 10:23:06 UTC  
> Url: https://github.com/boostorg/beast/pull/3027#issuecomment-3870796850  

I’m closing this for now as I wasn’t able to reproduce the reported issue. I’d appreciate it if the OP could provide a bit more information so we can investigate further.

---
