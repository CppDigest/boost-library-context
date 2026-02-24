# #1050 Don't copy the handler in websocket write_some_op [Closed]

> Username: cstratopoulos  
> Created at: 2018-02-28 20:32:23 UTC  
> Updated at: 2018-03-05 20:18:41 UTC  
> Closed at: 2018-03-01 15:51:21 UTC  
> Url: https://github.com/boostorg/beast/pull/1050  

While experimenting with the latest version of Beast I think I spotted a small omission from the revamp in https://github.com/boostorg/beast/commit/200e898f7e287d33863990ccf7af0f07bfa1a1a6  
  
This ~10 character commit is my attempt to fix this issue :smile:  
  
Some stray observations:   
  
- a recursive `grep` for the phrase `Copies will be made of the handler as required` turns up a lot of results in the documentation for various read/write ops. I guess these lines should all be removed?  
- I think the issue in this PR could have been caught by a compile time unit test in the form of a static assert. Maybe an approach with `boost::callable_traits::is_invocable` and a move-only function object could be used to test a wide variety of operations to make sure they don't require handler copying.  
  
I think maybe both those points are outside the scope of this PR, but if you think they are worth an issue or PR of their own I would be happy to contribute or help.

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2018-02-28 20:53:06 UTC  
> Url: https://github.com/boostorg/beast/pull/1050#issuecomment-369378908  

Yikes...you're right!

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2018-02-28 20:58:50 UTC  
> Url: https://github.com/boostorg/beast/pull/1050#issuecomment-369380587  

Asio docs (which I copied) were never updated:  
http://www.boost.org/doc/libs/1_66_0/doc/html/boost_asio/reference/async_read/overload1.html

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2018-02-28 20:59:58 UTC  
> Url: https://github.com/boostorg/beast/pull/1050#issuecomment-369380936  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1050?src=pr&el=h1) Report  
> Merging [#1050](https://codecov.io/gh/boostorg/beast/pull/1050?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/3a5f2effc1962c8752d7f162e7c8ef83ca943a2e?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1050/graphs/tree.svg?token=Gq6MFA9JRF&src=pr&height=150&width=650)](https://codecov.io/gh/boostorg/beast/pull/1050?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1050   +/-   ##  
========================================  
  Coverage    95.41%   95.41%             
========================================  
  Files          106      106             
  Lines        10595    10595             
========================================  
  Hits         10109    10109             
  Misses         486      486  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1050?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/impl/write.ipp](https://codecov.io/gh/boostorg/beast/pull/1050/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC93cml0ZS5pcHA=) | `97.95% <100%> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1050?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1050?src=pr&el=footer). Last update [3a5f2ef...591f40d](https://codecov.io/gh/boostorg/beast/pull/1050?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 4 [Approved]

> Username: djarek  
> Created_at: 2018-02-28 21:23:03 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/beast/pull/1050#pullrequestreview-100233807  

LGTM

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2018-02-28 21:43:23 UTC  
> Updated_at: 2018-02-28 21:44:07 UTC  
> Url: https://github.com/boostorg/beast/pull/1050#issuecomment-369393342  

This is part of **v161** now: https://github.com/boostorg/beast/pull/1051  
I also added some tests, they passed

---
