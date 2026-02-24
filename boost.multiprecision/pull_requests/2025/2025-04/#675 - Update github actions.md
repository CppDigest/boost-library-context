# #675 Update github actions [Merged]

> Username: mborland  
> Created at: 2025-04-08 14:28:55 UTC  
> Updated at: 2025-04-10 14:30:25 UTC  
> Merged at: 2025-04-10 14:29:27 UTC  
> Closed at: 2025-04-10 14:29:27 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/675  

We got hit with the brownout. I don't see a good way to go wholesale with boost.ci since we have so many test sets we need to cut down. I'll update drone next since the most recent clang on there is 10 and version 20 is on the streets.  
  
CC: @ckormanyos

---

## Comment 1

> Username: mborland  
> Created_at: 2025-04-08 15:57:48 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/675#issuecomment-2786925543  

Looks like everything is running smoothly again, but we've also uncovered some new errors with recent toolchains.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2025-04-08 16:15:22 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/675#issuecomment-2786982572  

Thanks for this guys!!  
  
Looks like the new failures are all in example/gauss_laguerre_quadrature.cpp which I think was @ckormanyos ?

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2025-04-08 16:26:31 UTC  
> Updated_at: 2025-04-10 14:30:25 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/675#issuecomment-2787015309  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/675?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.1%. Comparing base [(`02d8862`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/02d8862203d0cd95b81c51d0dd30cdffad1965c9?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`0c41cab`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/0c41cabaf7ed87d953356cbbd90cd306a839ba21?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 9 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/675/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/675?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@            Coverage Diff            @@  
##           develop    #675     +/-   ##  
=========================================  
- Coverage     94.1%   94.1%   -0.0%       
=========================================  
  Files          279     279               
  Lines        28982   28983      +1       
=========================================  
- Hits         27258   27257      -1       
- Misses        1724    1726      +2       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/multiprecision/pull/675?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [example/gauss\_laguerre\_quadrature.cpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/675?src=pr&el=tree&filepath=example%2Fgauss_laguerre_quadrature.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-ZXhhbXBsZS9nYXVzc19sYWd1ZXJyZV9xdWFkcmF0dXJlLmNwcA==) | `90.7% <100.0%> (+0.1%)` | :arrow_up: |  
| [test/git\_issue\_509.cpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/675?src=pr&el=tree&filepath=test%2Fgit_issue_509.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9naXRfaXNzdWVfNTA5LmNwcA==) | `100.0% <ø> (ø)` | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/multiprecision/pull/675/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/675?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/675?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [02d8862...0c41cab](https://app.codecov.io/gh/boostorg/multiprecision/pull/675?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 4

> Username: mborland  
> Created_at: 2025-04-08 16:32:41 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/675#issuecomment-2787032307  

MSVC 14.3 with C++20 is failing on `git_issue_509.cpp(15): error C2039: 'bit_cast': is not a member of 'std'`  
  
And the standalone test are failing with this:  
  
```  
standalone_test_convert_from_tom_int.cpp: In instantiation of ‘void test_convert_to_builtin() [with From = boost::multiprecision::number<boost::multiprecision::backends::tommath_int>; To = float]’:  
standalone_test_convert_from_tom_int.cpp:199:43:   required from here  
standalone_test_convert_from_tom_int.cpp:182:15: error: cannot convert ‘boost::multiprecision::number<boost::multiprecision::backends::tommath_int>’ to ‘float’ in initialization  
  182 |       To t1 = from;  
      |               ^~~~  
      |               |  
      |               boost::multiprecision::number<boost::multiprecision::backends::tommath_int>  
  
    "g++-13"   -fvisibility-inlines-hidden -std=c++14 -fPIC -m64 -pthread -O0 -fno-inline -Wall -g -fvisibility=hidden -Wall -Wextra -DBOOST_ALL_NO_LIB=1 -DCI_SUPPRESS_KNOWN_ISSUES -DHAS_GMP -DHAS_MPFR -DHAS_TOMMATH   -I"../../.." -I"../include"  -c -o "../../../bin.v2/libs/multiprecision/test/standalone_test_convert_from_tom_int.test/gcc-13/debug/x86_64/threading-multi/visibility-hidden/standalone_test_convert_from_tom_int.o" "standalone_test_convert_from_tom_int.cpp"  
```

---

## Comment 5

> Username: ckormanyos  
> Created_at: 2025-04-08 16:34:03 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/675#issuecomment-2787036002  

> Looks like the new failures are all in example/gauss_laguerre_quadrature.cpp which I think was @ckormanyos ?  
  
Yeah, that's the guy. I just pushed a presumed fix.  
  
But @jzmaddock I think there might be another conversion failure lurking around, but my glance was super quick.  
  
Let's see if I dialed the quadrature first then if that's green if there is anything else.  
  
Cc: @mborland

---

## Comment 6

> Username: mborland  
> Created_at: 2025-04-08 16:37:48 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/675#issuecomment-2787048151  

> But @jzmaddock I think there might be another conversion failure lurking around, but my glance was super quick.  
>   
  
Yep. Conversion in standalone is above. I just pushed the fix for the MSVC 14.3 issue

---

## Comment 7

> Username: ckormanyos  
> Created_at: 2025-04-08 17:03:30 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/675#issuecomment-2787117890  

Hi @mborland and @jzmaddock this is a good one and it is nice to move forward.  
  
It looks like there still might be some kind of a bogey in `standalone_test_convert_from_tom_int` on some compilers. I have not isolated it yet, but I'm not sure if I'm the right guy for that phenomenon.

---

## Comment 8

> Username: mborland  
> Created_at: 2025-04-08 17:32:49 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/675#issuecomment-2787187815  

It looks like the updated ubuntu version brings a new version of Tommath, which had not been updated since 2019. Maybe some of the deprecated things are now gone? Might take a minute to peel this one apart

---

## Comment 9

> Username: jzmaddock  
> Created_at: 2025-04-08 17:39:08 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/675#issuecomment-2787202363  

It's a strange one for sure.... but the error is in the front end so the tommath version should *probably* not matter?

---

## Comment 10

> Username: jzmaddock  
> Created_at: 2025-04-08 17:43:56 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/675#issuecomment-2787212662  

The error can be reproduced with msvc both with and without the #define BOOST_MP_STANDALONE.  I have a hunch this test wasn't being run before?

---

## Comment 11

> Username: jzmaddock  
> Created_at: 2025-04-08 17:45:57 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/675#issuecomment-2787217752  

I have a fix: there was a copy-and-paste error when the file was created, will push a fix shortly...

---

## Comment 12

> Username: mborland  
> Created_at: 2025-04-08 17:47:42 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/675#issuecomment-2787222339  

Thanks @jzmaddock! I guess similar updates should be applied against the math CI system too. I'll open a PR.

---

## Comment 13

> Username: mborland  
> Created_at: 2025-04-10 14:29:23 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/675#issuecomment-2793774421  

CI is back to green now. Thanks for the help!

---
