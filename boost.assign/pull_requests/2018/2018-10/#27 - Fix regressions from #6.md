# #27 Fix regressions from #6 [Merged]

> Username: jeking3  
> Created at: 2018-10-15 00:45:28 UTC  
> Updated at: 2018-10-16 00:16:50 UTC  
> Merged at: 2018-10-16 00:16:46 UTC  
> Closed at: 2018-10-16 00:16:46 UTC  
> Url: https://github.com/boostorg/assign/pull/27  

This fixes #26   
This fixes #28

---

## Comment 1

> Username: jeking3  
> Created_at: 2018-10-15 12:01:13 UTC  
> Url: https://github.com/boostorg/assign/pull/27#issuecomment-429824788  

That went well... :|

---

## Review 2 [Commented]

> Username: morinmorin  
> Created_at: 2018-10-15 14:24:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/assign/pull/27#pullrequestreview-164730078  

📁 include/boost/assign/list_of.hpp

```diff
  44 | 
  45 |- #if defined(BOOST_NO_CXX11_VARIADIC_TEMPLATES) || defined(BOOST_NO_CXX11_RVALUE_REFERENCES)
  45 |+ // some gcc < 4.8 do not support all of the feature required for boost::assign
```

> Username: morinmorin  
> Created_at: 2018-10-15 14:24:27 UTC  
> Updated_at: 2018-10-15 18:00:58 UTC  
> Url: https://github.com/boostorg/assign/pull/27#discussion_r225185170  

gcc <= 4.6 (or gcc < 4.7)


---

## Review 3 [Commented]

> Username: morinmorin  
> Created_at: 2018-10-15 14:31:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/assign/pull/27#pullrequestreview-164733603  

📁 include/boost/assign/list_of.hpp

```diff
 426 | 
 421 |- #if defined(BOOST_NO_CXX11_VARIADIC_TEMPLATES) || defined(BOOST_NO_CXX11_RVALUE_REFERENCES)
 427 |+ #if BOOST_ASSIGN_USE_VARIADIC_TEMPLATES
```

> Username: morinmorin  
> Created_at: 2018-10-15 14:31:18 UTC  
> Updated_at: 2018-10-15 18:00:58 UTC  
> Url: https://github.com/boostorg/assign/pull/27#discussion_r225187882  

The condition is flipped here and there.  
`#ifndef BOOST_ASSIGN_USE_VARIADIC_TEMPLATES` is a correct condition. Or, replace bodies of `#if` branch and `#else` branch.

> Username: jeking3  
> Created_at: 2018-10-15 15:33:49 UTC  
> Updated_at: 2018-10-15 18:00:58 UTC  
> Url: https://github.com/boostorg/assign/pull/27#discussion_r225212848  

Thanks, I was in a rush.  That's why we have pull requests. :)  I'll get back to it shortly.


---

## Comment 4

> Username: jeking3  
> Created_at: 2018-10-15 19:44:35 UTC  
> Url: https://github.com/boostorg/assign/pull/27#issuecomment-429987714  

... just waiting for the travis CI backlog to clear and run this build ...

---

## Comment 5

> Username: morinmorin  
> Created_at: 2018-10-15 22:49:36 UTC  
> Url: https://github.com/boostorg/assign/pull/27#issuecomment-430040708  

Thanks, LGTM for the .hpp files.

---

## Comment 6

> Username: codecov[bot]  
> Created_at: 2018-10-15 23:10:35 UTC  
> Updated_at: 2018-10-15 23:10:55 UTC  
> Url: https://github.com/boostorg/assign/pull/27#issuecomment-430045112  

# [Codecov](https://codecov.io/gh/boostorg/assign/pull/27?src=pr&el=h1) Report  
> Merging [#27](https://codecov.io/gh/boostorg/assign/pull/27?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/assign/commit/88bc7a302c398cf9f3b025e2ec83fcdae4055370?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/assign/pull/27/graphs/tree.svg?width=650&token=SOGsyPqaS7&height=150&src=pr)](https://codecov.io/gh/boostorg/assign/pull/27?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #27   +/-   ##  
========================================  
  Coverage    78.62%   78.62%             
========================================  
  Files           13       13             
  Lines          262      262             
  Branches        55       55             
========================================  
  Hits           206      206             
  Misses           5        5             
  Partials        51       51  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/assign/pull/27?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/assign/assignment\_exception.hpp](https://codecov.io/gh/boostorg/assign/pull/27/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9hc3NpZ24vYXNzaWdubWVudF9leGNlcHRpb24uaHBw) | `66.66% <ø> (ø)` | :arrow_up: |  
| [include/boost/assign/list\_of.hpp](https://codecov.io/gh/boostorg/assign/pull/27/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9hc3NpZ24vbGlzdF9vZi5ocHA=) | `80.35% <ø> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/assign/pull/27?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/assign/pull/27?src=pr&el=footer). Last update [88bc7a3...91c69ce](https://codecov.io/gh/boostorg/assign/pull/27?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
