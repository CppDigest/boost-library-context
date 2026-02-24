# #1349 Implementation of the log of the lower incomplete gamma function [Merged]

> Username: JacobHass8  
> Created at: 2026-01-13 02:06:02 UTC  
> Updated at: 2026-02-07 17:49:25 UTC  
> Merged at: 2026-01-25 17:52:44 UTC  
> Closed at: 2026-01-25 17:52:44 UTC  
> Url: https://github.com/boostorg/math/pull/1349  

See #1346 and #1173.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2026-01-13 03:22:39 UTC  
> Updated_at: 2026-01-25 17:52:56 UTC  
> Url: https://github.com/boostorg/math/pull/1349#issuecomment-3741658106  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1349?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 95.29%. Comparing base ([`1d670c8`](https://app.codecov.io/gh/boostorg/math/commit/1d670c85b4918d1e703f2fda9abe24d5d8bf60d5?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`76c9c2b`](https://app.codecov.io/gh/boostorg/math/commit/76c9c2bd38d601cda478c25388465ee68fa39dfa?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 24 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1349/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1349?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1349   +/-   ##  
========================================  
  Coverage    95.29%   95.29%             
========================================  
  Files          814      814             
  Lines        67422    67467   +45       
========================================  
+ Hits         64249    64294   +45       
  Misses        3173     3173             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1349?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/math/special\_functions/gamma.hpp](https://app.codecov.io/gh/boostorg/math/pull/1349?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fgamma.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2dhbW1hLmhwcA==) | `99.86% <100.00%> (+<0.01%)` | :arrow_up: |  
| [test/compile\_test/instantiate.hpp](https://app.codecov.io/gh/boostorg/math/pull/1349?src=pr&el=tree&filepath=test%2Fcompile_test%2Finstantiate.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9jb21waWxlX3Rlc3QvaW5zdGFudGlhdGUuaHBw) | `100.00% <100.00%> (ø)` | |  
| [test/test\_igamma.hpp](https://app.codecov.io/gh/boostorg/math/pull/1349?src=pr&el=tree&filepath=test%2Ftest_igamma.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2lnYW1tYS5ocHA=) | `100.00% <100.00%> (ø)` | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/math/pull/1349/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1349?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1349?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [1d670c8...76c9c2b](https://app.codecov.io/gh/boostorg/math/pull/1349?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2026-01-13 12:21:02 UTC  
> Url: https://github.com/boostorg/math/pull/1349#issuecomment-3744024224  

I think your added series is the same as the existing: https://github.com/boostorg/math/blob/1d670c85b4918d1e703f2fda9abe24d5d8bf60d5/include/boost/math/special_functions/gamma.hpp#L425

---

## Comment 3

> Username: JacobHass8  
> Created_at: 2026-01-13 17:07:18 UTC  
> Url: https://github.com/boostorg/math/pull/1349#issuecomment-3745452015  

> I think your added series is the same as the existing:  
>   
> https://github.com/boostorg/math/blob/1d670c85b4918d1e703f2fda9abe24d5d8bf60d5/include/boost/math/special_functions/gamma.hpp#L425  
  
You're absolutely right. Based on the name of the function, I assumed it was a continued fraction implementation. This makes things a lot easier!

---

## Comment 4

> Username: JacobHass8  
> Created_at: 2026-01-13 17:38:04 UTC  
> Url: https://github.com/boostorg/math/pull/1349#issuecomment-3745585071  

I also changed the criteria for when the asymptotic series is used based on code paths in previous functions that used the asymptotic series. With my limited spot testing, this gives accurate results and is ready for testing.

---

## Comment 5

> Username: JacobHass8  
> Created_at: 2026-01-19 18:10:20 UTC  
> Url: https://github.com/boostorg/math/pull/1349#issuecomment-3769597555  

@jzmaddock I think this PR for the lower incomplete gamma function is nearly complete. I'm having some trouble testing long double types on ARM64 architectures where most of the tests are failing. The difference between my computed value and the expected is of order $10^{-18}$ whereas the tolerance is $10^{-30}$. The difference isn't small enough to increase the tolerance.   
  
I'm now thinking the expected values are not quite right. I haven't been able to get Mathematica to output arbitrarily large precision so I've been using the [mpmath](https://mpmath.org/) library for Python. Do you happen to have the Mathematica code you used to calculate values for the upper incomplete gamma function?

---

## Comment 6

> Username: ckormanyos  
> Created_at: 2026-01-19 20:23:11 UTC  
> Url: https://github.com/boostorg/math/pull/1349#issuecomment-3770003480  

I'll bet the platform you are describing uses 10-byte floating-point representations for `long double`. This is about the precision range where 80-bit `long double` reaches. But your control values expect the full 128-bits. You can express your tolerances in terms of multiplies of `std::numeric_limits<T>::epsilon()` and these will actually scale to `T`, assumed to be the floating-point type in the instantiation of the test(s).

---

## Comment 7

> Username: JacobHass8  
> Created_at: 2026-01-19 21:06:06 UTC  
> Updated_at: 2026-01-19 21:06:37 UTC  
> Url: https://github.com/boostorg/math/pull/1349#issuecomment-3770118802  

> I'll bet the platform you are describing uses 10-byte floating-point representations for `long double`. This is about the precision range where 80-bit `long double` reaches. But your control values expect the full 128-bits. You can express your tolerances in terms of multiplies of `std::numeric_limits<T>::epsilon()` and these will actually scale to `T`, assumed to be the floating-point type in the instantiation of the test(s).  
  
I don't think that's the issue. In `test_igamma.hpp`, we set `T tolerance = boost::math::tools::epsilon<T>() * 1000;`. I think the tolerance is right, but my expected values aren't.

---

## Comment 8

> Username: JacobHass8  
> Created_at: 2026-01-20 22:01:13 UTC  
> Url: https://github.com/boostorg/math/pull/1349#issuecomment-3775150526  

Never mind, I got Mathematica to output higher precision. It matches the values that I've entered. So the implementation only has precision up to 10^{-18}. Here's the Mathematica code if anyone is interested.   
<details><summary>Details</summary>  
<p>  
  
```Mathematica  
N[Log[GammaRegularized[100,0,1/10]],64]  
```  
  
</p>  
</details>

---

## Comment 9

> Username: mborland  
> Created_at: 2026-01-20 22:06:32 UTC  
> Url: https://github.com/boostorg/math/pull/1349#issuecomment-3775174934  

> Never mind, I got Mathematica to output higher precision. It matches the values that I've entered. So the implementation only has precision up to 10^{-18}. Here's the Mathematica code if anyone is interested.  
>   
> Details  
> ```mathematica  
> N[Log[GammaRegularized[100,0,1/10]],64]  
> ```  
  
Can you please add that as a comment in the test file for posterity?

---

## Comment 10

> Username: JacobHass8  
> Created_at: 2026-01-20 22:38:37 UTC  
> Url: https://github.com/boostorg/math/pull/1349#issuecomment-3775285008  

> Can you please add that as a comment in the test file for posterity?  
  
I've added this right before the test!

---

## Comment 11

> Username: JacobHass8  
> Created_at: 2026-01-20 22:50:10 UTC  
> Url: https://github.com/boostorg/math/pull/1349#issuecomment-3775319534  

I'm not sure what's going wrong with the `long doubles`. The approximation returns the same result as `log(gamma_p)` and is still off from Mathematica's result by 1e-18. Maybe at some point the `long doubles` are converted to `doubles`?

---

## Comment 12

> Username: jzmaddock  
> Created_at: 2026-01-21 09:21:11 UTC  
> Url: https://github.com/boostorg/math/pull/1349#issuecomment-3777027541  

Not sure it's the whole issue, but it is an issue, this code: `static_cast<T>(0.6)` takes a double approximation to an inexact floating point value, and then converts it to a long double: which is clearly wrong.  `static_cast<T>(0.6L)` is better, but will still fail for multiprecision code (if we test it) where T is wider than a long double.  Using an exact binary value for inputs tends to be my choice, otherwise you need something horrible like `static_cast<T>(static_cast<T>(6) / 10)`.  You should also use 6/10 as the input to mathematica in this case, otherwise 0.6 is interpreted as something like a double precision value.

---

## Comment 13

> Username: JacobHass8  
> Created_at: 2026-01-21 17:55:55 UTC  
> Url: https://github.com/boostorg/math/pull/1349#issuecomment-3780143089  

> Not sure it's the whole issue, but it is an issue, this code: `static_cast<T>(0.6)` takes a double approximation to an inexact floating point value, and then converts it to a long double: which is clearly wrong. `static_cast<T>(0.6L)` is better, but will still fail for multiprecision code (if we test it) where T is wider than a long double. Using an exact binary value for inputs tends to be my choice, otherwise you need something horrible like `static_cast<T>(static_cast<T>(6) / 10)`. You should also use 6/10 as the input to mathematica in this case, otherwise 0.6 is interpreted as something like a double precision value.  
  
I _think_ that might actually be the issue! I just tried this code on my computer and got a difference of order 1e-32.   
  
```C++  
typedef boost::multiprecision::cpp_bin_float_quad T;   
  
T x = static_cast<T>(static_cast<T>(1) / 10);  
T a = static_cast<T>(100);  
T trueVal = static_cast<T>("-594.0968942751157965882143339808498054972378571169718495813176479");  
std::cout << std::setprecision(64) << boost::math::lgamma_p(a, x) - trueVal << std::endl;  
```  
This is strange though because in previous tests for `lgamma_q`, we used   
```C++  
   BOOST_CHECK_CLOSE(::boost::math::lgamma_q(static_cast<T>(20), static_cast<T>(0.25)), static_cast<T>(-2.946458104491857816330873290969917497748067639461638294404e-31L), tolerance);  
```  
and this passed for long doubles.   
  
> Using an exact binary value for inputs tends to be my choice, otherwise you need something horrible like `static_cast<T>(static_cast<T>(6) / 10)`  
  
Is there an easy way to convert `0.6` to a binary literal? I've seen calculators online, but haven't been able to correctly convert them to a `long double`. I might just go with the horrible `static_cast` for now to see if it works.

---

## Comment 14

> Username: jzmaddock  
> Created_at: 2026-01-21 19:31:30 UTC  
> Url: https://github.com/boostorg/math/pull/1349#issuecomment-3780776221  

>This is strange though because in previous tests for lgamma_q, we used  
  
>   BOOST_CHECK_CLOSE(::boost::math::lgamma_q(static_cast<T>(20), static_cast<T>(0.25)), static_cast<T>(-2.946458104491857816330873290969917497748067639461638294404e-31L), tolerance);  
  
>and this passed for long doubles.  
  
Yes, because all the input values have exact binary representations, so you could use 0.25f as the input argument and it would still calculate to whatever precision the result_type is.  
  
>Is there an easy way to convert 0.6 to a binary literal? I've seen calculators online, but haven't been able to correctly convert them to a long double. I might just go with the horrible static_cast for now to see if it works.  
  
No because 0.6 has no exact decimal representation: it's an infinitely recurring number when expressed as base 2 (at least I think it is, 0.1 is the classic example).  So you would need a way to store an infinite number of binary digits.  
  
And that's not all... even if you use an exact fraction, say 6/10 calculated at the precision of T, then the input values for float precision are notably different (truncated) compared to long double, or indeed the arbitrary precision of mathematica.  So if the function is ill-conditioned, the 0.5ulp error in the float input values can be enough to make a significant difference to the result.  
  
So for the sake of easiness, I always choose exact binary values, or where we use randomly generated test data, the inputs are truncated to fit inside a float without precision loss first, and then the expected result is calculated.  This can sometimes miss obscure issues where the last few bits of the input are somehow getting lost in the calculation, but for the sake of everyone's sanity, it sure does save a lot of grief :)

---

## Comment 15

> Username: JacobHass8  
> Created_at: 2026-01-21 20:52:22 UTC  
> Url: https://github.com/boostorg/math/pull/1349#issuecomment-3781111816  

> So for the sake of easiness, I always choose exact binary values, or where we use randomly generated test data, the inputs are truncated to fit inside a float without precision loss first, and then the expected result is calculated. This can sometimes miss obscure issues where the last few bits of the input are somehow getting lost in the calculation, but for the sake of everyone's sanity, it sure does save a lot of grief :)  
  
I hadn't even realized some decimals had exact binary values and others didn't. That's really good to know in the future. Thanks for all your help again with this PR (and the previous)!

---

## Comment 16

> Username: ckormanyos  
> Created_at: 2026-01-21 21:42:47 UTC  
> Updated_at: 2026-01-21 21:43:25 UTC  
> Url: https://github.com/boostorg/math/pull/1349#issuecomment-3781296421  

> I hadn't even realized some decimals had exact binary values and others didn't.  
  
For real @JacobHass8.  
That is a profound point. When using even computer algebra systems, I still write stuff like  
  
```  
N[Whatever[1/4], 50]  
```  
  
to get 50 decimal digits of `Whatever`.  
  
In the C/C++ world, all compilers will take fractional powers of 2 and linear combinations thereof (and also reasonably small) integral values to be exact. Stuff like `1/3` in C/C++ is inexact. Stuff like `0.625` in C/C++ will be exact. It is trippy.  
  
Cc: @jzmaddock and @mborland

---

## Comment 17

> Username: JacobHass8  
> Created_at: 2026-01-22 01:58:00 UTC  
> Url: https://github.com/boostorg/math/pull/1349#issuecomment-3782077267  

Everything looks like it is passing now except g++-14 C++23 autodiff. The error code is below and looks unrelated to the changes that I've made.   
  
<details><summary>Details</summary>  
<p>  
  
```bash  
  
testing.capture-output ../../../bin.v2/libs/math/test/test_autodiff_8.test/gcc-14/release/x86_64/link-static/threading-multi/visibility-hidden/test_autodiff_8.run  
====== BEGIN OUTPUT ======  
Running 53 test cases...  
unknown location(0): fatal error: in "test_autodiff_8/heuman_lambda_hpp<_Float32>": Throw location unknown (consider using BOOST_THROW_EXCEPTION)  
Dynamic exception type: boost::wrapexcept<std::domain_error>  
std::exception::what: Error in function boost::math::heuman_lambda<N5boost4math15differentiation11autodiff_v16detail4fvarIDF32_Lm5EEE>(N5boost4math15differentiation11autodiff_v16detail4fvarIDF32_Lm5EEE, N5boost4math15differentiation11autodiff_v16detail4fvarIDF32_Lm5EEE): When 1-k^2 == 1 then phi must be < Pi/2, but got phi = depth(1)(4.20396852,1,0,0,0,0)  
  
test_autodiff_8.cpp(38): last checkpoint  
  
*** 1 failure is detected in the test module "test_autodiff"  
```  
  
</p>  
</details>

---

## Comment 18

> Username: JacobHass8  
> Created_at: 2026-01-22 02:01:15 UTC  
> Url: https://github.com/boostorg/math/pull/1349#issuecomment-3782088023  

I should still update the docs and add Cuda support which I'm just going to copy from #1346.

---

## Comment 19

> Username: jzmaddock  
> Created_at: 2026-01-24 12:51:59 UTC  
> Url: https://github.com/boostorg/math/pull/1349#issuecomment-3794583752  

Just checking, is this one good to merge now?

---

## Comment 20

> Username: JacobHass8  
> Created_at: 2026-01-24 17:56:33 UTC  
> Url: https://github.com/boostorg/math/pull/1349#issuecomment-3795274912  

> Just checking, is this one good to merge now?  
  
I just changed a typo in the documentation and a spurious `if` statement that wasn't going to be used. If all the tests pass again, I don't have any further changes!

---

## Comment 21

> Username: jzmaddock  
> Created_at: 2026-01-25 17:52:39 UTC  
> Url: https://github.com/boostorg/math/pull/1349#issuecomment-3797014510  

Failure is unrelated to this, merging...

---

## Comment 22

> Username: mdhaber  
> Created_at: 2026-02-07 17:49:25 UTC  
> Url: https://github.com/boostorg/math/pull/1349#issuecomment-3864946467  

Thanks very much, all! This will be very helpful.

---
