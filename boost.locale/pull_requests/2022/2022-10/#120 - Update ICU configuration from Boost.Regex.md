# #120 Update ICU configuration from Boost.Regex [Merged]

> Username: Flamefire  
> Created at: 2022-10-18 11:22:58 UTC  
> Updated at: 2022-10-19 12:25:17 UTC  
> Merged at: 2022-10-19 12:25:13 UTC  
> Closed at: 2022-10-19 12:25:13 UTC  
> Url: https://github.com/boostorg/locale/pull/120  

Use the same mechanism for getting the ICU configuration as Boost.Regex does.  
  
Error on use of `ICU_LINK` or `ICU_LINK_LOCALE` now that we have proper support.  
  
Closes #70 as the work of @SSE4 is integrated here     
Fixes #55  
  
/cc @grafikrobot  
  
TODO: Check that the omission of `<dll-path>` doesn't break anything.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-10-18 15:38:01 UTC  
> Updated_at: 2022-10-19 12:24:42 UTC  
> Url: https://github.com/boostorg/locale/pull/120#issuecomment-1282595581  

# [Codecov](https://codecov.io/gh/boostorg/locale/pull/120?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#120](https://codecov.io/gh/boostorg/locale/pull/120?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (cf79b51) into [develop](https://codecov.io/gh/boostorg/locale/commit/6a9248bb470b52e36510c075652775415f206747?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (6a9248b) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
> :exclamation: Current head cf79b51 differs from pull request most recent head bf0e7db. Consider uploading reports for the commit bf0e7db to get more accurate results  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/locale/pull/120/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/locale/pull/120?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #120   +/-   ##  
========================================  
  Coverage    88.46%   88.46%             
========================================  
  Files          100      100             
  Lines         8982     8982             
========================================  
  Hits          7946     7946             
  Misses        1036     1036             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/locale/pull/120?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/locale/pull/120?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [6a9248b...bf0e7db](https://codecov.io/gh/boostorg/locale/pull/120?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: Flamefire  
> Created_at: 2022-10-18 20:32:42 UTC  
> Url: https://github.com/boostorg/locale/pull/120#issuecomment-1282970352  

@grafikrobot I'm unsure about the last commit [Make icu-path non-local](https://github.com/boostorg/locale/pull/120/commits/2685d774de0035b8cf04b4f6379e1c5d9a7983cf)  
  
Is this the correct way? Without it the variable is not set in the `path_options` rule and I wasn't able to find information about `local`/scopes in B2

---

## Comment 3

> Username: grafikrobot  
> Created_at: 2022-10-19 01:15:01 UTC  
> Url: https://github.com/boostorg/locale/pull/120#issuecomment-1283228173  

> @grafikrobot I'm unsure about the last commit [Make icu-path non-local](https://github.com/boostorg/locale/pull/120/commits/2685d774de0035b8cf04b4f6379e1c5d9a7983cf)  
>   
> Is this the correct way? Without it the variable is not set in the `path_options` rule and I wasn't able to find information about `local`/scopes in B2  
  
That's correct. The `local` var only lasts as long as the evaluated scope of the jamfile. And since it's evaluated once. And afterwards the rule is called the var is gone. One convention we do use is to prefix module global variables with a period ("."). I.e. `.icu-path = ...`. To designate that they are actually private vars.

---
