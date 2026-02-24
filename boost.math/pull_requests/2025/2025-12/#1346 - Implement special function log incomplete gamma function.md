# #1346 Implement special function log incomplete gamma function [Merged]

> Username: JacobHass8  
> Created at: 2025-12-29 01:03:58 UTC  
> Updated at: 2026-01-13 02:08:55 UTC  
> Merged at: 2026-01-09 12:55:44 UTC  
> Closed at: 2026-01-09 12:55:44 UTC  
> Url: https://github.com/boostorg/math/pull/1346  

Implementation of log incomplete gamma function using asymptotic approximations where the incomplete gamma function underflows. See #1173 and #1338.

---

## Comment 1

> Username: JacobHass8  
> Created_at: 2025-12-29 01:06:05 UTC  
> Url: https://github.com/boostorg/math/pull/1346#issuecomment-3695207793  

@jzmaddock I've tried to add error checking and the promotion boilerplate based on your comment https://github.com/boostorg/math/pull/1338#issuecomment-3655757703. Does this look okay for a start?

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2025-12-30 18:51:56 UTC  
> Url: https://github.com/boostorg/math/pull/1346#issuecomment-3700177535  

Thanks @JacobHass8 that looks good to me.  BTW the separation between lgamma_incomplete_imp_final and lgamma_incomplete_imp shouldn't be needed in this case: that was a hack Matt introduced for some functions to workaround the lack of recursion support in some GPU contexts, but there's no recursion here, so we should be good :)  
  
Some tests and docs and hopefully this should be good to go!  Thanks for this.

---

## Comment 3

> Username: JacobHass8  
> Created_at: 2025-12-31 20:57:45 UTC  
> Updated_at: 2025-12-31 22:33:26 UTC  
> Url: https://github.com/boostorg/math/pull/1346#issuecomment-3702889227  

> Some tests and docs and hopefully this should be good to go! Thanks for this.  
  
What file should I put the tests in, `math/tests/test_igamma.hpp`? Are the spot checks I've implemented so far sufficient?

---

## Review 4 [Commented]

> Username: JacobHass8  
> Created_at: 2025-12-31 22:32:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1346#pullrequestreview-3621486504  

📁 test/test_igamma.hpp

```diff
 262 |+    BOOST_CHECK_CLOSE(::boost::math::lgamma_q(static_cast<T>(5), static_cast<T>(100)), static_cast<T>(log(1.6139305336977304790405739225035685228527400976549e-37L)), tolerance);
 263 |+    BOOST_CHECK_CLOSE(::boost::math::lgamma_q(static_cast<T>(22.5), static_cast<T>(2000)), static_cast<T>(-1883.4897732037716195918619632721L), tolerance * 10); // calculated via mpmath
 264 |+    BOOST_CHECK_CLOSE(::boost::math::lgamma_q(static_cast<T>(501.2), static_cast<T>(2000)), static_cast<T>(-810.31461624182202285737730562687L), tolerance * 10); // calculated via mpmath
```

> Username: JacobHass8  
> Created_at: 2025-12-31 22:32:58 UTC  
> Url: https://github.com/boostorg/math/pull/1346#discussion_r2655943304  

I used one test case that was previously checked for `gamma_q`. I also added two more examples that I calculated using `mpmath` in python which supports arbitrary precision. Are there any other tests that I should implement?


---

## Comment 5

> Username: jzmaddock  
> Created_at: 2026-01-01 10:54:46 UTC  
> Url: https://github.com/boostorg/math/pull/1346#issuecomment-3703538940  

drone tests were failing for 128-bit long double platforms, I've updated the test data with values from wolframalpha in case the data was truncated to double precision somewhere along the way.  
  
Also not sure why no github actions are being run - anyone any ideas?  @mborland ?  
  
With regard to further tests, the area most likely to fail, would be where the implementation first switches to the log asymptotic expansion, and at higher precision (we should support 128-bit long doubles at least, I'm less concerned about full arbitrary precision just yet).

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2026-01-01 10:55:19 UTC  
> Url: https://github.com/boostorg/math/pull/1346#issuecomment-3703539325  

Ah... I needed to approve the github run: now done!

---

## Comment 7

> Username: codecov[bot]  
> Created_at: 2026-01-01 12:31:11 UTC  
> Updated_at: 2026-01-09 12:57:04 UTC  
> Url: https://github.com/boostorg/math/pull/1346#issuecomment-3703648651  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1346?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 95.29%. Comparing base ([`f6be8e8`](https://app.codecov.io/gh/boostorg/math/commit/f6be8e863a2c233f6877bc12fd27ae3c445b4b20?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`2a6fb33`](https://app.codecov.io/gh/boostorg/math/commit/2a6fb336dcc874bb1264b7d055ed043fb889568e?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 26 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1346/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1346?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1346   +/-   ##  
========================================  
  Coverage    95.28%   95.29%             
========================================  
  Files          814      814             
  Lines        67364    67422   +58       
========================================  
+ Hits         64191    64249   +58       
  Misses        3173     3173             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1346?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/math/special\_functions/gamma.hpp](https://app.codecov.io/gh/boostorg/math/pull/1346?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fgamma.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2dhbW1hLmhwcA==) | `99.85% <100.00%> (+<0.01%)` | :arrow_up: |  
| [test/compile\_test/instantiate.hpp](https://app.codecov.io/gh/boostorg/math/pull/1346?src=pr&el=tree&filepath=test%2Fcompile_test%2Finstantiate.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9jb21waWxlX3Rlc3QvaW5zdGFudGlhdGUuaHBw) | `100.00% <100.00%> (ø)` | |  
| [test/test\_igamma.cpp](https://app.codecov.io/gh/boostorg/math/pull/1346?src=pr&el=tree&filepath=test%2Ftest_igamma.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2lnYW1tYS5jcHA=) | `98.07% <100.00%> (ø)` | |  
| [test/test\_igamma.hpp](https://app.codecov.io/gh/boostorg/math/pull/1346?src=pr&el=tree&filepath=test%2Ftest_igamma.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2lnYW1tYS5ocHA=) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1346?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1346?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [f6be8e8...2a6fb33](https://app.codecov.io/gh/boostorg/math/pull/1346?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 8

> Username: JacobHass8  
> Created_at: 2026-01-01 19:29:36 UTC  
> Url: https://github.com/boostorg/math/pull/1346#issuecomment-3704031791  

> With regard to further tests, the area most likely to fail, would be where the implementation first switches to the log asymptotic expansion, and at higher precision (we should support 128-bit long doubles at least, I'm less concerned about full arbitrary precision just yet).  
  
I've added some documentation but don't know how to generate the html. I tried running b2 in the doc folder but received an error from a different file.   
  
I also added a test for $x=999$ which is right below when the asymptotic expansion is used. Is there anything else that needs to be done before this can be merged?

---

## Comment 9

> Username: JacobHass8  
> Created_at: 2026-01-04 00:21:18 UTC  
> Url: https://github.com/boostorg/math/pull/1346#issuecomment-3707474443  

@jzmaddock I started looking into implementing the log of the beta function. I'm not sure if this even needs to be implemented though because the precision is already very good as in this example  
```C++  
#include <iostream>  
#include <boost/math/special_functions/beta.hpp>  
  
int main(int argc, char* argv[])  
{  
    std::cout << log(boost::math::ibeta(15., 10., 1e-20)) << std::endl; // -676.692  
    return 0;  
}  
```  
I'm only seeing precision issues if a or b gets large. I'm not sure if that's a regime that people are interested in though.   
  
If it's decided to be implemented, how could I go about that? It doesn't appear that there's a simple function that could be used to approximate the tail as in the log gamma case. I only found `boost::math::beta_small_b_large_a_series` which is already used to calculate `boost::math::ibeta`.

---

## Comment 10

> Username: jzmaddock  
> Created_at: 2026-01-04 12:40:30 UTC  
> Url: https://github.com/boostorg/math/pull/1346#issuecomment-3708053036  

Re incomplete beta, I don't see anything particularly easy, unless maybe you're so far out in the extreme that https://dlmf.nist.gov/8.18#E14 can be applied with the sum part neglected?

---

## Comment 11

> Username: dschmitz89  
> Created_at: 2026-01-05 09:59:26 UTC  
> Url: https://github.com/boostorg/math/pull/1346#issuecomment-3709738444  

@JacobHass8 This looks great! Could you check the test cases requested by the user in the Scipy issue https://github.com/scipy/scipy/issues/8424 ? The OP mentioned underflow and overflow issues, in this conversation I only see underflow.

---

## Comment 12

> Username: JacobHass8  
> Created_at: 2026-01-05 18:33:17 UTC  
> Url: https://github.com/boostorg/math/pull/1346#issuecomment-3711630421  

> @JacobHass8 This looks great! Could you check the test cases requested by the user in the Scipy issue [scipy/scipy#8424](https://github.com/scipy/scipy/issues/8424) ? The OP mentioned underflow and overflow issues, in this conversation I only see underflow.  
  
I've added the following test cases which should address the issue:  
```C++  
   BOOST_CHECK_CLOSE(::boost::math::lgamma_q(static_cast<T>(500), static_cast<T>(10)), static_cast<T>(-3.79666711621207197039397438773960431648625558027046365463e-639L), tolerance * 3);  
   BOOST_CHECK_CLOSE(::boost::math::lgamma_q(static_cast<T>(5), static_cast<T>(1000)), static_cast<T>(-975.5430287171020511929200293377669175923128826278957569928895945L), tolerance);  
```  
Note that the first test, when $k=500$ and $\lambda=10$ underflows unless long double precision is used. The log of the CDF for the Poisson distribution there is of order $10^{-639}$ so underflow makes sense.

---

## Comment 13

> Username: mborland  
> Created_at: 2026-01-05 19:07:04 UTC  
> Url: https://github.com/boostorg/math/pull/1346#issuecomment-3711733756  

@JacobHass8 I added the CUDA markers and testing. Everything runs without changes needed. It does looks like you have a tolerance issue on CI machines with 128-bit long doubles (s390x and ARM64):  
  
```  
====== BEGIN OUTPUT ======  
  
Running 1 test case...  
  
Tests run with GNU C++ version 11.2.0, GNU libstdc++ version 20220324, linux  
  
Testing spot values with type float  
  
Testing spot values with type double  
  
Testing spot values with type long double  
  
test_igamma.hpp(283): error: in "test_main": difference{7.89562e-31} between ::boost::math::lgamma_q(static_cast<T>(1200), static_cast<T>(1250.25)){-2.59193486211758620551930971222062819} and static_cast<T>(-2.591934862117586205519309712218581885256650074210410262843591453L){-2.59193486211758620551930971221858169} exceeds 4.81482e-29%  
  
Testing spot values with type real_concept  
  
test_igamma.hpp(283): error: in "test_main": difference{4.8521e-31} between ::boost::math::lgamma_q(static_cast<T>(1200), static_cast<T>(1250.25)){-2.59193} and static_cast<T>(-2.591934862117586205519309712218581885256650074210410262843591453L){-2.59193} exceeds 4.81482e-29%  
  
Testing tgamma(a, z) medium values with type float  
```

---

## Comment 14

> Username: JacobHass8  
> Created_at: 2026-01-05 20:22:42 UTC  
> Url: https://github.com/boostorg/math/pull/1346#issuecomment-3711962844  

> @JacobHass8 I added the CUDA markers and testing. Everything runs without changes needed. It does looks like you have a tolerance issue on CI machines with 128-bit long doubles (s390x and ARM64):  
  
Would simply increasing the tolerances in this case be acceptable? This is for very large arguments and the difference seems quite small.   
  
In the future, how can I test with s390x and ARM64 CI machines?

---

## Comment 15

> Username: mborland  
> Created_at: 2026-01-05 21:00:12 UTC  
> Url: https://github.com/boostorg/math/pull/1346#issuecomment-3712068201  

> > @JacobHass8 I added the CUDA markers and testing. Everything runs without changes needed. It does looks like you have a tolerance issue on CI machines with 128-bit long doubles (s390x and ARM64):  
>   
> Would simply increasing the tolerances in this case be acceptable? This is for very large arguments and the difference seems quite small.  
>   
  
I think that would be fine.  
  
> In the future, how can I test with s390x and ARM64 CI machines?  
  
They are automatically included in the check titled "continuous-integration/drone/pr". You can click on that runner's hyperlink, and it will show you the several hundred configurations that Drone runs for us.

---

## Comment 16

> Username: JacobHass8  
> Created_at: 2026-01-06 18:48:57 UTC  
> Url: https://github.com/boostorg/math/pull/1346#issuecomment-3715889553  

> They are automatically included in the check titled "continuous-integration/drone/pr". You can click on that runner's hyperlink, and it will show you the several hundred configurations that Drone runs for us.  
  
Oh, I see where that is now. Thanks! Is there a way to see if these tests will pass with my revisions before pushing?

---

## Comment 17

> Username: mborland  
> Created_at: 2026-01-06 19:04:55 UTC  
> Url: https://github.com/boostorg/math/pull/1346#issuecomment-3715940381  

> > They are automatically included in the check titled "continuous-integration/drone/pr". You can click on that runner's hyperlink, and it will show you the several hundred configurations that Drone runs for us.  
>   
> Oh, I see where that is now. Thanks! Is there a way to see if these tests will pass with my revisions before pushing?  
  
I use multi-arch docker containers integrated into CLion to test platforms like these I don't have. Outside of that generally no, you'll have to wait and see.

---

## Comment 18

> Username: JacobHass8  
> Created_at: 2026-01-06 20:43:36 UTC  
> Url: https://github.com/boostorg/math/pull/1346#issuecomment-3716267248  

> I use multi-arch docker containers integrated into CLion to test platforms like these I don't have. Outside of that generally no, you'll have to wait and see.  
   
I had been trying to use various boost multiprecision types but to no success. I'm going to try to set up a docker container. Thanks for the help!

---

## Comment 19

> Username: ckormanyos  
> Created_at: 2026-01-06 20:50:07 UTC  
> Updated_at: 2026-01-06 20:50:49 UTC  
> Url: https://github.com/boostorg/math/pull/1346#issuecomment-3716292112  

> I had been trying to use various boost multiprecision types but to no success.  
  
Most (if not all) of the special functions in Math run with Multiprecision types. Sometimes these need quite specialized test cases. The actual _testing_ of special functions (in Math) with Multiprecision types, however, occurs in the CI/CD of Multiprecision.  
  
That can make it tricky when you write a cool new Math function. Then we add Multiprecision tests and it has CI/CD issues in _Multiprecision_.  
  
Down the road, when really considering running with Multiprecision, we can help you add tests in Multiprecision too if you like.

---

## Comment 20

> Username: jzmaddock  
> Created_at: 2026-01-07 12:44:25 UTC  
> Url: https://github.com/boostorg/math/pull/1346#issuecomment-3718694871  

I've just tweaked the expected error rate again.  
  
Also hooked up some more tests - sorry I should have mentioned these sooner, all new functions should be added to test/compile_test/instantiate.hpp so that they get concept-checked (this includes but isn't limited to, multiprecision concept checking).  There should also be an include test (in this case tests/compile_test/sf_gamma.cpp), and some trivial bits in include/boost/math/special_functions/math_fwd.hpp.  
  
BTW If you need to test 128-bit reals locally I tend to reach for cpp_bin_float_quad from multiprecision.

---

## Comment 21

> Username: JacobHass8  
> Created_at: 2026-01-07 17:59:37 UTC  
> Url: https://github.com/boostorg/math/pull/1346#issuecomment-3720074824  

> I've just tweaked the expected error rate again.  
  
It looks like we're still failing for `real_concept` types. I'll push a commit to change that.   
  
> Also hooked up some more tests - sorry I should have mentioned these sooner, all new functions should be added to test/compile_test/instantiate.hpp so that they get concept-checked (this includes but isn't limited to, multiprecision concept checking). There should also be an include test (in this case tests/compile_test/sf_gamma.cpp), and some trivial bits in include/boost/math/special_functions/math_fwd.hpp.  
  
That's great to know in the future. Thanks for doing a lot of the heavy lifting with this PR!   
  
> BTW If you need to test 128-bit reals locally I tend to reach for cpp_bin_float_quad from multiprecision.  
  
I tried that earlier but wasn't able to recreate the error for some reason. I was finding a difference of 1e-21, not 1e-31 as the error message stated. Maybe I was just doing something wrong though.

---

## Comment 22

> Username: jzmaddock  
> Created_at: 2026-01-07 18:49:51 UTC  
> Url: https://github.com/boostorg/math/pull/1346#issuecomment-3720277311  

>I tried that earlier but wasn't able to recreate the error for some reason. I was finding a difference of 1e-21, not 1e-31 as the error message stated. Maybe I was just doing something wrong though.  
  
Random thought: were you comparing the result to a long double constant?  You need to construct the value to be tested against (the true/expected value) from a string of digits, not a floating point type which will truncate the constant before the multiprecision type gets to see it.

---

## Comment 23

> Username: JacobHass8  
> Created_at: 2026-01-07 19:01:39 UTC  
> Url: https://github.com/boostorg/math/pull/1346#issuecomment-3720328234  

> Random thought: were you comparing the result to a long double constant? You need to construct the value to be tested against (the true/expected value) from a string of digits, not a floating point type which will truncate the constant before the multiprecision type gets to see it.  
  
I think that's the issue! Here's the code I was running   
```C++  
typedef boost::multiprecision::cpp_bin_float_quad T;  
auto a = static_cast<T>(1200);  
auto z = static_cast<T>(1250.25);  
auto trueVal = static_cast<T>(-2.591934862117586205519309712218581885256650074210410262843591453L);  
  
std::cout << boost::math::lgamma_q(a, z) - trueVal << std::endl; // 1e-20  
```  
So I really should have been using `auto trueVal = static_cast<T>("-2.591934862117586205519309712218581885256650074210410262843591453");`.

---

## Comment 24

> Username: JacobHass8  
> Created_at: 2026-01-07 22:55:45 UTC  
> Url: https://github.com/boostorg/math/pull/1346#issuecomment-3721168148  

It looks like we're just failing on cygwin and windows_gcc. Could I just wrap the failing test in   
```C++   
#if defined(WIN32) || defined(_WIN32) || defined(__WIN32) || defined(__CYGWIN__)  
```  
and increase the tolerance?

---

## Comment 25

> Username: jzmaddock  
> Created_at: 2026-01-08 12:27:12 UTC  
> Url: https://github.com/boostorg/math/pull/1346#issuecomment-3723650430  

Yup, I've pushed some tolerance changes.

---

## Comment 26

> Username: ckormanyos  
> Created_at: 2026-01-08 12:52:18 UTC  
> Updated_at: 2026-01-08 12:53:02 UTC  
> Url: https://github.com/boostorg/math/pull/1346#issuecomment-3723742727  

> Yup, I've pushed some tolerance changes.  
  
I'm not sure what's red now. Maybe some compilers don't think you can put the `#` hash symbol within the scope of parentheses. Couple new compile errors. Sigh I suspect the whole line needs to be written out for MINGW/CYGWIN or the lack thereof.

---

## Comment 27

> Username: jzmaddock  
> Created_at: 2026-01-08 15:57:26 UTC  
> Url: https://github.com/boostorg/math/pull/1346#issuecomment-3724518988  

Yup, I put a macro inside a macro, my bad, just pushed a fix.

---

## Comment 28

> Username: ckormanyos  
> Created_at: 2026-01-08 19:12:36 UTC  
> Updated_at: 2026-01-08 19:13:16 UTC  
> Url: https://github.com/boostorg/math/pull/1346#issuecomment-3725285405  

> Failing after 24m  
  
For some reason, there is failure in `test_reverse_mode_autodiff_stl_support.cpp:982`, which looks like tolerance, and is seemingly unrelated to this new function.  
  
After that, we might consider to start preliminarily testing this function in Multiprecision.

---

## Comment 29

> Username: jzmaddock  
> Created_at: 2026-01-09 12:55:33 UTC  
> Url: https://github.com/boostorg/math/pull/1346#issuecomment-3728807578  

Yup, new failure is completely unrelated... merging.

---

## Comment 30

> Username: ckormanyos  
> Created_at: 2026-01-09 20:09:21 UTC  
> Updated_at: 2026-01-09 20:10:15 UTC  
> Url: https://github.com/boostorg/math/pull/1346#issuecomment-3730419948  

I'll try to work out a Multiprecision test when time allows, in the hopes that we get it in for 1.91. This is a cool new function.

---

## Comment 31

> Username: dschmitz89  
> Created_at: 2026-01-12 08:03:59 UTC  
> Updated_at: 2026-01-12 08:04:30 UTC  
> Url: https://github.com/boostorg/math/pull/1346#issuecomment-3737310286  

Awesome, thanks everyone involved :). scipy/xsf contains a [`log1mexp` function](https://github.com/scipy/scipy/pull/22439/files#diff-0d6c07786556f6350d4dc2392dcce30e0caaa57adba6d5df1809dbd4c508de84R72) to accurately compute `ln(1-exp(x)`. Would you consider this a viable approach to compute the log of the incomplete upper gamma `lgamma_p` using the `lgamma_q` from this PR?

---

## Comment 32

> Username: JacobHass8  
> Created_at: 2026-01-12 15:52:17 UTC  
> Url: https://github.com/boostorg/math/pull/1346#issuecomment-3739257441  

> scipy/xsf contains a [`log1mexp` function](https://github.com/scipy/scipy/pull/22439/files#diff-0d6c07786556f6350d4dc2392dcce30e0caaa57adba6d5df1809dbd4c508de84R72) to accurately compute `ln(1-exp(x)`. Would you consider this a viable approach to compute the log of the incomplete upper gamma `lgamma_p` using the `lgamma_q` from this PR?  
  
I don't think that using `log1mexp` would allow the same range of values. `log1mexp` needs to take the exponential of `lgamma_q` and surely this would underflow `lgamma_q` is of order -1000. I'm not 100% sure though. It's worth a shot because it would be a very nice/easy solution! I'll write it up and post my results here.

---

## Comment 33

> Username: JacobHass8  
> Created_at: 2026-01-12 16:26:27 UTC  
> Url: https://github.com/boostorg/math/pull/1346#issuecomment-3739413920  

> Awesome, thanks everyone involved :). scipy/xsf contains a [`log1mexp` function](https://github.com/scipy/scipy/pull/22439/files#diff-0d6c07786556f6350d4dc2392dcce30e0caaa57adba6d5df1809dbd4c508de84R72) to accurately compute `ln(1-exp(x)`. Would you consider this a viable approach to compute the log of the incomplete upper gamma `lgamma_p` using the `lgamma_q` from this PR?  
  
Here's a simple example where this method would break down. I think `lgamma_p` will require it's own implementation, but that shouldn't be too difficult after this PR.   
```C++  
int main(int argc, char* argv[])  
{  
    typedef double T;   
    auto a = static_cast<T>(10);  
    auto z = static_cast<T>(1000);  
      
    std::cout << boost::math::lgamma_q(a, z) << std::endl; // -950.623  
    std::cout << std::exp(boost::math::lgamma_q(a, z)) << std::endl; // 0  
    std::cout << std::log1p(-std::exp(boost::math::lgamma_q(a, z))) << std::endl; // -0  
    return 0;  
}  
```

---

## Comment 34

> Username: JacobHass8  
> Created_at: 2026-01-13 02:08:55 UTC  
> Url: https://github.com/boostorg/math/pull/1346#issuecomment-3741447423  

> Would you consider this a viable approach to compute the log of the incomplete upper gamma `lgamma_p` using the `lgamma_q` from this PR?  
  
Tagging onto my last comment, I implemented a new PR here #1349. I just had to implement a series expansion for large $a$ which wasn't too bad. I only have to figure out more of the logic, add some promotion boilerplate and then write some tests.

---
