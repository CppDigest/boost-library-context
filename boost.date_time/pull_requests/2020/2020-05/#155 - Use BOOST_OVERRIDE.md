# #155 Use BOOST_OVERRIDE [Merged]

> Username: EugeneZelenko  
> Created at: 2020-05-23 02:57:09 UTC  
> Updated at: 2020-05-23 14:27:17 UTC  
> Merged at: 2020-05-23 14:20:00 UTC  
> Closed at: 2020-05-23 14:20:00 UTC  
> Url: https://github.com/boostorg/date_time/pull/155  

Use BOOST_OVERRIDE to fix GCC -Wsuggest-override and Clang-tidy modernize-use-override warnings.  
Also fix Clang -Wextra-semi-stmt and Clang-tidy readability-container-size-empty warnings.  
Alphabetical order of STL headers.  
Fix some misspellings.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-05-23 13:53:27 UTC  
> Updated_at: 2020-05-23 13:55:10 UTC  
> Url: https://github.com/boostorg/date_time/pull/155#issuecomment-633058179  

# [Codecov](https://codecov.io/gh/boostorg/date_time/pull/155?src=pr&el=h1) Report  
> Merging [#155](https://codecov.io/gh/boostorg/date_time/pull/155?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/date_time/commit/d77ee59bc21fcb5a605e3e99095df1dc12ea76c5&el=desc) will **not change** coverage.  
> The diff coverage is `68.75%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/date_time/pull/155/graphs/tree.svg?width=650&height=150&src=pr&token=nDoh7t8f6g)](https://codecov.io/gh/boostorg/date_time/pull/155?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #155   +/-   ##  
========================================  
  Coverage    52.72%   52.72%             
========================================  
  Files           76       76             
  Lines         4491     4491             
  Branches      2243     2243             
========================================  
  Hits          2368     2368             
  Misses         391      391             
  Partials      1732     1732             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/date_time/pull/155?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/date\_time/date\_facet.hpp](https://codecov.io/gh/boostorg/date_time/pull/155/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvZGF0ZV9mYWNldC5ocHA=) | `73.33% <0.00%> (ø)` | |  
| [include/boost/date\_time/gregorian\_calendar.ipp](https://codecov.io/gh/boostorg/date_time/pull/155/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvZ3JlZ29yaWFuX2NhbGVuZGFyLmlwcA==) | `90.66% <ø> (ø)` | |  
| [include/boost/date\_time/time\_parsing.hpp](https://codecov.io/gh/boostorg/date_time/pull/155/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvdGltZV9wYXJzaW5nLmhwcA==) | `51.81% <ø> (ø)` | |  
| [include/boost/date\_time/time\_facet.hpp](https://codecov.io/gh/boostorg/date_time/pull/155/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvdGltZV9mYWNldC5ocHA=) | `35.60% <75.00%> (ø)` | |  
| [include/boost/date\_time/date\_generators.hpp](https://codecov.io/gh/boostorg/date_time/pull/155/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvZGF0ZV9nZW5lcmF0b3JzLmhwcA==) | `49.31% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/date_time/pull/155?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/date_time/pull/155?src=pr&el=footer). Last update [d77ee59...290cc8d](https://codecov.io/gh/boostorg/date_time/pull/155?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: JeffGarland  
> Created_at: 2020-05-23 14:19:55 UTC  
> Url: https://github.com/boostorg/date_time/pull/155#issuecomment-633062986  

Looks good - thank you for your contribution!

---

## Comment 3

> Username: EugeneZelenko  
> Created_at: 2020-05-23 14:27:17 UTC  
> Url: https://github.com/boostorg/date_time/pull/155#issuecomment-633064397  

Thank you for help!

---
