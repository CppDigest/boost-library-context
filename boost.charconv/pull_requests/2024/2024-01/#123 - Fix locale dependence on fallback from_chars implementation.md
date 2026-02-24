# #123 Fix locale dependence on fallback from_chars implementation  [Merged]

> Username: mborland  
> Created at: 2024-01-18 08:42:30 UTC  
> Updated at: 2024-01-22 09:34:29 UTC  
> Merged at: 2024-01-22 09:34:26 UTC  
> Closed at: 2024-01-22 09:34:26 UTC  
> Url: https://github.com/boostorg/charconv/pull/123  

Closes: #122

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-01-18 11:25:32 UTC  
> Updated_at: 2024-01-19 10:06:09 UTC  
> Url: https://github.com/boostorg/charconv/pull/123#issuecomment-1898296942  

## [Codecov](https://app.codecov.io/gh/cppalliance/charconv/pull/123?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) Report  
Attention: `3 lines` in your changes are missing coverage. Please review.  
> Comparison is base [(`0e9dbf2`)](https://app.codecov.io/gh/cppalliance/charconv/commit/0e9dbf273d215ca717ae98f36e9c15d4f88934c2?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) 88.25% compared to head [(`d7b108f`)](https://app.codecov.io/gh/cppalliance/charconv/pull/123?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) 88.29%.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/cppalliance/charconv/pull/123/graphs/tree.svg?width=650&height=150&src=pr&token=7B68uj8tl4&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)](https://app.codecov.io/gh/cppalliance/charconv/pull/123?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #123      +/-   ##  
===========================================  
+ Coverage    88.25%   88.29%   +0.03%       
===========================================  
  Files           55       56       +1       
  Lines         7893     7927      +34       
===========================================  
+ Hits          6966     6999      +33       
- Misses         927      928       +1       
```  
  
  
| [Files](https://app.codecov.io/gh/cppalliance/charconv/pull/123?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) | Coverage Δ | |  
|---|---|---|  
| [...lude/boost/charconv/detail/dragonbox/dragonbox.hpp](https://app.codecov.io/gh/cppalliance/charconv/pull/123?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvZHJhZ29uYm94L2RyYWdvbmJveC5ocHA=) | `97.28% <100.00%> (ø)` | |  
| [include/boost/charconv/to\_chars.hpp](https://app.codecov.io/gh/cppalliance/charconv/pull/123?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi90b19jaGFycy5ocHA=) | `90.30% <100.00%> (ø)` | |  
| [test/github\_issue\_122.cpp](https://app.codecov.io/gh/cppalliance/charconv/pull/123?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC9naXRodWJfaXNzdWVfMTIyLmNwcA==) | `100.00% <100.00%> (ø)` | |  
| [...de/boost/charconv/detail/from\_chars\_float\_impl.hpp](https://app.codecov.io/gh/cppalliance/charconv/pull/123?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvZnJvbV9jaGFyc19mbG9hdF9pbXBsLmhwcA==) | `85.88% <82.35%> (+1.59%)` | :arrow_up: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/cppalliance/charconv/pull/123?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/cppalliance/charconv/pull/123?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Last update [0e9dbf2...d7b108f](https://app.codecov.io/gh/cppalliance/charconv/pull/123?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
  
</details>

---

## Review 2 [Changes requested]

> Username: Flamefire  
> Created_at: 2024-01-18 12:05:59 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/charconv/pull/123#pullrequestreview-1829528770  

📁 include/boost/charconv/detail/from_chars_float_impl.hpp

```diff
  83 |+         return {first, std::errc::not_enough_memory}; // LCOV_EXCL_LINE
  84 |+     }
  85 |+     BOOST_CHARCONV_USE_LOCALE(c_locale);
```

> Username: Flamefire  
> Created_at: 2024-01-18 12:04:35 UTC  
> Updated_at: 2024-01-18 12:06:00 UTC  
> Url: https://github.com/boostorg/charconv/pull/123#discussion_r1457347281  

The locale is never reset so this changes the users locale, doesn't it? I'd suggest to use an RAII class instead such that the reset cannot be forgotten. Also not sure what happens if you call `freelocale` on a locale that is in use (i.e. after setlocale/uselocale), I guess that is UB/use-after-free  
  
Using an RAII object has the additional advantage of resulting in less changes as you can keep the "return"s

> Username: mborland  
> Created_at: 2024-01-19 07:18:31 UTC  
> Url: https://github.com/boostorg/charconv/pull/123#discussion_r1458486038  

If I modify to `const auto prev_locale = BOOST_CHARCONV_USE_LOCALE(c_locale);` the [man pages](https://man7.org/linux/man-pages/man3/uselocale.3.html) say it is to return the current locale. Calling `BOOST_CHARCONV_USE_LOCALE(prev_locale)` before freeing the newly allocated locale causes the test to fail as if none of this PR was present. Any insight on to why that may be?

> Username: mborland  
> Created_at: 2024-01-19 08:42:45 UTC  
> Updated_at: 2024-01-19 08:42:46 UTC  
> Url: https://github.com/boostorg/charconv/pull/123#discussion_r1458585547  

The answer is the conversions were incorrect but matched because the C locale was never reverted back. Added a known validation value to the tests.


---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2024-01-19 09:51:06 UTC  
> Url: https://github.com/boostorg/charconv/pull/123#issuecomment-1900086036  

An automated preview of the documentation is available at [https://123.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://123.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---
