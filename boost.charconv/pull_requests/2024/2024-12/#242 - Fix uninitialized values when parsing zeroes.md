# #242 Fix uninitialized values when parsing zeroes  [Merged]

> Username: Flamefire  
> Created at: 2024-12-28 12:50:23 UTC  
> Updated at: 2025-01-03 08:40:03 UTC  
> Merged at: 2025-01-02 21:10:36 UTC  
> Closed at: 2025-01-02 21:10:36 UTC  
> Url: https://github.com/boostorg/charconv/pull/242  

`00[..].e0` will not initialize the significant reference argument.  
  
`00[..]0.[0...]` will not initialize exponent nor significant.  
  
Requires  
- [x] https://github.com/boostorg/charconv/pull/243

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-12-28 13:51:36 UTC  
> Updated_at: 2025-01-02 21:11:01 UTC  
> Url: https://github.com/boostorg/charconv/pull/242#issuecomment-2564330479  

## [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/242?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: Patch coverage is `97.22222%` with `1 line` in your changes missing coverage. Please review.  
> Project coverage is 94.85%. Comparing base [(`9846df1`)](https://app.codecov.io/gh/boostorg/charconv/commit/9846df15986e4288d3677269d3a506d2808b115d?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`685d4b1`)](https://app.codecov.io/gh/boostorg/charconv/commit/685d4b1bfceeffc7adf80eaebcdbfe28557a3544?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 6 commits behind head on develop.  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/charconv/pull/242?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Patch % | Lines |  
|---|---|---|  
| [include/boost/charconv/detail/parser.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/242?src=pr&el=tree&filepath=include%2Fboost%2Fcharconv%2Fdetail%2Fparser.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvcGFyc2VyLmhwcA==) | 90.90% | [1 Missing :warning: ](https://app.codecov.io/gh/boostorg/charconv/pull/242?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/charconv/pull/242/graphs/tree.svg?width=650&height=150&src=pr&token=jTnxtbJoTv&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/charconv/pull/242?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #242      +/-   ##  
===========================================  
+ Coverage    94.77%   94.85%   +0.08%       
===========================================  
  Files           69       69                
  Lines         9050     9085      +35       
===========================================  
+ Hits          8577     8618      +41       
+ Misses         473      467       -6       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/charconv/pull/242?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [test/test\_parser.cpp](https://app.codecov.io/gh/boostorg/charconv/pull/242?src=pr&el=tree&filepath=test%2Ftest_parser.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3BhcnNlci5jcHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/charconv/detail/parser.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/242?src=pr&el=tree&filepath=include%2Fboost%2Fcharconv%2Fdetail%2Fparser.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvcGFyc2VyLmhwcA==) | `83.66% <90.90%> (+3.97%)` | :arrow_up: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/charconv/pull/242?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/242?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [9846df1...685d4b1](https://app.codecov.io/gh/boostorg/charconv/pull/242?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: Flamefire  
> Created_at: 2025-01-02 15:20:00 UTC  
> Url: https://github.com/boostorg/charconv/pull/242#issuecomment-2567936545  

BTW: During the rebase I figured it would be easier to (re)use `val` and `r` instead of numbering them. This also eliminates the potential for a C&P error especially in `valX, strlen(valX)` param pair. And/Or `parser` could accept a string_view.

---

## Review 3 [Commented]

> Username: mborland  
> Created_at: 2025-01-02 15:27:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/charconv/pull/242#pullrequestreview-2527764240  

📁 include/boost/charconv/detail/parser.hpp

```diff
  73 |+     auto result = boost::charconv::detail::from_chars(first, last, tmp_value, base);
  74 |+     if (result) {
  75 |+         if (tmp_value > std::numeric_limits<Unsigned_Integer>::max())
```

> Username: mborland  
> Created_at: 2025-01-02 15:27:11 UTC  
> Updated_at: 2025-01-02 15:27:12 UTC  
> Url: https://github.com/boostorg/charconv/pull/242#discussion_r1900989067  

```diff  
-        if (tmp_value > std::numeric_limits<Unsigned_Integer>::max())  
+        if (tmp_value > (std::numeric_limits<Unsigned_Integer>::max)())  
```


---

## Comment 4

> Username: mborland  
> Created_at: 2025-01-02 15:28:31 UTC  
> Url: https://github.com/boostorg/charconv/pull/242#issuecomment-2567949690  

Other than that one comment to make windows platforms happy this looks good to me.

---

## Comment 5

> Username: Flamefire  
> Created_at: 2025-01-02 15:44:35 UTC  
> Url: https://github.com/boostorg/charconv/pull/242#issuecomment-2567974688  

> Other than that one comment to make windows platforms happy this looks good to me.  
  
Fixed. A quick regex search shows a few similar issues, e.g. in `fallback_routines.hpp`

---

## Comment 6

> Username: mborland  
> Created_at: 2025-01-02 15:54:57 UTC  
> Url: https://github.com/boostorg/charconv/pull/242#issuecomment-2567992699  

> > Other than that one comment to make windows platforms happy this looks good to me.  
>   
> Fixed. A quick regex search shows a few similar issues, e.g. in `fallback_routines.hpp`  
  
Opened #251

---
