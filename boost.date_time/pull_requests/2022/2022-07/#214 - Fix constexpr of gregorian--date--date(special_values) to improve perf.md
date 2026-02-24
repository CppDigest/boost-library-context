# #214 Fix constexpr of gregorian::date::date(special_values) to improve perf [Merged]

> Username: p12tic  
> Created at: 2022-07-31 12:29:55 UTC  
> Updated at: 2025-08-15 04:43:04 UTC  
> Merged at: 2022-08-01 10:34:36 UTC  
> Closed at: 2022-08-01 10:34:36 UTC  
> Url: https://github.com/boostorg/date_time/pull/214  

Fixes #121   
  
Turns out even when all functions without the library are prefixed with constexpr, GCC up to at least v10 does not consider this constructor as constexpr at least when generating code.  
```  
BOOST_CXX14_CONSTEXPR explicit date(special_values sv):  
      date_time::date<date, gregorian_calendar, date_duration>(date_rep_type::from_special(sv))  
    {  
      if (sv == min_date_time)  
      {  
        *this = date(1400, 1, 1);  
      }  
      if (sv == max_date_time)  
      {  
        *this = date(9999, 12, 31);  
      }  
    }  
```  
  
The culprit is the assignment to `*this`. Refactoring the code to initialize the instance just once improves code generation significantly. Note that clang since at least v11 does not exhibit this problem.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-08-01 03:33:25 UTC  
> Url: https://github.com/boostorg/date_time/pull/214#issuecomment-1200656005  

# [Codecov](https://codecov.io/gh/boostorg/date_time/pull/214?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#214](https://codecov.io/gh/boostorg/date_time/pull/214?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (a8478d4) into [develop](https://codecov.io/gh/boostorg/date_time/commit/f972dc96567777642652a18e802a9e94cf225b75?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (f972dc9) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/date_time/pull/214/graphs/tree.svg?width=650&height=150&src=pr&token=nDoh7t8f6g&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/date_time/pull/214?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #214   +/-   ##  
========================================  
  Coverage    91.74%   91.75%             
========================================  
  Files           77       77             
  Lines         4823     4827    +4       
========================================  
+ Hits          4425     4429    +4       
  Misses         398      398             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/date_time/pull/214?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/date\_time/gregorian/greg\_date.hpp](https://codecov.io/gh/boostorg/date_time/pull/214/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvZ3JlZ29yaWFuL2dyZWdfZGF0ZS5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [...boost/date\_time/posix\_time/posix\_time\_duration.hpp](https://codecov.io/gh/boostorg/date_time/pull/214/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvcG9zaXhfdGltZS9wb3NpeF90aW1lX2R1cmF0aW9uLmhwcA==) | `100.00% <0.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/date_time/pull/214?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/date_time/pull/214?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [f972dc9...a8478d4](https://codecov.io/gh/boostorg/date_time/pull/214?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 2

> Username: JeffGarland  
> Created_at: 2022-08-01 10:34:49 UTC  
> Url: https://github.com/boostorg/date_time/pull/214#issuecomment-1201022089  

Looks good - thanks!

---
