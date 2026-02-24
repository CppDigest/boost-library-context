# #1274 docs: add Windows install instructions [Closed]

> Username: SasiKiran4328  
> Created at: 2025-06-16 07:41:48 UTC  
> Updated at: 2025-06-17 04:31:11 UTC  
> Closed at: 2025-06-16 16:01:37 UTC  
> Url: https://github.com/boostorg/math/pull/1274  

Adds Windows build instructions using Chocolatey and CMake for Boost.Math.

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2025-06-16 10:28:39 UTC  
> Url: https://github.com/boostorg/math/pull/1274#issuecomment-2976008997  

Hi @SasiKiran4328 our repos require approval to run CI for first-time contributors, so I've just approved your workflow run on GHA.  
  
Cc: @jzmaddock and @mborland

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2025-06-16 10:32:06 UTC  
> Updated_at: 2025-06-16 10:56:11 UTC  
> Url: https://github.com/boostorg/math/pull/1274#issuecomment-2976018554  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1274?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 95.07%. Comparing base [(`39c2fda`)](https://app.codecov.io/gh/boostorg/math/commit/39c2fda279e5ad4022643c7cf04888f136d36f76?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`0fd5520`)](https://app.codecov.io/gh/boostorg/math/commit/0fd5520777eca6eb9b2e6e828783286f5126c9f1?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1274/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1274?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1274   +/-   ##  
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
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1274?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1274?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [39c2fda...0fd5520](https://app.codecov.io/gh/boostorg/math/pull/1274?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Review 3 [Changes requested]

> Username: mborland  
> Created_at: 2025-06-16 11:18:09 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/math/pull/1274#pullrequestreview-2931683822  

Why do we need chocolately when you can get everything you need from the MSVC installer? Also, this seems to be missing the rest of the instructions.

---

## Comment 4

> Username: SasiKiran4328  
> Created_at: 2025-06-16 13:18:42 UTC  
> Url: https://github.com/boostorg/math/pull/1274#issuecomment-2976632118  

Thank you @mborland for reviewing my previous changes and pointing out the Chocolatey concerns   
  
I’ve now restructured the Windows installation instructions based on your feedback. The updated section is simplified, beginner-friendly, and avoids external package managers like Chocolatey.  
  
Please let me know if there’s anything else I can improve. I'm still learning and truly appreciate your guidance

---

## Comment 5

> Username: SasiKiran4328  
> Created_at: 2025-06-16 13:26:19 UTC  
> Url: https://github.com/boostorg/math/pull/1274#issuecomment-2976657285  

Hi Sir! @ckormanyos Thank you so much for approving the workflow run   
I wasn't aware of that CI policy for first-time contributors. Really appreciate the support! Excited to see the checks run now

---

## Review 6 [Changes requested]

> Username: mborland  
> Created_at: 2025-06-16 15:04:07 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/math/pull/1274#pullrequestreview-2932523063  

Did you try and run this? There's already a build folder with files in it so you can't `mkdir` a new one. Then when you "build" outside of the boost tree the library assumes you're in standalone mode. This builds nothing since it's header only in the mode.

---

## Comment 7

> Username: SasiKiran4328  
> Created_at: 2025-06-16 15:43:00 UTC  
> Url: https://github.com/boostorg/math/pull/1274#issuecomment-2977137624  

Hi Sir @mborland,  
Thank you for pointing that out and for the helpful explanation! 🙏🏼  
You're absolutely right—I overlooked the fact that the repo already has a build folder and didn't consider the implications of standalone mode for a header-only library.  
  
I'll correct the instructions to build within the Boost tree using a custom build folder like cmake-build. Appreciate your time and guidance. I’ll push the fix shortly.

---

## Comment 8

> Username: mborland  
> Created_at: 2025-06-16 16:01:37 UTC  
> Updated_at: 2025-06-16 16:01:59 UTC  
> Url: https://github.com/boostorg/math/pull/1274#issuecomment-2977200617  

> Hi Sir @mborland, Thank you for pointing that out and for the helpful explanation! 🙏🏼 You're absolutely right—I overlooked the fact that the repo already has a build folder and didn't consider the implications of standalone mode for a header-only library.  
>   
> I'll correct the instructions to build within the Boost tree using a custom build folder like cmake-build. Appreciate your time and guidance. I’ll push the fix shortly.  
  
Those instructions already exist in the docs for the libraries, and on the Boost.Cmake page. Our directions are specifically for development and already outline how to do this. I am going to close this since I think competing information is confusing at best since we don't run the CMake Infrastructure, and our CML is generated automatically.

---

## Comment 9

> Username: SasiKiran4328  
> Created_at: 2025-06-17 04:31:10 UTC  
> Url: https://github.com/boostorg/math/pull/1274#issuecomment-2978898428  

Hi @mborland Sir,  
  
Thank you so much for the clarification and for taking the time to explain this so thoroughly 🙏🏻 That makes complete sense now. I really appreciate your patience and detailed feedback throughout this PR.  
  
I understand the risk of competing/conflicting documentation, and I’ll make sure to refer to the centralized sources you mentioned going forward.  
  
Also, this experience helped me learn a lot about Boost’s structure, standalone modes, and CI expectations—so thank you for the learning opportunity!  
  
If there are areas where contributions are more useful, I’d love to help out—especially around docs or beginner-friendly dev issues.  
  
Thanks again! 😊

---
