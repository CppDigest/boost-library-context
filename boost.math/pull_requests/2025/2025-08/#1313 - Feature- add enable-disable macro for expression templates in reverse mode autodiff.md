# #1313 Feature: add enable/disable macro for expression templates in reverse mode autodiff [Merged]

> Username: demroz  
> Created at: 2025-08-27 03:48:08 UTC  
> Updated at: 2025-09-10 10:02:39 UTC  
> Merged at: 2025-09-10 08:29:11 UTC  
> Closed at: 2025-09-10 08:29:11 UTC  
> Url: https://github.com/boostorg/math/pull/1313  

This feature adds a `BOOST_MATH_REVERSE_MODE_ET_ON` and `BOOST_MATH_REVERSE_MODE_ET_OFF` macro to enable/disable expression templates for the reverse mode autodiff library, and adds some documentation regarding this feature.  
  
This is also probably a good place to open a discussions about the future of the library. Adding special function support is most likely the next step, and I'd love some help and advice with that. I'm guessing `tgamma` and `cyl_bessel_j` are good places to start.  
  
The other place that I'd like to take this library is adding some optimization routines like gradient descent, SGD, Adam, LBFGS, etc...

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-08-27 04:42:20 UTC  
> Updated_at: 2025-09-10 08:29:32 UTC  
> Url: https://github.com/boostorg/math/pull/1313#issuecomment-3226716566  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1313?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 95.11%. Comparing base ([`2c943fb`](https://app.codecov.io/gh/boostorg/math/commit/2c943fbbc45ffa781f7ce4aa4f022a10a93e7c77?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`7535a6e`](https://app.codecov.io/gh/boostorg/math/commit/7535a6ef2331ffb19d2abd166814e0ff986f1b38?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 57 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1313/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1313?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1313   +/-   ##  
========================================  
  Coverage    95.10%   95.11%             
========================================  
  Files          796      797    +1       
  Lines        67115    67197   +82       
========================================  
+ Hits         63830    63912   +82       
  Misses        3285     3285             
```  
[see 11 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/math/pull/1313/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1313?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1313?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [2c943fb...7535a6e](https://app.codecov.io/gh/boostorg/math/pull/1313?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 2

> Username: demroz  
> Created_at: 2025-09-01 18:10:33 UTC  
> Url: https://github.com/boostorg/math/pull/1313#issuecomment-3243041796  

@mborland @ckormanyos is there any chance I can get this looked at? I've been working on some gradient optimizers in the meantime. Should be ready soon-ish

---

## Comment 3

> Username: mborland  
> Created_at: 2025-09-01 18:14:04 UTC  
> Url: https://github.com/boostorg/math/pull/1313#issuecomment-3243046233  

I will take a look tomorrow morning and leave comments

---

## Review 4 [Commented]

> Username: mborland  
> Created_at: 2025-09-02 07:16:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1313#pullrequestreview-3175152745  

By and large this looks like what we talked about in the last thread. Looks generally good to me.

📁 doc/differentiation/autodiff_reverse.qbk

```diff
 586 |+     g++ -DBOOST_MATH_REVERSE_MODE_ET_OFF
 587 |+ 
 588 |+ The expression templated version of this code doesn't currently interact nicely with the Boost.Math special function implementations, and will throw compile time errors. Disabling expression templates will often fix this, however various special functions are implemented in a way that breaks the automatic differentiation chain for certain values. Complete special function support may be added in the future.
```

> Username: mborland  
> Created_at: 2025-09-02 07:05:16 UTC  
> Updated_at: 2025-09-02 07:16:58 UTC  
> Url: https://github.com/boostorg/math/pull/1313#discussion_r2315120258  

I think it would be useful to have a table here that lists the special functions that expression templates supports, rather than the user having to experiment on seeing what compiles.

> Username: demroz  
> Created_at: 2025-09-02 07:47:19 UTC  
> Url: https://github.com/boostorg/math/pull/1313#discussion_r2315220173  

That's fair. Is there any way to add tests that aren't expected to compile without breaking the entire process? If not I can test locally

> Username: mborland  
> Created_at: 2025-09-02 09:34:36 UTC  
> Url: https://github.com/boostorg/math/pull/1313#discussion_r2315513352  

Yes. In addition to `run` in the Jamfile you have the options of `run-fail`, `compile`, and `compile-fail`. Example is here: https://github.com/boostorg/math/blob/develop/test/Jamfile.v2#L1629.  
  
In the build log it'll show `failed-as-expected`


📁 include/boost/math/differentiation/detail/reverse_mode_autodiff_basic_ops_et.hpp

```diff
 109 |+ 
 110 |+ /** @brief
 111 |+  *   subtraction float - rvar
```

> Username: mborland  
> Created_at: 2025-09-02 07:07:56 UTC  
> Updated_at: 2025-09-02 07:16:58 UTC  
> Url: https://github.com/boostorg/math/pull/1313#discussion_r2315125548  

Does the current testing include C++23's `<stdfloat>` types? If not I think they should be added. It has its own special rules like narrowing is a compilation error rather than a warning.

> Username: demroz  
> Created_at: 2025-09-02 09:08:26 UTC  
> Updated_at: 2025-09-02 09:09:34 UTC  
> Url: https://github.com/boostorg/math/pull/1313#discussion_r2315431909  

The `test_autodiff_reverse.hpp`  is essentially a slightly modified copy of the forward mode version. It has a macro that checks for `__STDCPP_FLOAT_*_T__`  
[here](https://github.com/boostorg/math/blob/ab265d474f2f1996610df283e71a63c7a7eb1cad/test/test_autodiff_reverse.hpp#L36). I believe this is what youre asking about, I've never really worked with C++23's `<stdfloat>` types

> Username: mborland  
> Created_at: 2025-09-02 09:36:16 UTC  
> Url: https://github.com/boostorg/math/pull/1313#discussion_r2315517789  

Yes, but you'll need to add this to the top:  
  
```  
#if __has_include(<stdfloat>)  
#  include <stdfloat>  
#endif  
```  
  
Since the macros are defined inside that header instead of globally.

> Username: demroz  
> Created_at: 2025-09-02 12:46:27 UTC  
> Url: https://github.com/boostorg/math/pull/1313#discussion_r2315980232  

added these tests

> Username: ckormanyos  
> Created_at: 2025-09-04 10:12:41 UTC  
> Updated_at: 2025-09-04 10:12:42 UTC  
> Url: https://github.com/boostorg/math/pull/1313#discussion_r2321528789  

> es, but you'll need to add this to the top:  
  
Do we still need to query `__has_include` itself?  
  
```  
#if defined(__has_include)  
#if __has_include(<stdfloat>)  
#  include <stdfloat>  
#endif  
#endif  
```

> Username: mborland  
> Created_at: 2025-09-04 10:18:36 UTC  
> Url: https://github.com/boostorg/math/pull/1313#discussion_r2321540958  

No; we don't support any compiler that lacks `__has_include`.


📁 include/boost/math/differentiation/detail/reverse_mode_autodiff_stl_et.hpp

```diff
  90 |+ {
  91 |+     BOOST_MATH_STD_USING
  92 |+     return arg * pow(static_cast<RealType>(2.0), i);
```

> Username: mborland  
> Created_at: 2025-09-02 07:12:34 UTC  
> Updated_at: 2025-09-02 07:16:58 UTC  
> Url: https://github.com/boostorg/math/pull/1313#discussion_r2315138017  

Does the case of `HUGE_VAL` need to be handled here or does the expression template value naturally handle that?

> Username: demroz  
> Created_at: 2025-09-02 12:45:52 UTC  
> Url: https://github.com/boostorg/math/pull/1313#discussion_r2315978495  

It naturally handles huge values correctly. I added an explicit test case for this though. Works as expected


📁 include/boost/math/differentiation/autodiff_reverse.hpp

```diff
  11 |+ #define BOOST_MATH_REVERSE_MODE_ET_ON
  12 |+ #endif
  13 |+ 
```

> Username: mborland  
> Created_at: 2025-09-02 07:15:52 UTC  
> Updated_at: 2025-09-02 07:16:58 UTC  
> Url: https://github.com/boostorg/math/pull/1313#discussion_r2315144652  

It might be worth adding a check here that there's no crazy configuration problems:  
  
```  
#if defined(BOOST_MATH_REVERSE_MODE_ET_OFF) && defined(BOOST_MATH_REVERSE_MODE_ET_ON)  
#  error "BOOST_MATH_REVERSE_MODE_ET_OFF and BOOST_MATH_REVERSE_MODE_ET_ON are mutually exclusive"  
#endif  
```

> Username: demroz  
> Created_at: 2025-09-02 12:48:11 UTC  
> Url: https://github.com/boostorg/math/pull/1313#discussion_r2315984959  

added

> Username: mborland  
> Created_at: 2025-09-02 15:22:59 UTC  
> Updated_at: 2025-09-02 15:23:00 UTC  
> Url: https://github.com/boostorg/math/pull/1313#discussion_r2316426950  

So this commit also changed the default behavior. Instead of if not explicitly ET_OFF then ET_ON, to if not explicitly ET_ON then ET_OFF (via fall through). Should ET_ON be the default for performance reasons? Or should ET_OFF be the default since it has broader compatibility. I would say ET_ON if we tell the user what works with it, and how to turn it off (which are both already covered in docs or previous comments)  
  
@ckormanyos and @jzmaddock in case you guys have an opinion on the matter.

> Username: demroz  
> Created_at: 2025-09-02 16:20:20 UTC  
> Updated_at: 2025-09-02 16:20:43 UTC  
> Url: https://github.com/boostorg/math/pull/1313#discussion_r2316586323  

My fault. I didn't think too hard about about your comment and just copy and pasted that piece of code. I think ET should be enabled ON by default. At least from personal experience its what makes the most sense to me. If there is no objection from @ckormanyos and @jzmaddock I'll fix it in my next commit.

> Username: ckormanyos  
> Created_at: 2025-09-04 07:15:31 UTC  
> Url: https://github.com/boostorg/math/pull/1313#discussion_r2321085776  

>  I think ET should be enabled ON by default. At least from personal experience its what makes the most sense to me.  
  
It also makes sense to me.


---

## Comment 5

> Username: demroz  
> Created_at: 2025-09-08 15:59:03 UTC  
> Updated_at: 2025-09-08 16:02:52 UTC  
> Url: https://github.com/boostorg/math/pull/1313#issuecomment-3266968851  

@mborland I autogenerated some compile tests for all the special functions and summarized them in the docs. Main thing to look at is `autogen_rvar_specfun_tests.sh` and the table in the docs, and maybe one of the added tests. Most of the code is repeated with just the function name changing. I didn't add these tests to the Jamfile. I'm not sure if I should. Let me know if thats necessary. There are just a lot of them and they took like an hour and a half on my machine to run

---

## Review 6 [Commented]

> Username: mborland  
> Created_at: 2025-09-09 06:44:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1313#pullrequestreview-3199606240  

📁 doc/differentiation/autodiff_reverse.qbk

```diff
 707 | [h2:expression-templates-and-auto Expression Templates and auto]
 575 |- Reverse mode autodiff is expression template based. This means that some care has to be taken into considerations when writing code that uses this library. For example consider the code below:
 708 |+ Reverse mode autodiff is expression template based by default. If you'd like to disable expression templates (for debugging, benchmarking, or simplify integration with other systems), you must define the following macro at compile time:
```

> Username: mborland  
> Created_at: 2025-09-09 06:38:43 UTC  
> Updated_at: 2025-09-09 06:44:08 UTC  
> Url: https://github.com/boostorg/math/pull/1313#discussion_r2332182729  

```diff  
-Reverse mode autodiff is expression template based by default. If you'd like to disable expression templates (for debugging, benchmarking, or simplify integration with other systems), you must define the following macro at compile time:  
+Reverse mode autodiff is expression template (`BOOST_MATH_REVERSE_MODE_ET_ON`) based by default. If you'd like to disable expression templates (for debugging, benchmarking, or simplify integration with other systems), you must define the following macro at compile time:  
```

> Username: demroz  
> Created_at: 2025-09-09 18:14:32 UTC  
> Url: https://github.com/boostorg/math/pull/1313#discussion_r2334403307  

added

---

📁 doc/differentiation/autodiff_reverse.qbk

```diff
 708 |+ Reverse mode autodiff is expression template based by default. If you'd like to disable expression templates (for debugging, benchmarking, or simplify integration with other systems), you must define the following macro at compile time:
 709 |+ 
 710 |+     #define BOOST_MATH_REVERSE_MODE_ET_OFF
```

> Username: mborland  
> Created_at: 2025-09-09 06:40:13 UTC  
> Updated_at: 2025-09-09 06:44:08 UTC  
> Url: https://github.com/boostorg/math/pull/1313#discussion_r2332186087  

Maybe add an include of the library after this line? The important point is this has to come first

> Username: demroz  
> Created_at: 2025-09-09 18:14:38 UTC  
> Url: https://github.com/boostorg/math/pull/1313#discussion_r2334403504  

added


📁 test/test_reverse_mode_autodiff_special_functions_compile/airy/test_reverse_mode_autodiff_airy_ai_compile_test_ET_OFF_cpp_types.cpp

```diff
   1 |+ #define BOOST_MATH_REVERSE_MODE_ET_OFF
```

> Username: mborland  
> Created_at: 2025-09-09 06:42:48 UTC  
> Updated_at: 2025-09-09 06:44:08 UTC  
> Url: https://github.com/boostorg/math/pull/1313#discussion_r2332191976  

The doc runner in CI is failing with the message that all of these auto generated files needs a license and copyright at the top. I think it would be worth adding a line at the top as well that says these are auto-generated do no manually edit them.

> Username: demroz  
> Created_at: 2025-09-09 18:14:42 UTC  
> Updated_at: 2025-09-09 18:14:43 UTC  
> Url: https://github.com/boostorg/math/pull/1313#discussion_r2334403659  

added


---

## Comment 7

> Username: mborland  
> Created_at: 2025-09-09 06:51:26 UTC  
> Updated_at: 2025-09-09 06:51:42 UTC  
> Url: https://github.com/boostorg/math/pull/1313#issuecomment-3269137614  

I actually think right now none of your stuff is cycling in the CI. I see `test-suite autodiff_reverse_specific` and `test-suite "test_reverse_mode_autodiff"`. I would consolidate these into 1 (and remove the quotes) for what you already have, and at the bottom of the file add `test_reverse_mode_autodiff` next to `autodiff` on the `github_ci_block_2` line. Then create a `test-suite autodiff-long-running-tests` with everything from this PR. We can add a few choice CI runners that only run that test-suite since we know it's lengthy. We do the same thing in Multiprecision with a test suite named long running tests.

---

## Comment 8

> Username: demroz  
> Created_at: 2025-09-10 06:34:36 UTC  
> Url: https://github.com/boostorg/math/pull/1313#issuecomment-3273503102  

@mborland so I added the fixes you asked for and fixed the Jamfile. Seems like everything is running at this point.

---

## Comment 9

> Username: mborland  
> Created_at: 2025-09-10 08:31:19 UTC  
> Url: https://github.com/boostorg/math/pull/1313#issuecomment-3273898457  

Ok. I added a commit to run the long tests on our Drone CI. I tried to push it onto your branch, but since it is named develop it accidentally all got merged into boostorg:develop at the same time.

---

## Comment 10

> Username: demroz  
> Created_at: 2025-09-10 08:47:29 UTC  
> Url: https://github.com/boostorg/math/pull/1313#issuecomment-3273958247  

Ok I'll stop submitting PRs from the develop branch to avoid this in the future.

---

## Comment 11

> Username: mborland  
> Created_at: 2025-09-10 09:04:37 UTC  
> Url: https://github.com/boostorg/math/pull/1313#issuecomment-3274019589  

> Ok I'll stop submitting PRs from the develop branch to avoid this in the future.  
  
Thanks. Definitely a case of operator error though. Looks like you have tight tolerances that may be prone to random failures: https://github.com/boostorg/math/actions/runs/17607913028/job/50022812424#step:17:877

---

## Comment 12

> Username: demroz  
> Created_at: 2025-09-10 09:13:46 UTC  
> Url: https://github.com/boostorg/math/pull/1313#issuecomment-3274054472  

> > Ok I'll stop submitting PRs from the develop branch to avoid this in the future.  
>   
> Thanks. Definitely a case of operator error though. Looks like you have tight tolerances that may be prone to random failures: https://github.com/boostorg/math/actions/runs/17607913028/job/50022812424#step:17:877  
  
that's unfortunate. I've been fighting with these for a while now and thought I got them all at this point. I'll probably switch everything to relative errors and set something like 0.01 as tolerance.

---

## Comment 13

> Username: mborland  
> Created_at: 2025-09-10 09:45:26 UTC  
> Url: https://github.com/boostorg/math/pull/1313#issuecomment-3274177985  

The `long-running` tests are cycling in under an hour which is good, but they're showing a number of failures: https://drone.cpp.al/boostorg/math/2396/669/2

---

## Comment 14

> Username: demroz  
> Created_at: 2025-09-10 10:02:39 UTC  
> Url: https://github.com/boostorg/math/pull/1313#issuecomment-3274233532  

> The `long-running` tests are cycling in under an hour which is good, but they're showing a number of failures: https://drone.cpp.al/boostorg/math/2396/669/2  
  
looks like some of the function arguments didn't get parsed properly. I'll fix this evening

---
