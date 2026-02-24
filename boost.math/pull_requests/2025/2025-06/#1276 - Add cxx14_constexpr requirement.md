# #1276 Add cxx14_constexpr requirement [Merged]

> Username: mborland  
> Created at: 2025-06-16 15:31:40 UTC  
> Updated at: 2025-06-17 08:39:48 UTC  
> Merged at: 2025-06-16 17:00:01 UTC  
> Closed at: 2025-06-16 17:00:01 UTC  
> Url: https://github.com/boostorg/math/pull/1276  

Partial for: #1275

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-06-16 16:26:36 UTC  
> Updated_at: 2025-06-16 22:24:25 UTC  
> Url: https://github.com/boostorg/math/pull/1276#issuecomment-2977280155  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1276?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 95.07%. Comparing base [(`39c2fda`)](https://app.codecov.io/gh/boostorg/math/commit/39c2fda279e5ad4022643c7cf04888f136d36f76?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`eb2930d`)](https://app.codecov.io/gh/boostorg/math/commit/eb2930d8cc47f14dceefecabe83b2ff3a0013583?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 2 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1276/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1276?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1276   +/-   ##  
========================================  
  Coverage    95.07%   95.07%             
========================================  
  Files          792      792             
  Lines        66882    66882             
========================================  
  Hits         63589    63589             
  Misses        3293     3293             
```  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1276?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1276?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [39c2fda...eb2930d](https://app.codecov.io/gh/boostorg/math/pull/1276?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2025-06-16 17:22:50 UTC  
> Url: https://github.com/boostorg/math/pull/1276#issuecomment-2977432590  

While completely harmless, I'm not at all convinced that this fixes the original issue (or that there is an easy fix), the point being that only a vanishingly small number of dependents will be using the Math binaries as opposed to the headers, and if you're just using the headers, then there would appear to be no appropriate target to annotate with our requirements.  Unless I'm missing something - @Lastique.

---

## Comment 3

> Username: mborland  
> Created_at: 2025-06-16 17:37:31 UTC  
> Url: https://github.com/boostorg/math/pull/1276#issuecomment-2977471798  

That's true. Theoretically I could port: https://github.com/boostorg/math/pull/1277 to config and then we could `static_assert` in the boost.math config header if it's not set. That would be a slightly nicer error message than what he had in his issue.

---

## Comment 4

> Username: Lastique  
> Created_at: 2025-06-16 18:23:44 UTC  
> Url: https://github.com/boostorg/math/pull/1276#issuecomment-2977611536  

Given the Boost.Build modularization efforts, header-only libraries now also have targets in b2. Those targets can also have the C++ requirements, as well as dependencies, attached. Those requirements and dependencies propagate to downstream users.  
  
Specifically, if Boost.Math ever becomes header-only, it will still have a b2 target with all C++ requirements attached. Downstream users will still "link" with that target and thus inherit the C++ requirements.  
  
Now, to this PR. I'm not sure `cxx14_constexpr` alone will be enough to fix the problem. The compiler error messages in Boost.Parameter tests suggest that it is the `<type_traits>` lacking C++14 stuff that is the problem. And for that there currently is no Boost.Config check. Either Boost.Math will have to add its own check, or (preferably) one needs to be added to Boost.Config.

---

## Comment 5

> Username: mborland  
> Created_at: 2025-06-16 18:48:33 UTC  
> Url: https://github.com/boostorg/math/pull/1276#issuecomment-2977709816  

> Given the Boost.Build modularization efforts, header-only libraries now also have targets in b2. Those targets can also have the C++ requirements, as well as dependencies, attached. Those requirements and dependencies propagate to downstream users.  
>   
> Specifically, if Boost.Math ever becomes header-only, it will still have a b2 target with all C++ requirements attached. Downstream users will still "link" with that target and thus inherit the C++ requirements.  
>   
> Now, to this PR. I'm not sure `cxx14_constexpr` alone will be enough to fix the problem. The compiler error messages in Boost.Parameter tests suggest that it is the `<type_traits>` lacking C++14 stuff that is the problem. And for that there currently is no Boost.Config check. Either Boost.Math will have to add its own check, or (preferably) one needs to be added to Boost.Config.  
  
#1277 Should cover the type traits issue. For that one I had to ask Rene how to globally apply a library specific configuration since `requires` in the Jamfile only applies to Boost.Config.

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2025-06-17 08:17:52 UTC  
> Url: https://github.com/boostorg/math/pull/1276#issuecomment-2979392352  

> Specifically, if Boost.Math ever becomes header-only, it will still have a b2 target with all C++ requirements attached. Downstream users will still "link" with that target and thus inherit the C++ requirements.  
  
In 99.999% of cases it *is* header only, if users are linking to the compiled library just to express a dependency then that's flat out wrong.

---

## Comment 7

> Username: Lastique  
> Created_at: 2025-06-17 08:32:33 UTC  
> Url: https://github.com/boostorg/math/pull/1276#issuecomment-2979444434  

Well, the dependency is necessary at least to get include paths to Boost.Math headers, as the monolithic include directory may no longer exist.  
  
If you want to make linking with Boost.Math binary optional, you need to introduce a new target that corresponds to the header-only Boost.Math. The existing target should still link, since otherwise you would break that 0.001% of users.

---

## Comment 8

> Username: Lastique  
> Created_at: 2025-06-17 08:39:48 UTC  
> Url: https://github.com/boostorg/math/pull/1276#issuecomment-2979467425  

BTW, the same is true with CMake, only it was the case from the start. In CMake, you also have targets to express dependencies, whether those targets are built or not.

---
