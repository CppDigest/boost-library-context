# #952 feat: decode free functions [Merged]

> Username: alandefreitas  
> Created at: 2025-11-12 22:27:46 UTC  
> Updated at: 2026-01-19 21:00:57 UTC  
> Merged at: 2026-01-19 21:00:56 UTC  
> Closed at: 2026-01-19 21:00:57 UTC  
> Url: https://github.com/boostorg/url/pull/952  

fix #947

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2025-11-12 22:33:47 UTC  
> Updated_at: 2026-01-19 19:16:13 UTC  
> Url: https://github.com/boostorg/url/pull/952#issuecomment-3524185781  

An automated preview of the documentation is available at [https://952.url.prtest2.cppalliance.org/index.html](https://952.url.prtest2.cppalliance.org/index.html)  
  
If more commits are pushed to the pull request, the docs will rebuild at the same URL.  
  
2026-01-19 19:16:12 UTC

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2025-11-12 22:41:37 UTC  
> Url: https://github.com/boostorg/url/pull/952#issuecomment-3524205152  

you might have pulled the trigger a little too quickly on this. look at how beast2 is using percent-decoding:  
  
https://github.com/cppalliance/beast2/blob/daf37da49b4fdf3fcc66d4d6e920205717c86bd2/src/server/detail/any_router.cpp#L42  
  
(I plan on refactoring to StringToken to re-use strings)

---

## Comment 3

> Username: alandefreitas  
> Created_at: 2025-11-12 22:44:08 UTC  
> Updated_at: 2025-11-12 22:44:41 UTC  
> Url: https://github.com/boostorg/url/pull/952#issuecomment-3524210752  

> I plan on refactoring to StringToken to re-use strings  
  
Don't we already have a string token for that?  
  
https://952.url.prtest2.cppalliance.org/url/reference/boost/urls/string_token/assign_to.html

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2025-11-12 22:45:53 UTC  
> Url: https://github.com/boostorg/url/pull/952#issuecomment-3524214866  

GCOVR code coverage report [https://952.url.prtest2.cppalliance.org/gcovr/index.html](https://952.url.prtest2.cppalliance.org/gcovr/index.html)  
LCOV code coverage report [https://952.url.prtest2.cppalliance.org/genhtml/index.html](https://952.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://952.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://952.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)  
  
Build time: 2025-11-12 22:45:52 UTC

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2025-11-12 22:57:16 UTC  
> Updated_at: 2026-01-19 19:21:42 UTC  
> Url: https://github.com/boostorg/url/pull/952#issuecomment-3524241475  

GCOVR code coverage report [https://952.url.prtest2.cppalliance.org/gcovr/index.html](https://952.url.prtest2.cppalliance.org/gcovr/index.html)  
LCOV code coverage report [https://952.url.prtest2.cppalliance.org/genhtml/index.html](https://952.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://952.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://952.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)  
  
Build time: 2026-01-19 19:21:41 UTC

---

## Comment 6

> Username: codecov[bot]  
> Created_at: 2025-11-12 22:57:28 UTC  
> Updated_at: 2025-11-13 04:53:20 UTC  
> Url: https://github.com/boostorg/url/pull/952#issuecomment-3524241862  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/952?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 99.21%. Comparing base ([`bbe9700`](https://app.codecov.io/gh/boostorg/url/commit/bbe9700fedb4897e46bc14a64de4088543054b7c?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`24937d3`](https://app.codecov.io/gh/boostorg/url/commit/24937d35d35e2495087e7a91174ecd97edf4a75b?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/952/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/952?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #952   +/-   ##  
========================================  
  Coverage    99.21%   99.21%             
========================================  
  Files          159      160    +1       
  Lines         8684     8708   +24       
========================================  
+ Hits          8616     8640   +24       
  Misses          68       68             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/url/pull/952?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/impl/decode.hpp](https://app.codecov.io/gh/boostorg/url/pull/952?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Fimpl%2Fdecode.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9kZWNvZGUuaHBw) | `100.00% <100.00%> (ø)` | |  
| [src/detail/decode.cpp](https://app.codecov.io/gh/boostorg/url/pull/952?src=pr&el=tree&filepath=src%2Fdetail%2Fdecode.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2RldGFpbC9kZWNvZGUuY3Bw) | `100.00% <ø> (ø)` | |  
| [src/detail/normalize.cpp](https://app.codecov.io/gh/boostorg/url/pull/952?src=pr&el=tree&filepath=src%2Fdetail%2Fnormalize.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2RldGFpbC9ub3JtYWxpemUuY3Bw) | `99.30% <ø> (ø)` | |  
| [src/detail/segments\_iter\_impl.cpp](https://app.codecov.io/gh/boostorg/url/pull/952?src=pr&el=tree&filepath=src%2Fdetail%2Fsegments_iter_impl.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2RldGFpbC9zZWdtZW50c19pdGVyX2ltcGwuY3Bw) | `99.19% <ø> (ø)` | |  
| [src/pct\_string\_view.cpp](https://app.codecov.io/gh/boostorg/url/pull/952?src=pr&el=tree&filepath=src%2Fpct_string_view.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3BjdF9zdHJpbmdfdmlldy5jcHA=) | `100.00% <ø> (ø)` | |  
| [src/url\_base.cpp](https://app.codecov.io/gh/boostorg/url/pull/952?src=pr&el=tree&filepath=src%2Furl_base.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3VybF9iYXNlLmNwcA==) | `99.66% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/952?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/952?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [bbe9700...24937d3](https://app.codecov.io/gh/boostorg/url/pull/952?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 7

> Username: vinniefalco  
> Created_at: 2025-11-12 23:05:26 UTC  
> Url: https://github.com/boostorg/url/pull/952#issuecomment-3524260318  

> Don't we already have a string token for that?  
  
I mean that I will change my code to use a StringToken api

---
