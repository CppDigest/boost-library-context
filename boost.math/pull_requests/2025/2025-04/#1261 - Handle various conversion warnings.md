# #1261 Handle various conversion warnings [Merged]

> Username: ckormanyos  
> Created at: 2025-04-27 07:19:08 UTC  
> Updated at: 2025-04-28 10:48:34 UTC  
> Merged at: 2025-04-27 17:02:35 UTC  
> Closed at: 2025-04-27 17:02:35 UTC  
> Url: https://github.com/boostorg/math/pull/1261  

Hi folks,  
  
I'm really happy with this one. After literally years of improvements in our combined work, it is now possible to run parts of Math + Multiprecision _unchanged_ off-the-rack on both PC as well as embedded targets.  
  
The purpose of this PR is to handle a few last warnings found when cross-compiling and running Math + Multiprecision on both PC-based GCC as well as 32-bit embedded ARM(R) bare-metal `gcc-arrm-none-eabi`.  
  
The microcontroller compiler found several warnings that seemed quite reasonable to deal with.  
  
I used warnings:  
  
```bash  
WARN_FLAGS  :=  -Wall                             \  
                -Wextra                           \  
                -Wpedantic                        \  
                -Wmain                            \  
                -Wundef                           \  
                -Wconversion                      \  
                -Wsign-conversion                 \  
                -Wunused-parameter                \  
                -Wuninitialized                   \  
                -Wshadow  
```  
  
This PR does not cover warnings in _all_ of Math, nor in _all_ of Multiprecision. But quite a bit of specfun and both `cpp_bin_float` (which also pulls in `cpp_int`) as well as `cpp_dec_float` at $101$ decimal digits + Math have been successfully executed on PC and microcontroller and become relatively warning-free.  
  
For `cpp_bin_float` and `cpp_dec_float` small amounts of dynamic RAM were needed (primarily for `std::allocator` in the `std::string` template class). So I wrote a tiny one-shot allocator for the global `new()` function and dispensed with the heap.  
  
The Multirpecision benchmark computes the $101$ decimal digit cube root value of  
  
$$  
\left(\frac{123456}{100}\right)^{1/3}~\approx~10.72763694322831704548693173735276478\ldots  
$$  
  
The Math benchmark computes a small series of cylindrical Bessel function values for 64-bit `::boost::float64_t`  
  
$$  
J_{1.23}\left(1/n\right), n=1{\ldots}10  
$$  
  
The FLASH/RAM consumptions and timings for the Multiprecision benchmark are shown below (target ARM(R) Cortex(R) M4F STM32F446 from ST-Microelectronics(R) clocked at $168~MHz$). The multiprecision numbers were instantiated with $101$ decimal digits. I also used stubbed functions for some C-language posix primitives such as those pulled in by I/O streaming (needed by Math's exception handling even when exceptions are turned off). I also used RTTI disabled, optimization `-O2`.  
  
| type                 | FLASH                    | RAM                      | time [ms]    |  
|-----------------|---------------------|----------------------|--------------|  
| `cpp_bin_float` | 97k                        | 6.3k                        |  1.0              |  
| `cpp_dec_float` | 80k                        | 26k                        |  2.8              |  
  
Cc: @jzmaddock and @mborland

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2025-04-27 07:30:20 UTC  
> Url: https://github.com/boostorg/math/pull/1261#issuecomment-2833262475  

Oh yeah, you'll also see some warnings handled in other stuff like `toms748_solve`. These are in there since I also did some warning hunting on Ubuntu using my own [`extended_complex`](https://github.com/ckormanyos/extended_complex) adaption to comupte $500$ decimal digit non-trivial roots of the complex-valued Riemann-zeta function, another cool project I've benched on for the past ten years or so...

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2025-04-27 07:59:49 UTC  
> Updated_at: 2025-04-27 17:02:42 UTC  
> Url: https://github.com/boostorg/math/pull/1261#issuecomment-2833281202  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1261?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: Patch coverage is `94.73684%` with `1 line` in your changes missing coverage. Please review.  
> Project coverage is 93.91%. Comparing base [(`5130435`)](https://app.codecov.io/gh/boostorg/math/commit/51304355aa3c8db074cdeac4e33ba10c2130df1c?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`bc0635f`)](https://app.codecov.io/gh/boostorg/math/commit/bc0635f5bee177420fa5ec2c708c360943592976?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 6 commits behind head on develop.  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1261?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Patch % | Lines |  
|---|---|---|  
| [...ath/special\_functions/detail/bernoulli\_details.hpp](https://app.codecov.io/gh/boostorg/math/pull/1261?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fdetail%2Fbernoulli_details.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2RldGFpbC9iZXJub3VsbGlfZGV0YWlscy5ocHA=) | 50.00% | [1 Missing :warning: ](https://app.codecov.io/gh/boostorg/math/pull/1261?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1261/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1261?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@            Coverage Diff            @@  
##           develop    #1261    +/-   ##  
=========================================  
  Coverage    93.90%   93.91%              
=========================================  
  Files          661      661              
  Lines        54559    54866   +307       
=========================================  
+ Hits         51233    51526   +293       
- Misses        3326     3340    +14       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1261?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/math/constants/info.hpp](https://app.codecov.io/gh/boostorg/math/pull/1261?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fconstants%2Finfo.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2NvbnN0YW50cy9pbmZvLmhwcA==) | `97.14% <ø> (ø)` | |  
| [include/boost/math/distributions/landau.hpp](https://app.codecov.io/gh/boostorg/math/pull/1261?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdistributions%2Flandau.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvbGFuZGF1LmhwcA==) | `95.78% <ø> (ø)` | |  
| [include/boost/math/policies/error\_handling.hpp](https://app.codecov.io/gh/boostorg/math/pull/1261?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fpolicies%2Ferror_handling.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3BvbGljaWVzL2Vycm9yX2hhbmRsaW5nLmhwcA==) | `85.58% <100.00%> (ø)` | |  
| [include/boost/math/policies/policy.hpp](https://app.codecov.io/gh/boostorg/math/pull/1261?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fpolicies%2Fpolicy.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3BvbGljaWVzL3BvbGljeS5ocHA=) | `95.65% <ø> (ø)` | |  
| [include/boost/math/special\_functions/bessel.hpp](https://app.codecov.io/gh/boostorg/math/pull/1261?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fbessel.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2Jlc3NlbC5ocHA=) | `100.00% <ø> (ø)` | |  
| [.../boost/math/special\_functions/detail/bessel\_jn.hpp](https://app.codecov.io/gh/boostorg/math/pull/1261?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fdetail%2Fbessel_jn.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2RldGFpbC9iZXNzZWxfam4uaHBw) | `100.00% <100.00%> (ø)` | |  
| [.../boost/math/special\_functions/detail/bessel\_jy.hpp](https://app.codecov.io/gh/boostorg/math/pull/1261?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fdetail%2Fbessel_jy.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2RldGFpbC9iZXNzZWxfankuaHBw) | `100.00% <100.00%> (ø)` | |  
| [...math/special\_functions/detail/bessel\_jy\_series.hpp](https://app.codecov.io/gh/boostorg/math/pull/1261?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fdetail%2Fbessel_jy_series.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2RldGFpbC9iZXNzZWxfanlfc2VyaWVzLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [...ost/math/special\_functions/detail/igamma\_large.hpp](https://app.codecov.io/gh/boostorg/math/pull/1261?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fdetail%2Figamma_large.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2RldGFpbC9pZ2FtbWFfbGFyZ2UuaHBw) | `100.00% <ø> (ø)` | |  
| [...ost/math/special\_functions/detail/lgamma\_small.hpp](https://app.codecov.io/gh/boostorg/math/pull/1261?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fdetail%2Flgamma_small.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2RldGFpbC9sZ2FtbWFfc21hbGwuaHBw) | `100.00% <ø> (ø)` | |  
| ... and [10 more](https://app.codecov.io/gh/boostorg/math/pull/1261?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1261?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1261?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [5130435...bc0635f](https://app.codecov.io/gh/boostorg/math/pull/1261?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Review 3 [Commented]

> Username: jzmaddock  
> Created_at: 2025-04-27 08:39:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1261#pullrequestreview-2797565491  

📁 include/boost/math/policies/policy.hpp

```diff
 134 |    template <Type N = Default>                                                                  \
 135 |-    class name : public boost::math::integral_constant<int, N>{};                                \
 135 |+    class name : public boost::math::integral_constant<int, static_cast<int>(N)>{};              \
```

> Username: jzmaddock  
> Created_at: 2025-04-27 08:39:49 UTC  
> Url: https://github.com/boostorg/math/pull/1261#discussion_r2062564374  

Should we cast to `Type` here since the argument could be a short or something (I'm sure it never is in practice, but it could be).

> Username: ckormanyos  
> Created_at: 2025-04-27 09:17:25 UTC  
> Url: https://github.com/boostorg/math/pull/1261#discussion_r2062572801  

I think you're right. But to be clear, you mean changing to something like below, right? That would make the most sense to me upon reading your comment.  
  
```cpp  
class name : public boost::math::integral_constant<Type, N>{};  
```

> Username: jzmaddock  
> Created_at: 2025-04-27 11:14:01 UTC  
> Url: https://github.com/boostorg/math/pull/1261#discussion_r2062597021  

Yes.  But thinking about it, I'm not sure if that change might have unintended ripple effects - you might need to try it and see!

> Username: ckormanyos  
> Created_at: 2025-04-27 17:02:26 UTC  
> Url: https://github.com/boostorg/math/pull/1261#discussion_r2062678223  

Hi John. It worked fine. I had a few typos underway. I will now merge this.  
  
Cc: @jzmaddock and @mborland


---

## Review 4 [Commented]

> Username: jzmaddock  
> Created_at: 2025-04-27 08:48:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1261#pullrequestreview-2797567826  

📁 include/boost/math/special_functions/detail/bernoulli_details.hpp

```diff
 374 |             {
 375 |-                *out = b2n_asymptotic<T, Policy>(static_cast<typename container_type::size_type>(start * 2U));
 375 |+                *out = b2n_asymptotic<T, Policy>(static_cast<int>(start * 2U));
```

> Username: jzmaddock  
> Created_at: 2025-04-27 08:48:45 UTC  
> Url: https://github.com/boostorg/math/pull/1261#discussion_r2062566360  

I have a hunch this is the wrong fix: a Bernoulli number may be too large for type `int` (overflow) and/or the wrong type for the iterator we are copying too - as the OutputIterator is a template parameter.    
  
Ideally, we would figure out what the `value_type` of the iterator is, and cast to that, but I don't think we can do that in C++20?  Failing that I think you need to look back up the instantiation stack to see what OutputIterator caused the warning, and possibly you may have to live with it (ie it's a genuine warning that you are doing something which may overflow).

> Username: ckormanyos  
> Created_at: 2025-04-27 09:24:28 UTC  
> Url: https://github.com/boostorg/math/pull/1261#discussion_r2062574434  

But the function `b2n_asymptotic(int)` itself accepts an input parameter of type `int`. Would it make sense to refactor that entire thing to accept `std::size_t` or even `std::ptrdiff_t`? The asme is true for `t2n_asymptotic(int)`.

> Username: jzmaddock  
> Created_at: 2025-04-27 11:16:06 UTC  
> Url: https://github.com/boostorg/math/pull/1261#discussion_r2062597442  

Sorry Chris, I completely mis-read that, please ignore my comment, I thought you were casting the result to an int not the argument.  You were quite right!


---

## Comment 5

> Username: jzmaddock  
> Created_at: 2025-04-27 08:54:18 UTC  
> Url: https://github.com/boostorg/math/pull/1261#issuecomment-2833335116  

Thanks Chris, other than my couple of comments, this all looks good to me.

---

## Comment 6

> Username: ckormanyos  
> Created_at: 2025-04-27 17:04:58 UTC  
> Updated_at: 2025-04-27 17:05:39 UTC  
> Url: https://github.com/boostorg/math/pull/1261#issuecomment-2833550588  

OK so I see some `LCOV` warnings and (in this issue not even dealing with that) I did some corrections.  
  
But I'm not quite sure that we have the code annotations exactly right. I looked 3 times but could not find the root cause of inconsistencies.  
  
When the `specfun` job that is in the `cover` YAML uploads the results, there are line/annotation inconsistencies. I repaired some of them and got more coverage and consistency, but we are still probably better than we thought.  
  
I'll track this down in another issue. This is not the proper place to perfect our cover annotations.  
  
Cc: @jzmaddock and @mborland

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2025-04-28 08:42:11 UTC  
> Url: https://github.com/boostorg/math/pull/1261#issuecomment-2834467160  

>OK so I see some LCOV warnings and (in this issue not even dealing with that) I did some corrections.  
  
>But I'm not quite sure that we have the code annotations exactly right. I looked 3 times but could not find the root cause of inconsistencies.  
  
>When the specfun job that is in the cover YAML uploads the results, there are line/annotation inconsistencies. I repaired some of them and got more coverage and consistency, but we are still probably better than we thought.  
  
I haven't really touched on anything in /detail/ yet and I know there's a lot to look at in there.  
  
What I'm trying to do is only annotate when:  
  
1) It's confirmed as covered by a test (lets say a multiprecision one) that isn't run with coverage analysis because it's too slow.  
2) When it's a GCOV SNAFU: these are mostly multi-line statements which GCOV just can't get right for some reason, the classic is a function call:  
  
```  
f(a,  
  b,  // not covered!!  
  c);  
```  
  
Which I change to  
  
```  
f(a, b, c);  
```  
  
even though that often makes things harder to read, or else a table:  
  
```  
T values[] = {  
... ,   // non of these lines show up as covered.  
};  
```  
  
Where I bracket the whole thing in LCOV_EXCL_START/LCOV_EXCL_STOP annotations  
  
Other than that I'm trying to actually find test cases, and/or check to see if it's actually dead code which can be removed and/or converted to an assert.

---

## Comment 8

> Username: ckormanyos  
> Created_at: 2025-04-28 10:47:52 UTC  
> Updated_at: 2025-04-28 10:48:33 UTC  
> Url: https://github.com/boostorg/math/pull/1261#issuecomment-2834840672  

Yes John (@jzmaddock), I agree with all that.  
  
> It's confirmed as covered by a test (lets say a multiprecision one) that isn't run with coverage analysis because it's too slow.  
  
Regarding this particular phrase pertaining to Multiprecision, ...  
  
I was looking at a few of these. I was wondering what you thought of the idea of making a few selected multiprecision _spot_ _values_? These could be designed to run quickly, having just a few values each, and still cover some of those Multiprecision-only sequences in _specfun_. Then they could theoretically get into the coverage report and hopefully not add too much run-time on the cover job.  
  
I could start looking into this if you think it's the way to go.  
  
Cc: @mborland

---
