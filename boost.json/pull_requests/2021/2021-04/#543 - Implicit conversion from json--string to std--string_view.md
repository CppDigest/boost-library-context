# #543 Implicit conversion from json::string to std::string_view [Merged]

> Username: grisumbras  
> Created at: 2021-04-21 20:37:59 UTC  
> Updated at: 2021-04-26 00:33:19 UTC  
> Merged at: 2021-04-26 00:29:54 UTC  
> Closed at: 2021-04-26 00:29:55 UTC  
> Url: https://github.com/boostorg/json/pull/543  

Fix #538

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2021-04-21 21:02:55 UTC  
> Updated_at: 2021-04-26 00:29:22 UTC  
> Url: https://github.com/boostorg/json/pull/543#issuecomment-824355792  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/543?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#543](https://codecov.io/gh/boostorg/json/pull/543?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (baed8e8) into [develop](https://codecov.io/gh/boostorg/json/commit/de6e72d5c0b35f3859acc5900293671a58a55acc?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (de6e72d) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
> :exclamation: Current head baed8e8 differs from pull request most recent head 0d65b0a. Consider uploading reports for the commit 0d65b0a to get more accurate results  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/543/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/543?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #543   +/-   ##  
========================================  
  Coverage    99.10%   99.10%             
========================================  
  Files           67       67             
  Lines         6049     6049             
========================================  
  Hits          5995     5995             
  Misses          54       54             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/543?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/string.hpp](https://codecov.io/gh/boostorg/json/pull/543/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3N0cmluZy5ocHA=) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/543?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/543?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [de6e72d...0d65b0a](https://codecov.io/gh/boostorg/json/pull/543?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2021-04-21 21:16:42 UTC  
> Url: https://github.com/boostorg/json/pull/543#issuecomment-824363214  

Because this is an implicit conversion I'd like @pdimov or @glenfe to weigh in on whether there are situations where a cycle of conversions could cause compilation errors or unexpected results.

---

## Comment 3

> Username: pdimov  
> Created_at: 2021-04-21 21:48:23 UTC  
> Url: https://github.com/boostorg/json/pull/543#issuecomment-824380301  

It's already present in standalone.

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2021-04-21 22:14:02 UTC  
> Url: https://github.com/boostorg/json/pull/543#issuecomment-824392106  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/543/pullrequest-condensed-437b46f.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/543/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/543/pullrequest.html)

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2021-04-21 23:32:53 UTC  
> Url: https://github.com/boostorg/json/pull/543#issuecomment-824431420  

Looks good

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2021-04-21 23:33:04 UTC  
> Url: https://github.com/boostorg/json/pull/543#issuecomment-824431506  

We need a CHANGELOG.md entry

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2021-04-25 21:21:43 UTC  
> Url: https://github.com/boostorg/json/pull/543#issuecomment-826391957  

An automated preview of the documentation is available at [https://543.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://543.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2021-04-25 22:29:02 UTC  
> Url: https://github.com/boostorg/json/pull/543#issuecomment-826400051  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/543/pullrequest-condensed-ec3ca7a.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/543/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/543/pullrequest.html)

---

## Comment 9

> Username: vinniefalco  
> Created_at: 2021-04-26 00:02:58 UTC  
> Url: https://github.com/boostorg/json/pull/543#issuecomment-826418925  

"json::string is convertible to string_view" would be my choice of commit message

---

## Review 10 [Commented]

> Username: grisumbras  
> Created_at: 2021-04-26 00:23:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/543#pullrequestreview-644198396  

📁 doc/qbk/release_notes.qbk

```diff
  17 |+ 
  18 |+ * [issue 538] [link json.ref.boost__json__string.operator_std__string_view
  19 |+   Implicit conversion operator from `string` to `std::string_view`].
```

> Username: grisumbras  
> Created_at: 2021-04-26 00:23:27 UTC  
> Updated_at: 2021-04-26 00:29:17 UTC  
> Url: https://github.com/boostorg/json/pull/543#discussion_r619905872  

This release notes item will be a link to the operator overload in question. Is this OK @vinniefalco ?


---

## Review 11 [Commented]

> Username: grisumbras  
> Created_at: 2021-04-26 00:24:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/543#pullrequestreview-644198481  

📁 include/boost/json/string.hpp

```diff
1143 |+ 
1144 |+         This overload is not defined when either `BOOST_JSON_STANDALONE` or
1145 |+         `BOOST_NO_CXX17_HDR_STRING_VIEW` is defined.
```

> Username: grisumbras  
> Created_at: 2021-04-26 00:24:04 UTC  
> Updated_at: 2021-04-26 00:29:17 UTC  
> Url: https://github.com/boostorg/json/pull/543#discussion_r619905981  

@vinniefalco What do you think about this paragraph?

> Username: vinniefalco  
> Created_at: 2021-04-26 00:25:45 UTC  
> Updated_at: 2021-04-26 00:29:17 UTC  
> Url: https://github.com/boostorg/json/pull/543#discussion_r619906270  

its fine


---
