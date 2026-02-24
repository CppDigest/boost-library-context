# #492 Drop support below 1 75 [Merged]

> Username: grisumbras  
> Created at: 2021-01-26 07:39:03 UTC  
> Updated at: 2021-03-10 11:30:15 UTC  
> Merged at: 2021-01-28 10:54:40 UTC  
> Closed at: 2021-01-28 10:54:40 UTC  
> Url: https://github.com/boostorg/json/pull/492  

Fixes #490   
  
Replaces checks of Boost version with checks for whether the library is used standalone. Replaces BOOST_CURRENT_LOCATION with a custom macro.

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-26 08:53:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/492#pullrequestreview-576134657  

📁 include/boost/json/detail/except.hpp

```diff
  25 | 
  26 | // VFALCO we are supporting Boost 1.67 because it is in a lot of distros
```

> Username: vinniefalco  
> Created_at: 2021-01-26 08:53:07 UTC  
> Updated_at: 2021-01-27 07:44:34 UTC  
> Url: https://github.com/boostorg/json/pull/492#discussion_r564341928  

This comment is no longer applicable


---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2021-01-26 08:53:40 UTC  
> Url: https://github.com/boostorg/json/pull/492#issuecomment-767397773  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/492/pullrequest-condensed-b308469.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/492/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/492/pullrequest.html)

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2021-01-26 08:54:09 UTC  
> Url: https://github.com/boostorg/json/pull/492#issuecomment-767398044  

Commit message should read "Add BOOST_JSON_SOURCE_POS"

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2021-01-26 08:54:55 UTC  
> Url: https://github.com/boostorg/json/pull/492#issuecomment-767398462  

Commit message should read "Don't check Boost version"

---

## Comment 5

> Username: grisumbras  
> Created_at: 2021-01-26 09:50:03 UTC  
> Url: https://github.com/boostorg/json/pull/492#issuecomment-767429018  

The latest update makes it so that standalone_with_boost only tested when everything else is tested _not in standalone mode_. This is due to the fact, that the test needs Boost present and standalone CI tests don't have it.

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2021-01-26 11:38:41 UTC  
> Url: https://github.com/boostorg/json/pull/492#issuecomment-767486873  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/492/pullrequest-condensed-733b592.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/492/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/492/pullrequest.html)

---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-26 17:20:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/492#pullrequestreview-576580663  

📁 test/CMakeLists.txt

```diff
  34 |     target_compile_definitions(limits PRIVATE BOOST_JSON_STANDALONE)
  35 |     target_compile_features(limits PRIVATE cxx_std_17)
  36 |+     list(FILTER BOOST_JSON_TESTS_FILES EXCLUDE REGEX "standalone_with_boost.cpp")
```

> Username: vinniefalco  
> Created_at: 2021-01-26 17:20:14 UTC  
> Updated_at: 2021-01-27 07:44:34 UTC  
> Url: https://github.com/boostorg/json/pull/492#discussion_r564689295  

you nasty boy


---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2021-01-26 19:08:42 UTC  
> Url: https://github.com/boostorg/json/pull/492#issuecomment-767763197  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/492/pullrequest-condensed-f041640.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/492/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/492/pullrequest.html)

---

## Comment 9

> Username: grisumbras  
> Created_at: 2021-01-27 08:25:25 UTC  
> Url: https://github.com/boostorg/json/pull/492#issuecomment-768119909  

> Error: retrieving gpg key timed out.  
  
This appears to be a spurious CI error

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2021-01-27 08:58:42 UTC  
> Url: https://github.com/boostorg/json/pull/492#issuecomment-768137801  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/492/pullrequest-condensed-6b1d959.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/492/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/492/pullrequest.html)

---

## Comment 11

> Username: codecov[bot]  
> Created_at: 2021-01-27 19:52:44 UTC  
> Updated_at: 2021-01-27 20:02:22 UTC  
> Url: https://github.com/boostorg/json/pull/492#issuecomment-768536812  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/492?src=pr&el=h1) Report  
> Merging [#492](https://codecov.io/gh/boostorg/json/pull/492?src=pr&el=desc) (f52b56d) into [develop](https://codecov.io/gh/boostorg/json/commit/932b97e5ce899f3faebb7b7ab5a68b023131b77f?el=desc) (932b97e) will **not change** coverage.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/492/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi)](https://codecov.io/gh/boostorg/json/pull/492?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #492   +/-   ##  
========================================  
  Coverage    99.10%   99.10%             
========================================  
  Files           67       67             
  Lines         6047     6047             
========================================  
  Hits          5993     5993             
  Misses          54       54             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/492?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/detail/impl/except.ipp](https://codecov.io/gh/boostorg/json/pull/492/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9pbXBsL2V4Y2VwdC5pcHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/detail/impl/string\_impl.ipp](https://codecov.io/gh/boostorg/json/pull/492/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9pbXBsL3N0cmluZ19pbXBsLmlwcA==) | `99.15% <100.00%> (ø)` | |  
| [include/boost/json/impl/array.hpp](https://codecov.io/gh/boostorg/json/pull/492/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvYXJyYXkuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/array.ipp](https://codecov.io/gh/boostorg/json/pull/492/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvYXJyYXkuaXBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/null\_resource.ipp](https://codecov.io/gh/boostorg/json/pull/492/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvbnVsbF9yZXNvdXJjZS5pcHA=) | `81.81% <100.00%> (ø)` | |  
| [include/boost/json/impl/object.hpp](https://codecov.io/gh/boostorg/json/pull/492/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvb2JqZWN0LmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/object.ipp](https://codecov.io/gh/boostorg/json/pull/492/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvb2JqZWN0LmlwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/parse.ipp](https://codecov.io/gh/boostorg/json/pull/492/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvcGFyc2UuaXBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/parser.ipp](https://codecov.io/gh/boostorg/json/pull/492/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvcGFyc2VyLmlwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/static\_resource.ipp](https://codecov.io/gh/boostorg/json/pull/492/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvc3RhdGljX3Jlc291cmNlLmlwcA==) | `81.81% <100.00%> (ø)` | |  
| ... and [4 more](https://codecov.io/gh/boostorg/json/pull/492/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/492?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/492?src=pr&el=footer). Last update [932b97e...f52b56d](https://codecov.io/gh/boostorg/json/pull/492?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
