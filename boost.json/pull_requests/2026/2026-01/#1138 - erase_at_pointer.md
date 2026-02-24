# #1138 erase_at_pointer  [Open]

> Username: RoyBellingan  
> Created at: 2026-01-06 06:15:28 UTC  
> Updated at: 2026-02-03 19:23:45 UTC  
> Url: https://github.com/boostorg/json/pull/1138  

Hopefully a more clear and decent version of  #1071

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2026-01-06 06:22:59 UTC  
> Updated_at: 2026-01-27 15:37:35 UTC  
> Url: https://github.com/boostorg/json/pull/1138#issuecomment-3713286905  

An automated preview of the documentation is available at [https://1138.json.prtest2.cppalliance.org/libs/json/doc/html/index.html](https://1138.json.prtest2.cppalliance.org/libs/json/doc/html/index.html)  
  
If more commits are pushed to the pull request, the docs will rebuild at the same URL.  
  
2026-01-27 15:37:33 UTC

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2026-01-06 06:35:17 UTC  
> Updated_at: 2026-02-03 19:23:45 UTC  
> Url: https://github.com/boostorg/json/pull/1138#issuecomment-3713319730  

GCOVR code coverage report [https://1138.json.prtest2.cppalliance.org/gcovr/index.html](https://1138.json.prtest2.cppalliance.org/gcovr/index.html)  
LCOV code coverage report [https://1138.json.prtest2.cppalliance.org/genhtml/index.html](https://1138.json.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff Report [https://1138.json.prtest2.cppalliance.org/diff-report/index.html](https://1138.json.prtest2.cppalliance.org/diff-report/index.html)  
  
Build time: 2026-02-03 19:23:44 UTC

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2026-01-06 07:20:02 UTC  
> Updated_at: 2026-01-29 11:41:02 UTC  
> Url: https://github.com/boostorg/json/pull/1138#issuecomment-3713469641  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/1138?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:x: Patch coverage is `82.81250%` with `11 lines` in your changes missing coverage. Please review.  
:white_check_mark: Project coverage is 93.59%. Comparing base ([`6238e35`](https://app.codecov.io/gh/boostorg/json/commit/6238e35f8dc272a3b3eb426a919136cb782ccb10?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`4c97cd0`](https://app.codecov.io/gh/boostorg/json/commit/4c97cd0ff39c453043cf1b474bdbcacb1979d0f0?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/json/pull/1138?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Patch % | Lines |  
|---|---|---|  
| [include/boost/json/impl/pointer.ipp](https://app.codecov.io/gh/boostorg/json/pull/1138?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fimpl%2Fpointer.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvcG9pbnRlci5pcHA=) | 82.81% | [11 Missing :warning: ](https://app.codecov.io/gh/boostorg/json/pull/1138?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/1138/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/1138?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1138      +/-   ##  
===========================================  
- Coverage    93.67%   93.59%   -0.08%       
===========================================  
  Files           91       91                
  Lines         9153     9217      +64       
===========================================  
+ Hits          8574     8627      +53       
- Misses         579      590      +11       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/json/pull/1138?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/value.hpp](https://app.codecov.io/gh/boostorg/json/pull/1138?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fvalue.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlLmhwcA==) | `98.83% <ø> (ø)` | |  
| [include/boost/json/impl/pointer.ipp](https://app.codecov.io/gh/boostorg/json/pull/1138?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fimpl%2Fpointer.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvcG9pbnRlci5pcHA=) | `96.41% <82.81%> (-3.59%)` | :arrow_down: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/1138?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/1138?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [6238e35...4c97cd0](https://app.codecov.io/gh/boostorg/json/pull/1138?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2026-01-14 01:57:37 UTC  
> Url: https://github.com/boostorg/json/pull/1138#issuecomment-3747336440  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1138/pullrequest-condensed-b695be2.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1138/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1138/pullrequest.html)

---

## Review 5 [Changes requested]

> Username: grisumbras  
> Created_at: 2026-01-19 14:41:19 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/json/pull/1138#pullrequestreview-3676637731  

In addition to other changes, I expect the tests to have the full code coverage of the added lines.

📁 include/boost/json/impl/pointer.ipp

```diff
 528 |+     ec.clear();
 529 |+     if(sv.empty()){
 530 |+         BOOST_JSON_FAIL(ec, error::syntax);
```

> Username: grisumbras  
> Created_at: 2026-01-19 07:21:58 UTC  
> Updated_at: 2026-01-19 14:41:19 UTC  
> Url: https://github.com/boostorg/json/pull/1138#discussion_r2703517006  

`error::syntax` is for JSON syntax errors. Arguably, `error::missing_slash` is fitting (you require at least one pointer token, and a pointer token starts with a /. But there's an interesting question if we want to allow zero tokens, and what to do in that case. For example, we can set the root object to null.

---

📁 include/boost/json/impl/pointer.ipp

```diff
 584 |+                 if( !result )
 585 |+                 {
 586 |+                     BOOST_JSON_FAIL(ec, error::past_the_end);
```

> Username: grisumbras  
> Created_at: 2026-01-19 14:22:55 UTC  
> Updated_at: 2026-01-19 14:41:19 UTC  
> Url: https://github.com/boostorg/json/pull/1138#discussion_r2704979837  

`error::past_the_end` is for when the current token is the past-the-end token (`-`). This is just `error::not_found`.

---

📁 include/boost/json/impl/pointer.ipp

```diff
 523 |+ bool
 524 |+ value::erase_at_pointer(
 525 |+     string_view         sv,
```

> Username: grisumbras  
> Created_at: 2026-01-19 14:34:33 UTC  
> Updated_at: 2026-01-19 14:41:19 UTC  
> Url: https://github.com/boostorg/json/pull/1138#discussion_r2705022290  

Please, reformat here and elsewhere. The project doesn't do such token alignment.

> Username: RoyBellingan  
> Created_at: 2026-01-20 00:33:07 UTC  
> Updated_at: 2026-01-20 00:33:08 UTC  
> Url: https://github.com/boostorg/json/pull/1138#discussion_r2706385279  

😓

---

📁 include/boost/json/impl/pointer.ipp

```diff
 539 |+     auto previous_result = this;
 540 |+ 
 541 |+     while (true)
```

> Username: grisumbras  
> Created_at: 2026-01-19 14:39:06 UTC  
> Updated_at: 2026-01-19 14:41:19 UTC  
> Url: https://github.com/boostorg/json/pull/1138#discussion_r2705038172  

Looking at this loop, this is pretty much `walk_pointer` used with `find_pointer` lambdas, followed by element erasure, if the search was successful. The only difference is that you need to store the parent of the discovered element, which can be achieved with lambdas with captures.  
  
So, why not do that instead of duplicating `walk_pointer`?

> Username: RoyBellingan  
> Created_at: 2026-01-20 00:33:34 UTC  
> Url: https://github.com/boostorg/json/pull/1138#discussion_r2706385772  

I will check that part of the code, thank you


📁 test/Jamfile

```diff
  24 |     doc_using_numbers.cpp
  25 |     double.cpp
  26 |+     erase_at_pointer.cpp
```

> Username: grisumbras  
> Created_at: 2026-01-19 14:16:51 UTC  
> Updated_at: 2026-01-19 14:41:19 UTC  
> Url: https://github.com/boostorg/json/pull/1138#discussion_r2704957187  

I don't think this feature warrants a separate test file. Just use `pointer.cpp`.


📁 include/boost/json/value.hpp

```diff
3042 |+     BOOST_JSON_DECL
3043 |+     bool
3044 |+     erase_at_pointer(
```

> Username: grisumbras  
> Created_at: 2026-01-19 14:17:56 UTC  
> Updated_at: 2026-01-19 14:41:19 UTC  
> Url: https://github.com/boostorg/json/pull/1138#discussion_r2704961201  

There should also be a non-throwing overload.


📁 test/erase_at_pointer.cpp

```diff
  12 |+ #include <boost/json/serialize.hpp>
  13 |+ #include "test_suite.hpp"
  14 |+ #include <iostream>
```

> Username: grisumbras  
> Created_at: 2026-01-19 14:27:47 UTC  
> Updated_at: 2026-01-19 14:41:19 UTC  
> Url: https://github.com/boostorg/json/pull/1138#discussion_r2704997503  

This header is not used.

---

📁 test/erase_at_pointer.cpp

```diff
 236 |+         
 237 |+         value target = boost::json::parse(R"(
 238 |+ [
```

> Username: grisumbras  
> Created_at: 2026-01-19 14:29:34 UTC  
> Updated_at: 2026-01-19 14:41:19 UTC  
> Url: https://github.com/boostorg/json/pull/1138#discussion_r2705004188  

Please, here and elsewhere indent according to the surrounding code.

> Username: RoyBellingan  
> Created_at: 2026-01-20 00:32:53 UTC  
> Url: https://github.com/boostorg/json/pull/1138#discussion_r2706385040  

🥲

---

📁 test/erase_at_pointer.cpp

```diff
  51 |+         return boost::json::parse(raw);
  52 |+     }
  53 |+ 
```

> Username: grisumbras  
> Created_at: 2026-01-19 14:31:49 UTC  
> Updated_at: 2026-01-19 14:41:19 UTC  
> Url: https://github.com/boostorg/json/pull/1138#discussion_r2705012659  

Can you configure your editor/IDE to not have stray whitespace at the end of lines?

> Username: RoyBellingan  
> Created_at: 2026-01-20 00:32:56 UTC  
> Url: https://github.com/boostorg/json/pull/1138#discussion_r2706385085  

😞


---
