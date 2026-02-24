# #701 double fp backend from gsoc21 [Merged]

> Username: ckormanyos  
> Created at: 2025-06-26 04:39:39 UTC  
> Updated at: 2025-06-28 17:37:28 UTC  
> Merged at: 2025-06-27 20:52:50 UTC  
> Closed at: 2025-06-27 20:52:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/701  

This PR replaces #515 and becomes the one expected to be officially merged into [boostorg/multiprecision](https://github.com/boostorg/multiprecision).  
  
This PR more properly preserves the commit log from the original GSoC21 project.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-06-26 05:16:28 UTC  
> Updated_at: 2025-06-27 20:54:16 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/701#issuecomment-3007083923  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/701?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: Patch coverage is `99.17864%` with `12 lines` in your changes missing coverage. Please review.  
> Project coverage is 93.9%. Comparing base [(`6df8e19`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/6df8e19f00632ef1d00ec5cc0cd813a22a4df201?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`d11a72e`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/d11a72ed0535b6f683537bb09131cabc1425c3be?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 747 commits behind head on develop.  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/multiprecision/pull/701?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Patch % | Lines |  
|---|---|---|  
| [include/boost/multiprecision/cpp\_double\_fp.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/701?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_double_fp.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfZG91YmxlX2ZwLmhwcA==) | 98.7% | [10 Missing :warning: ](https://app.codecov.io/gh/boostorg/multiprecision/pull/701?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
| [include/boost/multiprecision/float128.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/701?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Ffloat128.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9mbG9hdDEyOC5ocHA=) | 0.0% | [1 Missing :warning: ](https://app.codecov.io/gh/boostorg/multiprecision/pull/701?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
| [test/test\_round.cpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/701?src=pr&el=tree&filepath=test%2Ftest_round.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3JvdW5kLmNwcA==) | 95.7% | [1 Missing :warning: ](https://app.codecov.io/gh/boostorg/multiprecision/pull/701?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/701/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/701?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@            Coverage Diff            @@  
##           develop    #701     +/-   ##  
=========================================  
- Coverage     94.1%   93.9%   -0.1%       
=========================================  
  Files          280     273      -7       
  Lines        29074   28994     -80       
=========================================  
- Hits         27347   27218    -129       
- Misses        1727    1776     +49       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/multiprecision/pull/701?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...oost/multiprecision/cpp\_df\_qf/cpp\_df\_qf\_detail.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/701?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_df_qf%2Fcpp_df_qf_detail.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfZGZfcWYvY3BwX2RmX3FmX2RldGFpbC5ocHA=) | `100.0% <100.0%> (ø)` | |  
| [...ecision/cpp\_df\_qf/cpp\_df\_qf\_detail\_ccmath\_fabs.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/701?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_df_qf%2Fcpp_df_qf_detail_ccmath_fabs.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfZGZfcWYvY3BwX2RmX3FmX2RldGFpbF9jY21hdGhfZmFicy5ocHA=) | `100.0% <100.0%> (ø)` | |  
| [...cision/cpp\_df\_qf/cpp\_df\_qf\_detail\_ccmath\_floor.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/701?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_df_qf%2Fcpp_df_qf_detail_ccmath_floor.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfZGZfcWYvY3BwX2RmX3FmX2RldGFpbF9jY21hdGhfZmxvb3IuaHBw) | `100.0% <100.0%> (ø)` | |  
| [...recision/cpp\_df\_qf/cpp\_df\_qf\_detail\_ccmath\_fma.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/701?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_df_qf%2Fcpp_df_qf_detail_ccmath_fma.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfZGZfcWYvY3BwX2RmX3FmX2RldGFpbF9jY21hdGhfZm1hLmhwcA==) | `100.0% <100.0%> (ø)` | |  
| [...cision/cpp\_df\_qf/cpp\_df\_qf\_detail\_ccmath\_frexp.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/701?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_df_qf%2Fcpp_df_qf_detail_ccmath_frexp.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfZGZfcWYvY3BwX2RmX3FmX2RldGFpbF9jY21hdGhfZnJleHAuaHBw) | `100.0% <100.0%> (ø)` | |  
| [...cision/cpp\_df\_qf/cpp\_df\_qf\_detail\_ccmath\_isinf.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/701?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_df_qf%2Fcpp_df_qf_detail_ccmath_isinf.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfZGZfcWYvY3BwX2RmX3FmX2RldGFpbF9jY21hdGhfaXNpbmYuaHBw) | `100.0% <100.0%> (ø)` | |  
| [...cision/cpp\_df\_qf/cpp\_df\_qf\_detail\_ccmath\_isnan.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/701?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_df_qf%2Fcpp_df_qf_detail_ccmath_isnan.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfZGZfcWYvY3BwX2RmX3FmX2RldGFpbF9jY21hdGhfaXNuYW4uaHBw) | `100.0% <100.0%> (ø)` | |  
| [...cision/cpp\_df\_qf/cpp\_df\_qf\_detail\_ccmath\_ldexp.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/701?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_df_qf%2Fcpp_df_qf_detail_ccmath_ldexp.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfZGZfcWYvY3BwX2RmX3FmX2RldGFpbF9jY21hdGhfbGRleHAuaHBw) | `100.0% <100.0%> (ø)` | |  
| [...recision/cpp\_df\_qf/cpp\_df\_qf\_detail\_ccmath\_log.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/701?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_df_qf%2Fcpp_df_qf_detail_ccmath_log.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfZGZfcWYvY3BwX2RmX3FmX2RldGFpbF9jY21hdGhfbG9nLmhwcA==) | `100.0% <100.0%> (ø)` | |  
| [...ecision/cpp\_df\_qf/cpp\_df\_qf\_detail\_ccmath\_sqrt.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/701?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_df_qf%2Fcpp_df_qf_detail_ccmath_sqrt.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfZGZfcWYvY3BwX2RmX3FmX2RldGFpbF9jY21hdGhfc3FydC5ocHA=) | `100.0% <100.0%> (ø)` | |  
| ... and [29 more](https://app.codecov.io/gh/boostorg/multiprecision/pull/701?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
... and [42 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/multiprecision/pull/701/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/701?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/701?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [6df8e19...d11a72e](https://app.codecov.io/gh/boostorg/multiprecision/pull/701?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2025-06-26 05:21:33 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/701#issuecomment-3007092998  

So in my opinion about 1,000 commits and a few years into it, the `cpp_fp_backend` is ready to merge with this PR.  
  
I'll leave this one open for a day or two if anyone has further comments or notices something significant that might have been missed or neglected.  
  
Cc: @jzmaddock and @mborland and @sinandredemption and @cosurgi

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2025-06-26 06:52:39 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/701#issuecomment-3007371280  

This PR also addresses [BoostGSoC21/multiprecision/issues/160](https://github.com/BoostGSoC21/multiprecision/issues/160) in its entirety.

---

## Comment 4

> Username: mborland  
> Created_at: 2025-06-26 12:17:24 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/701#issuecomment-3008287179  

This also contains all of: https://github.com/boostorg/multiprecision/pull/700. I'll close that one since it has merge conflicts with this

---

## Comment 5

> Username: mborland  
> Created_at: 2025-06-26 17:48:28 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/701#issuecomment-3009307904  

@ckormanyos It wasn't your docs change that blew up the CI. A bunch of modular build changes are getting merged in right now. I would let things settle, and then I'll ping René if things are still bad

---

## Comment 6

> Username: ckormanyos  
> Created_at: 2025-06-26 17:56:04 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/701#issuecomment-3009327379  

> It wasn't your docs change that blew up the CI. A bunch of modular build changes are getting merged in right now.  
  
Yeah I suppose so.  
  
Well, the challenge of the heat is on. I know a big (or huge) evolutionary step is underway. The July release is creeping up... I'll chill for a tad and support when/where i can.  
  
We all want to get some big diffs into 1.89. I'm really happy to see all this going on. It's going to pay off in the future.

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2025-06-26 17:56:13 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/701#issuecomment-3009327721  

Thanks guys for pursuing this, and apologies for being AWOL, just starting to look at this now...

---

## Comment 8

> Username: ckormanyos  
> Created_at: 2025-06-26 17:57:32 UTC  
> Updated_at: 2025-06-26 18:06:22 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/701#issuecomment-3009331220  

> Thanks guys for pursuing this, and apologies for being AWOL, just starting to look at this now...  
  
Thanks John.  
  
If something really big pops up in your perception, then please let us know. I/we were unsure in a lot of areas. From my side, I subjectively _think_ this thing is on the right track.  
  
But I'd sure like your input.

---

## Review 9 [Commented]

> Username: jzmaddock  
> Created_at: 2025-06-26 18:00:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/701#pullrequestreview-2962990946  

📁 test/Simple_Jamfile

```diff
   1 |+ # Copyright 2022 Peter Dimov
```

> Username: jzmaddock  
> Created_at: 2025-06-26 18:00:25 UTC  
> Updated_at: 2025-06-26 18:00:26 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/701#discussion_r2169617091  

Did you intend this file to still be here?

> Username: ckormanyos  
> Created_at: 2025-06-26 18:03:06 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/701#discussion_r2169626349  

Yes. Matt put it in for dedicated Build trouble-shooting.  
  
I can add the updated authors and years.  
  
In fact, it was just a day or two ago when Matt rescured the day on a weird rounding issue. We have not yet fully comprehended it, but made a work around for 64-bit ARMs all over.

> Username: mborland  
> Created_at: 2025-06-26 18:03:41 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/701#discussion_r2169628474  

I don't think it hurts. Any other tests that don't have requirements (e.g. quad math linking) can be added to this file for CMake testing support.


---

## Comment 10

> Username: mborland  
> Created_at: 2025-06-26 18:16:14 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/701#issuecomment-3009405430  

Ok to fix the failures on this line: https://github.com/boostorg/multiprecision/blob/develop/example/Jamfile.v2#L95 replace  
  
```  
[ run hypergeometric_luke_algorithms.cpp no_eh_eg_support ../../chrono/build//boost_chrono ../../system/build//boost_system : : : [ requires cxx11_nullptr ]  ]  
```  
  
With  
  
```  
[ run hypergeometric_luke_algorithms.cpp no_eh_eg_support /boost/chrono//boost_chrono /boost/system//boost_system : : : [ requires cxx11_nullptr ]  ]  
```  
  
It's changing the paths to chrono and system. I don't have write access to the GSoC repo, so I am not allowed to commit onto this PR myself.

---

## Comment 11

> Username: ckormanyos  
> Created_at: 2025-06-26 18:25:13 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/701#issuecomment-3009438531  

> to fix the failures  
  
You meant line 95. Done. Cycling.  
  
> I don't have write access to the GSoC repo  
  
You do now. Admin rights are in your inbox.

---

## Comment 12

> Username: mborland  
> Created_at: 2025-06-26 18:32:34 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/701#issuecomment-3009467917  

> > to fix the failures  
>   
> You meant line 95. Done. Cycling.  
  
Should be just that line. Things seem to be running normal again.  
  
>   
> > I don't have write access to the GSoC repo  
>   
> You do now. Admin rights are in your inbox.  
  
Thanks.

---

## Review 13 [Commented]

> Username: jzmaddock  
> Created_at: 2025-06-26 18:33:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/701#pullrequestreview-2963133938  

📁 test/test_round.cpp

```diff
 189 |+    #endif
 190 |+ 
 191 |+    #if defined(TEST_CPP_DOUBLE_FLOAT)
```

> Username: jzmaddock  
> Created_at: 2025-06-26 18:33:49 UTC  
> Updated_at: 2025-06-26 18:33:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/701#discussion_r2169700963  

My only concern here, is we may have multiple TEST_* macros defined: this doesn't happen when running the Jamfile, but it is how I test locally ;)

> Username: ckormanyos  
> Created_at: 2025-06-26 18:38:06 UTC  
> Updated_at: 2025-06-26 18:38:07 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/701#discussion_r2169707188  

I am aware of this.  
  
We could consider to query if `TEST_CPP_DOUBLE_FLOAT` is defined. And then combine this with the query if _NONE_ of the other ones are defined.  
  
I was hoping you would ask.  
  
Then we (or I) could make a macro that says, hey if it is `TEST_CPP_DOUBLE_FLOAT` only, then do this. Otherwise don't.  
  
But the other day, when looking at some setups, I thought this was already being done. But maybe I got confuesd and it is only for specfun. Let me check.

> Username: ckormanyos  
> Created_at: 2025-06-27 17:54:19 UTC  
> Updated_at: 2025-06-27 17:54:20 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/701#discussion_r2172563891  

Handled in 284547464dfb5d5c859488a144f812b7ab8ead15

> Username: ckormanyos  
> Created_at: 2025-06-27 18:11:28 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/701#discussion_r2172599791  

fixed by 284547464dfb5d5c859488a144f812b7ab8ead15


---

## Review 14 [Commented]

> Username: jzmaddock  
> Created_at: 2025-06-26 18:35:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/701#pullrequestreview-2963136894  

📁 test/test_sf_import_c99.cpp

```diff
 345 |-    BOOST_CHECK_CLOSE_FRACTION(asinh(val + T(0)), T("1.6472311463710957106248586104436196635044144301932365282203100930843983757633104078778420255069424907777006132075516484778755360595913172299093829522950397895699619540523579875476513967578478619028438291006578604823887119907434"), s);
 351 |+    {
 352 |+       #if defined(TEST_CPP_DOUBLE_FLOAT)
```

> Username: jzmaddock  
> Created_at: 2025-06-26 18:35:05 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/701#discussion_r2169702697  

As above we may have multiple TEST_* macros set.

> Username: ckormanyos  
> Created_at: 2025-06-26 18:38:30 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/701#discussion_r2169707728  

I need to get back to you on this.

> Username: ckormanyos  
> Created_at: 2025-06-27 16:30:04 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/701#discussion_r2172405913  

Hi John (@jzmaddock) thanks for the direction on this.  
  
Fixed in 284547464dfb5d5c859488a144f812b7ab8ead15.  
  
I opted for a new file called `test_traits.hpp` under the assumption that this would be a good place to collect (in the future) all of these kinds of things and reduce redundancies.


---

## Review 15 [Commented]

> Username: jzmaddock  
> Created_at: 2025-06-26 18:35:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/701#pullrequestreview-2963137445  

📁 test/test_sf_import_c99.cpp

```diff
 350 |-    BOOST_CHECK_CLOSE_FRACTION(atanh(val + T(0)), T("0.5493061443340548456976226184612628523237452789113747258673471668187471466093044834368078774068660443939850145329789328711840021129652599105264009353836387053015813845916906835896868494221804799518712851583979557605727959588753"), s);
 363 |+    {
 364 |+       #if defined(TEST_CPP_DOUBLE_FLOAT)
```

> Username: jzmaddock  
> Created_at: 2025-06-26 18:35:20 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/701#discussion_r2169703019  

And here...

> Username: ckormanyos  
> Created_at: 2025-06-26 18:40:22 UTC  
> Updated_at: 2025-06-26 18:40:46 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/701#discussion_r2169710761  

Yes it it all over the place. If a solution is pending, we can find it.  
  
I need to look at your existing header-work. I thought you had already solved this problem for testing a single backend, while simultaneously allowing for multiple others to be tested.

> Username: ckormanyos  
> Created_at: 2025-06-26 18:45:21 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/701#discussion_r2169718481  

I/we need to do something in `setup.hpp` for this. I thought it was already done. But now I'm not so sure.

> Username: ckormanyos  
> Created_at: 2025-06-26 18:46:19 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/701#discussion_r2169720362  

Oh. That was only for Math tests.  
  
We might need some more PP work. I'd like to experience what you think.

> Username: ckormanyos  
> Created_at: 2025-06-26 18:48:54 UTC  
> Updated_at: 2025-06-26 18:49:23 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/701#discussion_r2169728817  

I wonder if a line like this is potentially possible?  
  
```  
#if defined (TEST_CPP_DOUBLE_FLOAT) && (!defined(TEST_MPF_50) && !defined(TEST_BACKEND) && !defined(TEST_CPP_DEC_FLOAT) && !defined(TEST_MPFR_50) && !defined(TEST_FLOAT128) && !defined(TEST_CPP_BIN_FLOAT))  
```  
  
Which is an awkward line, to say the least...  
  
Your thoughts?


---

## Comment 16

> Username: jzmaddock  
> Created_at: 2025-06-26 18:56:08 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/701#issuecomment-3009586115  

I think I've always used a traits class, see for example has_poor_large_value_support in test_sin.cpp.

---

## Comment 17

> Username: ckormanyos  
> Created_at: 2025-06-26 19:53:26 UTC  
> Updated_at: 2025-06-26 19:53:44 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/701#issuecomment-3009766737  

> I think I've always used a traits class, see for example has_poor_large_value_support in test_sin.cpp.  
  
Oh that is cool. Far out, I had not thought of that. I could activate tests or not-tests with a traits-class in association with `if-constecpr` or the Boost equivalent thereof.  
  
I think this is the right way to evolve. Let me see if I can use that instead of the PP.

---

## Comment 18

> Username: ckormanyos  
> Created_at: 2025-06-26 20:22:52 UTC  
> Updated_at: 2025-06-26 20:25:23 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/701#issuecomment-3009866365  

OK. So based on some sage and early review comments, we need to enable/disable tests of this new back end in a more refined and specific/exclusive way.  
  
I'll get to work on that and then let this thing settle.  
  
If any one else has some comments, please now is the time to add.  
  
It is not often that we add a new back end to MP. So this is the time for comments. I think we did our homework. But please if you review and notice something awkward, then consider change requests. They will be noted.  
  
Sure, this thing will evolve. But the more we catch prior to the first release, the better.  
  
Thx so far.

---

## Comment 19

> Username: ckormanyos  
> Created_at: 2025-06-27 08:41:12 UTC  
> Updated_at: 2025-06-27 08:41:26 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/701#issuecomment-3012212375  

> I think I've always used a traits class, see for example `has_poor_large_value_support in test_sin.cpp`.  
  
Thanks John. That'll work.  
  
See also [BoostGSoC21/multiprecision/issues/195](https://github.com/BoostGSoC21/multiprecision/issues/195), which is linked to the epic TODO list at [BoostGSoC21/multiprecision/issues/160](https://github.com/BoostGSoC21/multiprecision/issues/160).  
  
Cc: @sinandredemption and @cosurgi

---

## Comment 20

> Username: mborland  
> Created_at: 2025-06-27 15:06:32 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/701#issuecomment-3013397613  

@ckormanyos I'm not entirely sure why, but I am still blocked from pushing onto this branch. You'll want to wrap two of the `numeric_limits` members as so:  
  
```  
  
   // These members were deprecated in C++23, but only MSVC warns (as of June 25)  
   #ifdef _MSC_VER  
   #pragma warning(push)  
   #pragma warning(disable:4996)  
   #endif  
  
   static constexpr float_denorm_style      has_denorm                    = denorm_absent;  
   static constexpr bool                    has_denorm_loss               = true;  
  
   #ifdef _MSC_VER  
   #pragma warning(pop)  
   #endif // deprecated members  
```

---

## Comment 21

> Username: ckormanyos  
> Created_at: 2025-06-27 16:32:07 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/701#issuecomment-3013650837  

> wrap two of the `numeric_limits members`  
  
Thanks Matt (@mborland). Good catch.  
  
Cycling in 6cf9348985e2cd818b1e59874c08e1055cce725f

---

## Comment 22

> Username: ckormanyos  
> Created_at: 2025-06-27 17:52:53 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/701#issuecomment-3013934089  

> I am still blocked from pushing onto this branch.   
  
I don't know where the blocker is. I added you as admin on the repo.  See pic.  
  
Maybe you have to be a member of the Org. But I can't do that?  
  
But this thing will soon be in develop of Multiprecision anyway. So i'm not going to struggle any more for the moment. Sorry it didn't quite work out Matt. If we have any big, unexpected changes, we can try to dig deeper.  
  
![image](https://github.com/user-attachments/assets/7051d2b2-3302-4c71-a021-823ddaffea9d)

---

## Comment 23

> Username: ckormanyos  
> Created_at: 2025-06-27 17:58:03 UTC  
> Updated_at: 2025-06-27 17:58:26 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/701#issuecomment-3013946166  

So folks, this thing is green all over. And I don't have anything else to add at the moment.  
  
It is last call for comments.  
  
Our final outstanding decision is if we try to rush for the 3-July-2025 deadline for 1.89? Or should we take it down a notch and merge rather end of July or beginning of August for 1.90?  
  
Thoughts?  
  
I have a (very) slight 1.89 preference since I'm certain when this thing gets into the wild, we will learn some lessons and evolve it anyway. But I'm a little concerned about the failing _Inspect_ and hope that comes to its senses. (It's not a Multiprecision thing.)  
  
Cc: @jzmaddock and @sinandredemption and @cosurgi and @mborland

---

## Comment 24

> Username: mborland  
> Created_at: 2025-06-27 18:03:35 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/701#issuecomment-3013958068  

> Our final outstanding decision is if we try to rush for the 3-July-2025 deadline for 1.89? Or should we take it down a notch and merge rather end of July or beginning of August for 1.90?  
>   
> Thoughts?  
>   
  
I vote merge it in. Get it in the hands of users and see what happens. There's enough testing and validation that it's a solid product, but there's always corners we'd never think about.

---

## Comment 25

> Username: ckormanyos  
> Created_at: 2025-06-27 18:34:20 UTC  
> Updated_at: 2025-06-27 18:36:11 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/701#issuecomment-3014040161  

> And I don't have anything else to add at the moment.  
  
Upon review, I have come to the opinion that `test_float_io.cpp` was missing tests for `cpp_bin_float` (which is officially _not_ within the scope of this PR).  
  
So I have, nonetheless, taken the liberty to add these. Cycling now.  
  
Cc: @jzmaddock and @mborland

---

## Comment 26

> Username: ckormanyos  
> Created_at: 2025-06-27 20:37:11 UTC  
> Updated_at: 2025-06-27 20:40:36 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/701#issuecomment-3014297511  

OK so we got `cpp_bin_float` tested in `test_float_io.cpp`. I got greedy and, even though this was unrelated to this PR, I sneaked it in.  
  
This thing is good to go.  
  
Cc: @jzmaddock and @mborland and @sinandredemption and @cosurgi

---

## Comment 27

> Username: ckormanyos  
> Created_at: 2025-06-27 21:04:29 UTC  
> Updated_at: 2025-06-27 21:07:12 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/701#issuecomment-3014350972  

This was a big one.  
  
Please @sinandredemption accept my gratitude for your foudational work in GSoC 21. And also @cosurgi thank you for co-mentoring this.  
  
I feel that we helped each other and only in team could we get this one.  
  
@jzmaddock thank you for coaching and guiding us.  
  
And thanks @mborland for finishing this one of with the perfect CI, coverage and quality label. Thereby making this thing good.  
  
Out for now. Looking forward to QUAD-Double-Trouble.  
  
Closing and deleting branch.

---

## Comment 28

> Username: cosurgi  
> Created_at: 2025-06-27 21:36:12 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/701#issuecomment-3014400377  

This is great! Thank you very much! :)

---

## Comment 29

> Username: jzmaddock  
> Created_at: 2025-06-28 16:04:28 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/701#issuecomment-3015611099  

Well done everybody, and especially @ckormanyos for keeping this moving forwards!!

---

## Comment 30

> Username: ckormanyos  
> Created_at: 2025-06-28 17:36:56 UTC  
> Updated_at: 2025-06-28 17:37:28 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/701#issuecomment-3015881708  

> Well done everybody  
  
Thank you John (@jzmaddock), for helping every step of the way.  
  
I was really impressed with the tests we have in Multiprecison and the quality we have. You sure did a lot to get us there.  
  
This new backend is an interesting one. It's not precise. It _absolutely_ is fast within its modest digit range. Yet it might behave in subtle, diffferent ways than some clients expect from a FP-backend. I'm interested to see how this plays out in the client domain.

---
