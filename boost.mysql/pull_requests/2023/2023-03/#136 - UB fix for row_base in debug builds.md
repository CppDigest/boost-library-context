# #136 UB fix for row_base in debug builds [Merged]

> Username: anarthal  
> Created at: 2023-03-30 21:46:53 UTC  
> Updated at: 2023-04-01 09:05:30 UTC  
> Merged at: 2023-04-01 09:05:23 UTC  
> Closed at: 2023-04-01 09:05:23 UTC  
> Url: https://github.com/boostorg/mysql/pull/136  

Replaced unspecified behavior for pointer comparisons  
    with function objects with guaranteed behavior.  
Close #135

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-03-30 22:16:27 UTC  
> Updated_at: 2023-03-30 22:22:04 UTC  
> Url: https://github.com/boostorg/mysql/pull/136#issuecomment-1491038624  

## [Codecov](https://codecov.io/gh/boostorg/mysql/pull/136?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Patch coverage: **`100.00`**% and no project coverage change.  
> Comparison is base [(`643e39f`)](https://codecov.io/gh/boostorg/mysql/commit/643e39f85e6a577336b0e581d39f17a4254475bf?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 97.00% compared to head [(`66b4605`)](https://codecov.io/gh/boostorg/mysql/pull/136?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 97.00%.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
```diff  
@@           Coverage Diff           @@  
##           master     #136   +/-   ##  
=======================================  
  Coverage   97.00%   97.00%             
=======================================  
  Files         101      101             
  Lines        4580     4580             
=======================================  
  Hits         4443     4443             
  Misses        137      137             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/mysql/pull/136?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...lude/boost/mysql/detail/auxiliar/impl/row\_base.ipp](https://codecov.io/gh/boostorg/mysql/pull/136?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kZXRhaWwvYXV4aWxpYXIvaW1wbC9yb3dfYmFzZS5pcHA=) | `100.00% <100.00%> (ø)` | |  
  
Help us with your feedback. Take ten seconds to tell us [how you rate us](https://about.codecov.io/nps?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Have a feature suggestion? [Share it here.](https://app.codecov.io/gh/feedback/?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
</details>  
  
[:umbrella: View full report in Codecov by Sentry](https://codecov.io/gh/boostorg/mysql/pull/136?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).     
:loudspeaker: Do you have feedback about the report comment? [Let us know in this issue](https://about.codecov.io/codecov-pr-comment-feedback/?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---
