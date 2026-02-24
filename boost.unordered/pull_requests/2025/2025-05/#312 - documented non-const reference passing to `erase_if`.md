# #312 documented non-const reference passing to `erase_if` [Merged]

> Username: joaquintides  
> Created at: 2025-05-16 10:41:34 UTC  
> Updated at: 2025-05-31 16:08:44 UTC  
> Merged at: 2025-05-31 16:08:37 UTC  
> Closed at: 2025-05-31 16:08:37 UTC  
> Url: https://github.com/boostorg/unordered/pull/312  

This merely documents the status quo by which `erase_if` for maps passes non-const references to the user-provided predicate. Mentioned in this [this conversation](https://github.com/boostorg/unordered/issues/308#issuecomment-2774518565).

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2025-05-16 10:50:15 UTC  
> Url: https://github.com/boostorg/unordered/pull/312#issuecomment-2886373294  

An automated preview of the documentation is available at [https://312.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/index.html](https://312.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2025-05-30 08:13:17 UTC  
> Url: https://github.com/boostorg/unordered/pull/312#issuecomment-2921570286  

An automated preview of the documentation is available at [https://312.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/index.html](https://312.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/index.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2025-05-30 10:23:17 UTC  
> Url: https://github.com/boostorg/unordered/pull/312#issuecomment-2921936069  

An automated preview of the documentation is available at [https://312.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/index.html](https://312.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/index.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2025-05-30 10:29:23 UTC  
> Url: https://github.com/boostorg/unordered/pull/312#issuecomment-2921951485  

An automated preview of the documentation is available at [https://312.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/index.html](https://312.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/index.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2025-05-30 15:25:49 UTC  
> Url: https://github.com/boostorg/unordered/pull/312#issuecomment-2922699780  

An automated preview of the documentation is available at [https://312.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/index.html](https://312.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/index.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2025-05-30 16:03:15 UTC  
> Url: https://github.com/boostorg/unordered/pull/312#issuecomment-2922785833  

An automated preview of the documentation is available at [https://312.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/index.html](https://312.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/index.html)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2025-05-30 20:18:10 UTC  
> Url: https://github.com/boostorg/unordered/pull/312#issuecomment-2923398275  

An automated preview of the documentation is available at [https://312.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/index.html](https://312.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/index.html)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2025-05-31 07:58:15 UTC  
> Url: https://github.com/boostorg/unordered/pull/312#issuecomment-2924635337  

An automated preview of the documentation is available at [https://312.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/index.html](https://312.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/index.html)

---

## Comment 9

> Username: codecov[bot]  
> Created_at: 2025-05-31 11:20:02 UTC  
> Updated_at: 2025-05-31 16:08:44 UTC  
> Url: https://github.com/boostorg/unordered/pull/312#issuecomment-2924992105  

## [Codecov](https://app.codecov.io/gh/boostorg/unordered/pull/312?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 98.23%. Comparing base [(`a64d81a`)](https://app.codecov.io/gh/boostorg/unordered/commit/a64d81a3789b6f5deb8d8569503e6fe129df93a6?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`f0eca99`)](https://app.codecov.io/gh/boostorg/unordered/commit/f0eca993344d829f2c35f80f3c58710c18d163f9?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/unordered/pull/312/graphs/tree.svg?width=650&height=150&src=pr&token=ZqRPZlJZ5N&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/unordered/pull/312?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #312   +/-   ##  
========================================  
  Coverage    98.23%   98.23%             
========================================  
  Files          161      161             
  Lines        22213    22222    +9       
========================================  
+ Hits         21821    21830    +9       
  Misses         392      392             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/unordered/pull/312?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [test/unordered/erase\_if.cpp](https://app.codecov.io/gh/boostorg/unordered/pull/312?src=pr&el=tree&filepath=test%2Funordered%2Ferase_if.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC91bm9yZGVyZWQvZXJhc2VfaWYuY3Bw) | `100.00% <100.00%> (ø)` | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/unordered/pull/312/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/unordered/pull/312?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/unordered/pull/312?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [a64d81a...f0eca99](https://app.codecov.io/gh/boostorg/unordered/pull/312?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
