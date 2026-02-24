# #711 Obtain more code coverage [Merged]

> Username: ckormanyos  
> Created at: 2025-07-03 14:23:38 UTC  
> Updated at: 2025-08-31 14:41:33 UTC  
> Merged at: 2025-08-31 10:58:47 UTC  
> Closed at: 2025-08-31 10:58:47 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/711  

This PR obtains more code coverage. But it is for later in 1.90 (not for today's 1.89).

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-07-03 15:12:12 UTC  
> Updated_at: 2025-08-31 14:41:33 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/711#issuecomment-3032632975  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/711?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:x: Patch coverage is `99.89259%` with `1 line` in your changes missing coverage. Please review.  
:white_check_mark: Project coverage is 96.2%. Comparing base ([`55bf069`](https://app.codecov.io/gh/boostorg/multiprecision/commit/55bf06962149d1b150fe9eb42d6366f672251b9d?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`5b53322`](https://app.codecov.io/gh/boostorg/multiprecision/commit/5b533226741a32a3a41e641e38554a5cd50a88a7?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 65 commits behind head on develop.  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/multiprecision/pull/711?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Patch % | Lines |  
|---|---|---|  
| [include/boost/multiprecision/cpp\_dec\_float.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/711?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_dec_float.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfZGVjX2Zsb2F0LmhwcA==) | 99.7% | [1 Missing :warning: ](https://app.codecov.io/gh/boostorg/multiprecision/pull/711?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/711/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/711?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@            Coverage Diff            @@  
##           develop    #711     +/-   ##  
=========================================  
+ Coverage     94.7%   96.2%   +1.5%       
=========================================  
  Files          296     297      +1       
  Lines        30881   31177    +296       
=========================================  
+ Hits         29233   29967    +734       
+ Misses        1648    1210    -438       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/multiprecision/pull/711?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [example/exercise\_threading\_log\_agm.cpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/711?src=pr&el=tree&filepath=example%2Fexercise_threading_log_agm.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-ZXhhbXBsZS9leGVyY2lzZV90aHJlYWRpbmdfbG9nX2FnbS5jcHA=) | `100.0% <100.0%> (ø)` | |  
| [include/boost/multiprecision/complex\_adaptor.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/711?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcomplex_adaptor.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jb21wbGV4X2FkYXB0b3IuaHBw) | `92.3% <100.0%> (+2.8%)` | :arrow_up: |  
| [include/boost/multiprecision/cpp\_bin\_float.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/711?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_bin_float.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfYmluX2Zsb2F0LmhwcA==) | `93.9% <ø> (+2.7%)` | :arrow_up: |  
| [include/boost/multiprecision/cpp\_double\_fp.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/711?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_double_fp.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfZG91YmxlX2ZwLmhwcA==) | `100.0% <100.0%> (ø)` | |  
| [...nclude/boost/multiprecision/detail/default\_ops.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/711?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fdetail%2Fdefault_ops.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9kZXRhaWwvZGVmYXVsdF9vcHMuaHBw) | `93.5% <ø> (+2.5%)` | :arrow_up: |  
| [...oost/multiprecision/detail/functions/constants.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/711?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fdetail%2Ffunctions%2Fconstants.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9kZXRhaWwvZnVuY3Rpb25zL2NvbnN0YW50cy5ocHA=) | `99.2% <100.0%> (ø)` | |  
| [...lude/boost/multiprecision/detail/functions/pow.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/711?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fdetail%2Ffunctions%2Fpow.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9kZXRhaWwvZnVuY3Rpb25zL3Bvdy5ocHA=) | `99.4% <100.0%> (+4.7%)` | :arrow_up: |  
| [...ude/boost/multiprecision/detail/functions/trig.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/711?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fdetail%2Ffunctions%2Ftrig.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9kZXRhaWwvZnVuY3Rpb25zL3RyaWcuaHBw) | `97.5% <ø> (+0.8%)` | :arrow_up: |  
| [test/git\_issue\_167.cpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/711?src=pr&el=tree&filepath=test%2Fgit_issue_167.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9naXRfaXNzdWVfMTY3LmNwcA==) | `100.0% <100.0%> (+50.0%)` | :arrow_up: |  
| [test/git\_issue\_464.cpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/711?src=pr&el=tree&filepath=test%2Fgit_issue_464.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9naXRfaXNzdWVfNDY0LmNwcA==) | `100.0% <100.0%> (ø)` | |  
| ... and [17 more](https://app.codecov.io/gh/boostorg/multiprecision/pull/711?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
... and [4 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/multiprecision/pull/711/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/711?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/711?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [55bf069...5b53322](https://app.codecov.io/gh/boostorg/multiprecision/pull/711?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2025-07-05 20:10:30 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/711#issuecomment-3039833367  

Ok so I worked on coverage, mostly with focus on `cpp_dec_float`. That file is pretty well covered now, but missing 8 interesting lines. I'll let this one sit for a while.  
  
I would like to improve the line coverage of the tests and then we can discuss which headers need more dedicated edge tests.  
  
Let's just leave this as a draft until the action on 1.90 wraps up.

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2025-07-05 20:16:45 UTC  
> Updated_at: 2025-08-16 17:01:34 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/711#issuecomment-3039842747  

In `cpp_dec_float` going to nearly $99\\%$ coverage was straightforward. Sure it needed a few hundred lines of dedicated test code.  
  
I only found 1 bug, but harmless, in the area of negative infinity ($\-\infty$). There were several areas having redundant or unclear lines. I cleared up (or subjectively think I cleared up) a bunch of these sequences. Nothing really critical was found, and this shows how well this thing was tested to begin with. I'm really impressed with the test-suite.

---

## Comment 4

> Username: ckormanyos  
> Created_at: 2025-07-15 16:53:22 UTC  
> Updated_at: 2025-07-15 17:00:35 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/711#issuecomment-3074412289  

Hi @jzmaddock and @mborland  
  
Check it out, WE WILL NOT merge this until AFTER 1.89.  
  
I have now marked this as _ready for review_. With this, I mean _AFTER_ 1.89 gets out the door. But the scope of change is non-trivial.  
  
So I saw a window of opportunity (timewise and energy-wise) to go for coverage. I got a solid $96.1\\%$, which is good.  
  
OK so there were some highlights.  
  
- I found a small handful of bugs in `cpp_dec_float` and in some special functions edge-cases.  
- I think that some infinity/NaN edge-cases in `default_ops`-pow were incorrect (also `erf`and `erfc`, maybe `tgamma`).  
- In general I think these things are more right now than earlier, but there is work to be done.  
- With this, I got the special-functions C99 thing to actually use signed-zero on `cpp_bin_float` to get lots of coverage lines. I think it is _right_-er than before, but not yet fully done.   
- I leaned heavily into `cpp_dec_float` and got under 10 lines missing in coverage. I hit this one, changed quite a bit actually. But my confidence is high here.  
- I did some trivial changes to `complex_adaptor` and its tests to gain lots of lines.  
- Along those lines, I got `cpp_dec_float` working with `complex_adaptor`, which was previously not the case since some `eval_whatever` functions were actually missing from this backend.  
- I got the test cases `issue464` and `issue167` actually working and contributing to coverage. These were simply exception-ing out previously.  
- I found redundant code in a few places like `pow`functions and streamlined them.  
  
Well, that's about it.

---

## Comment 5

> Username: ckormanyos  
> Created_at: 2025-07-15 16:59:34 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/711#issuecomment-3074439064  

There is a lot of material within the scope of this PR. It might take a while to sift through it all and negotiate on the best (or better) fixes, as I might not have subjectively always chosen the right path.  
  
But in general, I think this PR is intact. And we can start taking a serious look at it.

---

## Comment 6

> Username: ckormanyos  
> Created_at: 2025-08-15 16:27:18 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/711#issuecomment-3192088971  

I think it is time to review this PR.  
  
Hi @jzmaddock and @mborland now that 1.89 is out, I'm satisfied with this PR for the moment. This PR is all about coverage and some syntax. Some bugs were found and corrected.  
  
The highlights (repeated from above) include:  
  
- I found a small handful of bugs in `cpp_dec_float` and in some special functions edge-cases.  
- I think that some infinity/NaN edge-cases in `default_ops`-pow were incorrect (also `erf`and `erfc`, maybe `tgamma`).  
- In general I think these things are more right now than earlier, but there is work to be done.  
- With this, I got the special-functions C99 thing to actually use signed-zero on `cpp_bin_float` to get lots of coverage lines. I think it is _right_-er than before, but not yet fully done.   
- I leaned heavily into `cpp_dec_float` and got under 10 lines missing in coverage. I hit this one, changed quite a bit actually. But my confidence is high here.  
- I did some trivial changes to `complex_adaptor` and its tests to gain lots of lines.  
- Along those lines, I got `cpp_dec_float` working with `complex_adaptor`, which was previously not the case since some `eval_whatever` functions were actually missing from this backend.  
- I got the test cases `issue464` and `issue167` actually working and contributing to coverage. These were simply exception-ing out previously.  
- I found redundant code in a few places like `pow`functions and streamlined them.

---

## Comment 7

> Username: ckormanyos  
> Created_at: 2025-08-16 13:25:44 UTC  
> Updated_at: 2025-08-16 13:26:30 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/711#issuecomment-3193669333  

OK this one is green again and ready for review.  
  
I'm pretty satisfied with these results. Project coverage is at a solid $96.2\\%$, with `cpp_double_fp_backend.hpp` fully covered and `cpp_dec_float` missing fewer than $5$ lines.  
  
A few bug fixes were done along the way. In future work, I'd like to see how to hit some of the missing lines in `cpp_int.hpp` and `cpp_bin_float.hpp`. I took a few tries at this but only had modest success.  
  
When stressing `cpp_bin_float`, I ran across #726, which seems like an actual (still non-solved) bug.  
  
There is also a hefty TODO list (for myself) at #703. So after concluding this `more_coverage` PR, I'll move on to #703 and possibly #726.  
  
Cc: @jzmaddock and @mborland

---

## Review 8 [Commented]

> Username: jzmaddock  
> Created_at: 2025-08-26 10:36:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/711#pullrequestreview-3155018586  

📁 include/boost/multiprecision/detail/default_ops.hpp

```diff
2620 | inline BOOST_MP_CXX14_CONSTEXPR multiprecision::number<Backend, ExpressionTemplates> erf BOOST_PREVENT_MACRO_SUBSTITUTION(const multiprecision::number<Backend, ExpressionTemplates>& arg)
2621 | {
2622 |+    const int fpc_arg { (boost::multiprecision::fpclassify)(arg) };
```

> Username: jzmaddock  
> Created_at: 2025-08-26 10:36:43 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/711#discussion_r2300563986  

Using fpclassify here is a bit heavyweight just to test for zero, using `arg.is_zero()` would be better, but actually, why do we need this at all - surely boost::math::erf should handle this already?

> Username: ckormanyos  
> Created_at: 2025-08-26 16:56:14 UTC  
> Updated_at: 2025-08-26 16:56:15 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/711#discussion_r2301595258  

Understood. Thank you. I will repair this.

> Username: ckormanyos  
> Created_at: 2025-08-29 06:20:38 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/711#discussion_r2309263328  

Resolved above


---

## Review 9 [Commented]

> Username: jzmaddock  
> Created_at: 2025-08-26 10:38:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/711#pullrequestreview-3155022095  

📁 include/boost/multiprecision/detail/default_ops.hpp

```diff
2636 |+    detail::scoped_default_precision<value_type> precision_guard(arg);
2637 |+ 
2638 |+    const int fpc_arg { (boost::multiprecision::fpclassify)(arg) };
```

> Username: jzmaddock  
> Created_at: 2025-08-26 10:38:00 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/711#discussion_r2300566786  

As above, but this is even worse, since we evaluate the same expression template *twice* here, once for fpclassify and once for the erf call.

> Username: ckormanyos  
> Created_at: 2025-08-26 16:56:21 UTC  
> Updated_at: 2025-08-26 16:56:22 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/711#discussion_r2301595523  

Understood. Thank you. I will repair this.

> Username: ckormanyos  
> Created_at: 2025-08-28 10:21:57 UTC  
> Updated_at: 2025-08-28 10:22:24 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/711#discussion_r2306958885  

Hi John (@jzmaddock) I finally had a moment and started looking into these edge cases.  
  
In my branch, I have activated the `test_c99_appendix_F()` tests for both `cpp_double_double` as well as `cpp_bin_float` (which had previously not been the case). This all happens in the file `test_sf_import_c99.cpp` in Multiprecision's vast suite of tests.  
  
The problem arises in the special function test that tests negative 0 in the error function [in my branch here](https://github.com/boostorg/multiprecision/blob/2f737ffa6372582b3f9c9ccea9b2ee7b8b8764ad/test/test_sf_import_c99.cpp#L1835).  
  
When doing this, the [_generic_ implementation](https://github.com/boostorg/math/blob/43239024883d0f4986bdfce4cb471b3563faf161/include/boost/math/special_functions/erf.hpp#L147) of `erf_imp()` is called.  
  
When `erf(cpp_bin_float(-0.0))` is called, the generic implementation does not catch the negative zero. It simply goes to the Taylor series and returns zero.  
  
So I corrected a problem, I just corrected it in the wrong place. We actually need to add a very few lines to [`erf_imp()`](https://github.com/boostorg/math/blob/43239024883d0f4986bdfce4cb471b3563faf161/include/boost/math/special_functions/erf.hpp#L147).

> Username: ckormanyos  
> Created_at: 2025-08-28 10:23:11 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/711#discussion_r2306961625  

I am happy to take a try at the changes in Math's specfun.

> Username: ckormanyos  
> Created_at: 2025-08-29 06:15:04 UTC  
> Updated_at: 2025-08-29 06:15:05 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/711#discussion_r2309254798  

I'm working out a sensible repair in Math, partly based on your suggestion below. Thanks @jzmaddock


---

## Review 10 [Commented]

> Username: jzmaddock  
> Created_at: 2025-08-26 10:42:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/711#pullrequestreview-3155034385  

📁 include/boost/multiprecision/detail/default_ops.hpp

```diff
2711 |+    else if ((fpc_arg == FP_INFINITE) && std::numeric_limits<local_number_type>::has_infinity && std::numeric_limits<local_number_type>::has_quiet_NaN)
2712 |+    {
2713 |+       const bool is_neg { arg.compare(local_number_type(0)) < 0 };
```

> Username: jzmaddock  
> Created_at: 2025-08-26 10:42:26 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/711#discussion_r2300576294  

`arg.sign() < 0` would be more efficient.  
  
As before I'm wondering why tgamma doesn't already handle this, and/or if there might not be a bug somewhere else?

> Username: ckormanyos  
> Created_at: 2025-08-26 16:57:36 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/711#discussion_r2301598736  

> As before I'm wondering why tgamma doesn't already handle this, and/or if there might not be a bug somewhere else?  
  
I will look into this. I was fiddling around with `tgamma` because of coverage in the C99-function compatibility tests. Maybe a focussed on the wrong place. I will investigate this prior to merging the PR.

> Username: ckormanyos  
> Created_at: 2025-08-29 06:21:45 UTC  
> Updated_at: 2025-08-29 06:21:46 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/711#discussion_r2309265176  

I simply reverted the `tgamma` changes. I do not (locally) see any more need from them. Perhaps I was initially mistaken. I'll see how CI/CD run in both Math and Multiprecision and repair anything if still present.


---

## Review 11 [Commented]

> Username: jzmaddock  
> Created_at: 2025-08-26 10:45:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/711#pullrequestreview-3155045805  

📁 include/boost/multiprecision/detail/functions/pow.hpp

```diff
 511 |+ 
 512 |+    const int fpc_x { eval_fpclassify(x) };
 513 |+    const int fpc_a { eval_fpclassify(a) };
```

> Username: jzmaddock  
> Created_at: 2025-08-26 10:45:55 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/711#discussion_r2300584065  

I don't think fpc_a is used anywhere else?  If not then comparison to zero should be faster, but even better would be `eval_is_zero`.

> Username: ckormanyos  
> Created_at: 2025-08-26 16:57:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/711#discussion_r2301599316  

Understood. Thank you. I will repair this.

> Username: ckormanyos  
> Created_at: 2025-08-29 06:30:33 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/711#discussion_r2309280299  

Actually, the newly introduced `fpc_a` and `fpc_x` are used below, both of them a few times. Indeed, we could discuss if and when it makes sense to pull out the heavyweight `eval_fpclassify`.  
  
For the exact change above, I reverted to `eval_is_zero(a)` for the common case of zero exponent which returns result 1 (even for NaN). That is a good, fast catch.


---

## Comment 12

> Username: jzmaddock  
> Created_at: 2025-08-26 10:52:51 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/711#issuecomment-3223659626  

Thanks Chris for this, looks good other than my few comments on the header changes.

---

## Comment 13

> Username: jzmaddock  
> Created_at: 2025-08-28 18:00:06 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/711#issuecomment-3234440192  

>When erf(cpp_bin_float(-0.0)) is called, the generic implementation does not catch the negative zero. It simply goes to the Taylor series and returns zero.  
  
Good catch!  
  
My gut says something like:  
  
```  
if(fabs(z) < tools::root_epsilon<T>())  
   return invert ? 1 : 2 * z / constants::root_pi<T>();  
```  
  
Which should make that zone more efficient as well as fixing the signed zero issue?  Note: just typed in off the top of my head!!

---

## Comment 14

> Username: ckormanyos  
> Created_at: 2025-08-31 10:58:38 UTC  
> Updated_at: 2025-08-31 10:59:55 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/711#issuecomment-3240048993  

Removed the `constexpr`-ness efforts, as these will be handled in a future, separate branch. The most modern, mostly passing commit having the bulk of the `constexpr`-ness efforts is ac1b9545e7775bfb7918f5558f74567a897bbe93.

---
