# #1222 Fix clang warning when compiling with -ffast-math: use of infinity is undefined behavior due to the currently enabled floating-point options [Merged]

> Username: pps83  
> Created at: 2024-11-28 13:41:14 UTC  
> Updated at: 2024-12-17 11:33:17 UTC  
> Merged at: 2024-12-16 21:18:07 UTC  
> Closed at: 2024-12-16 21:18:07 UTC  
> Url: https://github.com/boostorg/math/pull/1222  

Fixes the following warning:  
```  
C:\boost_1_86_0\boost/math/ccmath/isinf.hpp(29,69): warning : use of infinity is undefined behavior due to the currently enabled floating-point options [-Wnan-infinity-disabled]  
C:\boost_1_86_0\boost/math/ccmath/isinf.hpp(29,25): warning : use of infinity is undefined behavior due to the currently enabled floating-point options [-Wnan-infinity-disabled]  
C:\boost_1_86_0\boost/math/ccmath/ldexp.hpp(50,38): note: in instantiation of function template specialization 'boost::math::ccmath::isinf<float>' requested here  
C:\boost_1_86_0\boost/math/ccmath/ldexp.hpp(69,33): note: in instantiation of function template specialization 'boost::math::ccmath::ldexp<float, true>' requested here  
```

---

## Comment 1

> Username: pps83  
> Created_at: 2024-11-28 13:42:13 UTC  
> Url: https://github.com/boostorg/math/pull/1222#issuecomment-2506158139  

the question is: should this be suppressed or not? with `-fast-math` I get tons of warnings from this code.

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2024-11-28 14:20:21 UTC  
> Updated_at: 2024-12-16 21:28:00 UTC  
> Url: https://github.com/boostorg/math/pull/1222#issuecomment-2506234664  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1222?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.83%. Comparing base [(`8f843fd`)](https://app.codecov.io/gh/boostorg/math/commit/8f843fd13181a12cd5d1072b7024771ac63e230e?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`289e181`)](https://app.codecov.io/gh/boostorg/math/commit/289e1813d87a5127956a44958c220c21ec5c2411?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 6 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1222/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1222?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1222   +/-   ##  
========================================  
  Coverage    93.83%   93.83%             
========================================  
  Files          657      657             
  Lines        55241    55241             
========================================  
  Hits         51836    51836             
  Misses        3405     3405             
```  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1222?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1222?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [8f843fd...289e181](https://app.codecov.io/gh/boostorg/math/pull/1222?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2024-11-28 18:27:41 UTC  
> Url: https://github.com/boostorg/math/pull/1222#issuecomment-2506626393  

> the question is: should this be suppressed or not? with -fast-math I get tons of warnings from this code.  
  
As long as -ffast-math doesn't break the code, then yes it should be suppressed I would say.

---

## Comment 4

> Username: pps83  
> Created_at: 2024-12-13 20:57:14 UTC  
> Url: https://github.com/boostorg/math/pull/1222#issuecomment-2542353520  

@mborland can you please check the PR?

---

## Comment 5

> Username: ckormanyos  
> Created_at: 2024-12-17 06:32:33 UTC  
> Updated_at: 2024-12-17 06:35:33 UTC  
> Url: https://github.com/boostorg/math/pull/1222#issuecomment-2547609467  

Hi folks, this thing just broke my CI in an unrelated project, in which I have `-Werror` activated. I think or maybe it seems like It seems that some `__clang_major` versions are caught by the test on greater than `6`.  
  
See also this [broken CI](https://github.com/ckormanyos/wide-decimal/actions/runs/12365013148/job/34509258897#step:5:33).  
  
I haven't quite figured out the exact way to weed through all the compilers yet, but I think a higher version check _might_ be needed. But I also got red on version `15`.  
  
Oh and if you tell me I should just activate `-ffast-math` I'm fine with that. i don't know if I have it or not on those runs.  
  
Cc: @jzmaddock and @mborland

---

## Comment 6

> Username: mborland  
> Created_at: 2024-12-17 11:33:16 UTC  
> Url: https://github.com/boostorg/math/pull/1222#issuecomment-2548218420  

I'll fix it. The flag was added in Clang-18 so it's relatively new

---
