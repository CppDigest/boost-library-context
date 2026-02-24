# #262 make header `src/shread/mo_hash.hpp` guarded [Merged]

> Username: shyeyian  
> Created at: 2025-06-16 07:22:44 UTC  
> Updated at: 2025-06-17 08:27:50 UTC  
> Merged at: 2025-06-17 08:27:29 UTC  
> Closed at: 2025-06-17 08:27:29 UTC  
> Url: https://github.com/boostorg/locale/pull/262  

Header `src/shared/mo_hash.hpp` is unguarded.   
  
Most times it works. But recently I'm trying to compile `boost/locale` into a [`C++20 module`](https://cppreference.com/w/cpp/language/modules.html). I found this file better be guarded in this compiling-case, as `C++20 module` treats function-declarations in `.hpp` files as translatable ones (which cannot be multi-defined).  
  
One tiny change: `#ifdef xxx #define xxx` to make header `mo_hash.hpp` guarded.  
  
Thank you!  
*This is an amazing library and I will start it :)*

---

## Review 1 [Approved]

> Username: Flamefire  
> Created_at: 2025-06-16 07:41:49 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/locale/pull/262#pullrequestreview-2930963813  

Makes sense, thanks!  
  
If you get the module working please reach out to the Boost mailing list as there have been discussions on how to best approach this

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2025-06-16 17:26:32 UTC  
> Updated_at: 2025-06-17 08:27:50 UTC  
> Url: https://github.com/boostorg/locale/pull/262#issuecomment-2977442403  

## [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/262?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 95.96%. Comparing base [(`d6e3119`)](https://app.codecov.io/gh/boostorg/locale/commit/d6e3119892ca08fbd3d934d22dd821dfb230e3c4?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`7155f6d`)](https://app.codecov.io/gh/boostorg/locale/commit/7155f6df34159b84dc5cdf7de3d911d7a1dccedb?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/locale/pull/262/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/locale/pull/262?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #262   +/-   ##  
========================================  
  Coverage    95.96%   95.96%             
========================================  
  Files          119      119             
  Lines        10611    10611             
========================================  
  Hits         10183    10183             
  Misses         428      428             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/locale/pull/262?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [src/shared/mo\_hash.hpp](https://app.codecov.io/gh/boostorg/locale/pull/262?src=pr&el=tree&filepath=src%2Fshared%2Fmo_hash.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3NoYXJlZC9tb19oYXNoLmhwcA==) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/locale/pull/262?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/262?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [d6e3119...7155f6d](https://app.codecov.io/gh/boostorg/locale/pull/262?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
