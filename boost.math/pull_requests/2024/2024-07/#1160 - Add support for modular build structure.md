# #1160 Add support for modular build structure. [Merged]

> Username: grafikrobot  
> Created at: 2024-07-20 22:23:00 UTC  
> Updated at: 2024-09-06 12:46:14 UTC  
> Merged at: 2024-09-06 12:45:23 UTC  
> Closed at: 2024-09-06 12:45:23 UTC  
> Url: https://github.com/boostorg/math/pull/1160  

This is part of the effort to make the Boost libraries "modular" for build and consumption. See https://lists.boost.org/Archives/boost/2024/01/255704.php and https://github.com/grafikrobot/boost-b2-modular/blob/b2-modular/README.adoc for more information.  
  
This PR depends on the following other PRs being merged to both develop and master branches of the respective repos:  
  
- https://github.com/boostorg/boost/pull/854  
  
This PR will be changed to ready for review, i.e. not draft, when the above are merged. Do not merge this one until that time.

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2024-08-18 15:52:19 UTC  
> Url: https://github.com/boostorg/math/pull/1160#issuecomment-2295308264  

Please review and merge this PR at your earliest convenience.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2024-08-19 10:51:56 UTC  
> Url: https://github.com/boostorg/math/pull/1160#issuecomment-2296283453  

@grafikrobot There's a lot of failures here, for example:  
  
```  
gcc.compile.c++ ../../../bin.v2/libs/math/test/test_zeta_floatmax_t.test/gcc-14/debug/x86_64/link-static/threading-multi/visibility-hidden/float128/test_zeta.o  
In file included from float128/test_zeta.cpp:6:  
float128/setup.hpp:19:62: error: static assertion failed: These tests should only be run for 128-bit floating point types.  
   19 | static_assert(std::numeric_limits<boost::floatmax_t>::digits == 113, "These tests should only be run for 128-bit floating point types.");  
      |               ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~  
float128/setup.hpp:19:62: note: the comparison reduces to ‘(64 == 113)’  
```  
  
That test, and all the float128 tests are predicated on:  
  
```  
            [ check-target-builds ../config//has_128bit_floatmax_t "128-bit floatmax_t" : : <build>no ]  
```  
  
And that configure step is showing "no" and so should NOT be building?

---

## Comment 3

> Username: grafikrobot  
> Created_at: 2024-08-19 14:08:14 UTC  
> Url: https://github.com/boostorg/math/pull/1160#issuecomment-2296672159  

Hm.. Strange.. Will investigate.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2024-08-30 17:08:26 UTC  
> Url: https://github.com/boostorg/math/pull/1160#issuecomment-2321999271  

@grafikrobot thanks for the fixes, however there are still configuration related failures, for example if we stick to this job: https://drone.cpp.al/boostorg/math/2055/66/2 then the first failure is a linker error for test_2F0 - it's looking for libquadmath which isn't present.  The Jamfile for that target has:  
  
```  
[ check-target-builds ../config//has_float128 "GCC libquadmath and __float128 support" : <define>BOOST_MATH_TEST_FLOAT128 <linkflags>"-Bstatic -lquadmath -Bdynamic" ]  
```  
  
and the configuration output says:  
  
```  
   - GCC libquadmath and __float128 support : no [3]  
```  
  
But it's still trying to link against it.  
  
There are other configuration failings as well, that's just the first one in the log.  
  
Thanks!

---

## Comment 5

> Username: grafikrobot  
> Created_at: 2024-08-31 05:10:12 UTC  
> Url: https://github.com/boostorg/math/pull/1160#issuecomment-2322775687  

@jzmaddock I think I failed to understand how you are doing those checks. And actually I'm still failing. :-) What I tried to do was to normalize/abstract the various float18 config checks into one variable for reuse. But I didn't notice you use the checks differently in various places. So let me ask this.. In one place you use:  
  
```  
[ check-target-builds ../config//has_intel_quad "Intel _Quad datatype support" : <cxxflags>-Qoption,cpp,--extended_float_type <define>BOOST_MATH_USE_FLOAT128 ]  
```  
  
And in another you use:  
  
```  
[ check-target-builds ../config//has_intel_quad "Intel _Quad datatype support" : <cxxflags>-Qoption,cpp,--extended_float_type ]  
```  
  
Note the missing `<define>BOOST_MATH_USE_FLOAT128`. And the other checks `has_float18` and `has_128bit_floatmax_t` also differ in various uses. My questions, to try and fix my attempt:  
  
1. Are the individual uses of each check meant to the same everywhere?  
2. Should all three of the checks be used together as is done in `rule get_float128_tests`?

---

## Comment 6

> Username: grafikrobot  
> Created_at: 2024-08-31 20:26:08 UTC  
> Url: https://github.com/boostorg/math/pull/1160#issuecomment-2323035374  

@jzmaddock AFAICT the issues with quadmath are now resolved. But it would be great to get an answer on the consistency question of the trio of float128 checks. Or rather, on the lack of consistency in the many places those are copy+pasted which I fixed?

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2024-09-01 07:54:25 UTC  
> Url: https://github.com/boostorg/math/pull/1160#issuecomment-2323218326  

Apologies Rene, no they are not consistent, and no they are not really meant to be, each test has it's own set of requirements, some of them are "if this is available link to it because a dependency needs it", some are "if this is available enable testing" and so on.  
  
Plus it's not just the float128 configurations that are failing: I highlighted that because it was the first and most numerous thing to show up, but there are others as well as I think.

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2024-09-01 07:56:30 UTC  
> Url: https://github.com/boostorg/math/pull/1160#issuecomment-2323218987  

> Plus it's not just the float128 configurations that are failing: I highlighted that because it was the first and most numerous thing to show up, but there are others as well as I think.  
  
Or perhaps not as this appears to be green now.  
  
In principle though, shouldn't each conditional usage be able to define it's own set of requirements depending on what that target needs?

---

## Comment 9

> Username: grafikrobot  
> Created_at: 2024-09-01 14:12:29 UTC  
> Updated_at: 2024-09-01 14:13:21 UTC  
> Url: https://github.com/boostorg/math/pull/1160#issuecomment-2323357682  

> > Plus it's not just the float128 configurations that are failing: I highlighted that because it was the first and most numerous thing to show up, but there are others as well as I think.  
>   
> Or perhaps not as this appears to be green now.  
>   
> In principle though, shouldn't each conditional usage be able to define it's own set of requirements depending on what that target needs?  
  
In principle you can do whatever you like depending on what you need and intended. And it's precisely your INTENT that I'm asking about because I definitely do not know what math testing needs to do. I'm just guessing to what I think you might have intended. So to reformulate the question.. Here are the variations of the use of the `has_float128`, `has_intel_quad`, and `has_128bit_floatmax_t` appearing in the current Jamfile with counts of appearance:  
  
```  
55: [ check-target-builds ../config//has_float128 "GCC libquadmath and __float128 support" : <define>BOOST_MATH_TEST_FLOAT128 <linkflags>"-Bstatic -lquadmath -Bdynamic" ]  
01: [ check-target-builds ../config//has_float128 "GCC libquadmath and __float128 support" : <linkflags>"-Bstatic -lquadmath -Bdynamic" : <build>no ]  
13: [ check-target-builds ../config//has_float128 "GCC libquadmath and __float128 support" : <linkflags>"-Bstatic -lquadmath -Bdynamic" ]  
02: [ check-target-builds ../config//has_float128 "__float128" : : <build>no ]  
75: [ check-target-builds ../config//has_float128 "GCC libquadmath and __float128 support" : <linkflags>-lquadmath ]  
  
01: [ check-target-builds ../config//has_intel_quad "Intel _Quad datatype support" : <cxxflags>-Qoption,cpp,--extended_float_type <define>BOOST_MATH_USE_FLOAT128 ]  
01: [ check-target-builds ../config//has_intel_quad "Intel _Quad datatype support" : <cxxflags>-Qoption,cpp,--extended_float_type ]  
  
01: [ check-target-builds ../config//has_128bit_floatmax_t "128-bit floatmax_t" : : <build>no ]  
```  
  
Question is.. Did you intend to make all those different? Or should they be uniform and it's a historical mistake that they differ?  
  
Do note that I only rewrote the cases of ` [ check-target-builds ../config//has_float128 "GCC libquadmath and __float128 support" : <define>BOOST_MATH_TEST_FLOAT128 <linkflags>"-Bstatic -lquadmath -Bdynamic" ]`. The rest stayed the same.  
  
Also, generally, the reasons to specify what those checks are in a simple place near the top of the jamfile are:  
* To avoid possible copy+paste+edit errors over time.  
* To minimize Jamfile processing time, as each `[ check-target-builds .. ]` instance does a bunch of work. There's no optimization of repeated invocations in the interpreted world of B2 jamfiles.  
  
Further note.. Those are not the only copy+pasted `check-target-builds` cases. Things could be further de-duplicated for the rest.  
  
HTH

---

## Comment 10

> Username: mborland  
> Created_at: 2024-09-03 13:35:53 UTC  
> Url: https://github.com/boostorg/math/pull/1160#issuecomment-2326548405  

> Question is.. Did you intend to make all those different? Or should they be uniform and it's a historical mistake that they differ?  
>   
  
`has_float128` and `has_intel_quad` are separate because they represent two different types that are platform specific. `has_128bit_floatmax_t` would be if either of the previous are defined. Can `b2` differentiate between intel compiler being `icpc` or `icpx`? `icpx` is the new clang based intel compiler, and it does not support all the compiler flags that `icpc` uses.

---

## Comment 11

> Username: codecov[bot]  
> Created_at: 2024-09-04 02:15:05 UTC  
> Updated_at: 2024-09-06 12:46:14 UTC  
> Url: https://github.com/boostorg/math/pull/1160#issuecomment-2327773369  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1160?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.77%. Comparing base [(`ab57b20`)](https://app.codecov.io/gh/boostorg/math/commit/ab57b2093efe2de49dac9849065f01b290793be4?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`56e82c0`)](https://app.codecov.io/gh/boostorg/math/commit/56e82c0bb62d78959e47ed98a3d716257c8c2202?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1160/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1160?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1160      +/-   ##  
===========================================  
- Coverage    94.09%   93.77%   -0.32%       
===========================================  
  Files          780      654     -126       
  Lines        65851    53718   -12133       
===========================================  
- Hits         61960    50375   -11585       
+ Misses        3891     3343     -548       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1160?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [test/test\_bernoulli\_constants.cpp](https://app.codecov.io/gh/boostorg/math/pull/1160?src=pr&el=tree&filepath=test%2Ftest_bernoulli_constants.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2Jlcm5vdWxsaV9jb25zdGFudHMuY3Bw) | `83.72% <ø> (ø)` | |  
  
... and [180 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/math/pull/1160/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1160?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1160?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [ab57b20...56e82c0](https://app.codecov.io/gh/boostorg/math/pull/1160?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 12

> Username: mborland  
> Created_at: 2024-09-04 13:53:05 UTC  
> Url: https://github.com/boostorg/math/pull/1160#issuecomment-2329134181  

This is effectively green now with only Codecov failing. Based on the massive diff stat shown in the codecov comment it looks like something went wrong parsing an upload since all of those runs are green. Anything else @jzmaddock before I squash and merge this?

---
