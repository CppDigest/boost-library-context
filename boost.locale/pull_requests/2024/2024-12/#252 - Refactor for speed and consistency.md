# #252 Refactor for speed and consistency [Merged]

> Username: Flamefire  
> Created at: 2024-12-31 12:45:17 UTC  
> Updated at: 2025-01-12 16:22:04 UTC  
> Merged at: 2025-01-11 11:13:48 UTC  
> Closed at: 2025-01-11 11:13:48 UTC  
> Url: https://github.com/boostorg/locale/pull/252  

1. Consistently pass string_view by value as is best practice with it being a pointer-size pair  
2. Move `try_to_int` to new header moving it out of a header containing the large `base_num_format`.  
3. Use Boost.Charconv in `try_to_int`  
4. Allow `try_to_int` to be used with other integer types, e.g. the `unsigned` as used with in one other place  
5. Avoid superflous copies of the time zone string: First into a local variable, then into a `vector` to have it non-const. The latter is only required for FreeBSD and apple, not for all Linux systems.  
6. Test only: Return the success state in `TEST` macro to allow depending on the return value for further tests

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-01-02 02:00:53 UTC  
> Updated_at: 2025-01-11 11:13:55 UTC  
> Url: https://github.com/boostorg/locale/pull/252#issuecomment-2567223715  

## [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/252?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 95.87%. Comparing base [(`5a58e65`)](https://app.codecov.io/gh/boostorg/locale/commit/5a58e65bdacd48578206de7ef5b90d6cf61f0449?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`e540a63`)](https://app.codecov.io/gh/boostorg/locale/commit/e540a63c0b3f6fd28e8ebb1389b19dbde65ea712?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 11 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/locale/pull/252/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/locale/pull/252?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #252   +/-   ##  
========================================  
  Coverage    95.87%   95.87%             
========================================  
  Files          117      119    +2       
  Lines        10406    10410    +4       
========================================  
+ Hits          9977     9981    +4       
  Misses         429      429             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/locale/pull/252?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/locale/boundary/boundary\_point.hpp](https://app.codecov.io/gh/boostorg/locale/pull/252?src=pr&el=tree&filepath=include%2Fboost%2Flocale%2Fboundary%2Fboundary_point.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvYm91bmRhcnkvYm91bmRhcnlfcG9pbnQuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/locale/boundary/index.hpp](https://app.codecov.io/gh/boostorg/locale/pull/252?src=pr&el=tree&filepath=include%2Fboost%2Flocale%2Fboundary%2Findex.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvYm91bmRhcnkvaW5kZXguaHBw) | `98.77% <ø> (ø)` | |  
| [include/boost/locale/boundary/segment.hpp](https://app.codecov.io/gh/boostorg/locale/pull/252?src=pr&el=tree&filepath=include%2Fboost%2Flocale%2Fboundary%2Fsegment.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvYm91bmRhcnkvc2VnbWVudC5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/locale/detail/any\_string.hpp](https://app.codecov.io/gh/boostorg/locale/pull/252?src=pr&el=tree&filepath=include%2Fboost%2Flocale%2Fdetail%2Fany_string.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZGV0YWlsL2FueV9zdHJpbmcuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/locale/detail/encoding.hpp](https://app.codecov.io/gh/boostorg/locale/pull/252?src=pr&el=tree&filepath=include%2Fboost%2Flocale%2Fdetail%2Fencoding.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZGV0YWlsL2VuY29kaW5nLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/locale/encoding.hpp](https://app.codecov.io/gh/boostorg/locale/pull/252?src=pr&el=tree&filepath=include%2Fboost%2Flocale%2Fencoding.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZW5jb2RpbmcuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/locale/format.hpp](https://app.codecov.io/gh/boostorg/locale/pull/252?src=pr&el=tree&filepath=include%2Fboost%2Flocale%2Fformat.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZm9ybWF0LmhwcA==) | `97.82% <ø> (ø)` | |  
| [include/boost/locale/formatting.hpp](https://app.codecov.io/gh/boostorg/locale/pull/252?src=pr&el=tree&filepath=include%2Fboost%2Flocale%2Fformatting.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZm9ybWF0dGluZy5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/locale/message.hpp](https://app.codecov.io/gh/boostorg/locale/pull/252?src=pr&el=tree&filepath=include%2Fboost%2Flocale%2Fmessage.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvbWVzc2FnZS5ocHA=) | `100.00% <ø> (ø)` | |  
| [src/encoding/codepage.cpp](https://app.codecov.io/gh/boostorg/locale/pull/252?src=pr&el=tree&filepath=src%2Fencoding%2Fcodepage.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2VuY29kaW5nL2NvZGVwYWdlLmNwcA==) | `100.00% <ø> (ø)` | |  
| ... and [26 more](https://app.codecov.io/gh/boostorg/locale/pull/252?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/locale/pull/252?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/252?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [5a58e65...e540a63](https://app.codecov.io/gh/boostorg/locale/pull/252?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
