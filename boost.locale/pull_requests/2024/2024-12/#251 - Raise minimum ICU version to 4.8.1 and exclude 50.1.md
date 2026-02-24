# #251 Raise minimum ICU version to 4.8.1 and exclude 50.1 [Merged]

> Username: Flamefire  
> Created at: 2024-12-26 13:42:57 UTC  
> Updated at: 2024-12-28 12:50:43 UTC  
> Merged at: 2024-12-28 03:10:05 UTC  
> Closed at: 2024-12-28 03:10:05 UTC  
> Url: https://github.com/boostorg/locale/pull/251  

[](https://github.com/boostorg/locale/commit/f49fc76d88021cceb863c39af03fe38c750d2d58)  
  
A lot of checks and a major workaround are for ICU 4.8 or earlier which can be removed.  
  
An annoying bug (Parsing of timezones like "GMT" in "full" format followed by unrelated text) fixed in 4.8.1 is worth avoiding by requiring this version over 4.8.0.  
  
50.1(.0) has a regression so is explicitly excluded.  
  
Note that even the EoL Ubuntu 14.04 has ICU 52.1.3 available.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-12-27 00:40:41 UTC  
> Updated_at: 2024-12-27 01:02:16 UTC  
> Url: https://github.com/boostorg/locale/pull/251#issuecomment-2563197826  

## [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/251?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 95.83%. Comparing base [(`087dfb6`)](https://app.codecov.io/gh/boostorg/locale/commit/087dfb61d619d0f5b3faddd9eb1e06dbec6ad319?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`5e71820`)](https://app.codecov.io/gh/boostorg/locale/commit/5e7182094625cadaffa2f8234ca6728c3667032a?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/locale/pull/251/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/locale/pull/251?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #251      +/-   ##  
===========================================  
- Coverage    95.84%   95.83%   -0.01%       
===========================================  
  Files          117      117                
  Lines        10410    10406       -4       
===========================================  
- Hits          9977     9973       -4       
  Misses         433      433                
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/locale/pull/251?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [src/boost/locale/icu/collator.cpp](https://app.codecov.io/gh/boostorg/locale/pull/251?src=pr&el=tree&filepath=src%2Fboost%2Flocale%2Ficu%2Fcollator.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvY29sbGF0b3IuY3Bw) | `94.20% <ø> (ø)` | |  
| [src/boost/locale/icu/conversion.cpp](https://app.codecov.io/gh/boostorg/locale/pull/251?src=pr&el=tree&filepath=src%2Fboost%2Flocale%2Ficu%2Fconversion.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvY29udmVyc2lvbi5jcHA=) | `93.33% <ø> (ø)` | |  
| [src/boost/locale/icu/date\_time.cpp](https://app.codecov.io/gh/boostorg/locale/pull/251?src=pr&el=tree&filepath=src%2Fboost%2Flocale%2Ficu%2Fdate_time.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvZGF0ZV90aW1lLmNwcA==) | `92.85% <100.00%> (-0.28%)` | :arrow_down: |  
| [src/boost/locale/icu/formatters\_cache.cpp](https://app.codecov.io/gh/boostorg/locale/pull/251?src=pr&el=tree&filepath=src%2Fboost%2Flocale%2Ficu%2Fformatters_cache.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvZm9ybWF0dGVyc19jYWNoZS5jcHA=) | `100.00% <ø> (ø)` | |  
| [src/boost/locale/icu/time\_zone.hpp](https://app.codecov.io/gh/boostorg/locale/pull/251?src=pr&el=tree&filepath=src%2Fboost%2Flocale%2Ficu%2Ftime_zone.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvdGltZV96b25lLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [test/formatting\_common.hpp](https://app.codecov.io/gh/boostorg/locale/pull/251?src=pr&el=tree&filepath=test%2Fformatting_common.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9mb3JtYXR0aW5nX2NvbW1vbi5ocHA=) | `100.00% <ø> (ø)` | |  
| [test/test\_date\_time.cpp](https://app.codecov.io/gh/boostorg/locale/pull/251?src=pr&el=tree&filepath=test%2Ftest_date_time.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2RhdGVfdGltZS5jcHA=) | `100.00% <ø> (ø)` | |  
| [test/test\_formatting.cpp](https://app.codecov.io/gh/boostorg/locale/pull/251?src=pr&el=tree&filepath=test%2Ftest_formatting.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2Zvcm1hdHRpbmcuY3Bw) | `100.00% <100.00%> (ø)` | |  
| [test/test\_generator.cpp](https://app.codecov.io/gh/boostorg/locale/pull/251?src=pr&el=tree&filepath=test%2Ftest_generator.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2dlbmVyYXRvci5jcHA=) | `99.61% <100.00%> (+<0.01%)` | :arrow_up: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/locale/pull/251?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/251?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [087dfb6...5e71820](https://app.codecov.io/gh/boostorg/locale/pull/251?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
