# #74 Add support for movability to random generators. Fix POSIX random provider [Merged]

> Username: Lastique  
> Created at: 2018-06-17 16:04:11 UTC  
> Updated at: 2018-06-20 14:18:32 UTC  
> Merged at: 2018-06-18 14:24:47 UTC  
> Closed at: 2018-06-18 14:24:48 UTC  
> Url: https://github.com/boostorg/uuid/pull/74  

The commit adds support for move constructors and assignment to random UUID  
generators and random providers. Also, in POSIX random provider, the commit  
improves handling of return value from ::read(), which returns -1  
in case of error and may return 0 in case of success. Retry the call when  
it was interrupted before reading any bytes from /dev/urandom.  
  
Added new tests for movability.  
  
This fixes #71

---

## Comment 1

> Username: jeking3  
> Created_at: 2018-06-17 16:08:57 UTC  
> Url: https://github.com/boostorg/uuid/pull/74#issuecomment-397889028  

Interesting that you chose to do this without boost::move, any particular reason?

---

## Comment 2

> Username: jeking3  
> Created_at: 2018-06-17 16:14:32 UTC  
> Url: https://github.com/boostorg/uuid/pull/74#issuecomment-397889417  

Ideally we should have a compile-fail test that attempts copy by constructor and another copy by assignment to make sure those are properly disabled.  This would ensure in the future nobody re-enables it by accident.

---

## Comment 3

> Username: Lastique  
> Created_at: 2018-06-17 16:18:11 UTC  
> Url: https://github.com/boostorg/uuid/pull/74#issuecomment-397889647  

> Interesting that you chose to do this without boost::move, any particular reason?  
  
I did not want to pull in Boost.Move as a dependency. But if you think it's worth supporting move operations in C++03 then it can be added.

---

## Comment 4

> Username: jeking3  
> Created_at: 2018-06-17 16:37:19 UTC  
> Url: https://github.com/boostorg/uuid/pull/74#issuecomment-397890687  

I was trying to do it with Boost.Move but it wasn't cooperating with me.  It was probably me though. :)  I'd prefer it to be implementation that works with both language levels.  The dependencies in move are pretty light:  
  
https://pdimov.github.io/boostdep-report/develop/move.html  
  
I also offered to help the maintainer of Boost.Move add CI to that repository when I found it had none.

---

## Comment 5

> Username: codecov[bot]  
> Created_at: 2018-06-17 17:49:34 UTC  
> Updated_at: 2018-06-17 21:16:30 UTC  
> Url: https://github.com/boostorg/uuid/pull/74#issuecomment-397894934  

# [Codecov](https://codecov.io/gh/boostorg/uuid/pull/74?src=pr&el=h1) Report  
> Merging [#74](https://codecov.io/gh/boostorg/uuid/pull/74?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/uuid/commit/0d0aa876e5fab6ce80410164403ed4b5ac582645?src=pr&el=desc) will **increase** coverage by `1.43%`.  
> The diff coverage is `77.19%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/uuid/pull/74/graphs/tree.svg?width=650&src=pr&token=6falIr5npV&height=150)](https://codecov.io/gh/boostorg/uuid/pull/74?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #74      +/-   ##  
===========================================  
+ Coverage    78.34%   79.78%   +1.43%       
===========================================  
  Files           13       13                
  Lines          605      638      +33       
  Branches       156      154       -2       
===========================================  
+ Hits           474      509      +35       
  Misses          17       17                
+ Partials       114      112       -2  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/uuid/pull/74?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/uuid/random\_generator.hpp](https://codecov.io/gh/boostorg/uuid/pull/74/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC91dWlkL3JhbmRvbV9nZW5lcmF0b3IuaHBw) | `72.91% <68.96%> (+15.34%)` | :arrow_up: |  
| [...nclude/boost/uuid/detail/random\_provider\_posix.ipp](https://codecov.io/gh/boostorg/uuid/pull/74/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC91dWlkL2RldGFpbC9yYW5kb21fcHJvdmlkZXJfcG9zaXguaXBw) | `86.48% <85.71%> (+10.48%)` | :arrow_up: |  
| [include/boost/uuid/detail/random\_provider.hpp](https://codecov.io/gh/boostorg/uuid/pull/74/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC91dWlkL2RldGFpbC9yYW5kb21fcHJvdmlkZXIuaHBw) | `92.3% <85.71%> (+6.59%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/uuid/pull/74?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/uuid/pull/74?src=pr&el=footer). Last update [0d0aa87...429daf0](https://codecov.io/gh/boostorg/uuid/pull/74?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 6

> Username: Lastique  
> Created_at: 2018-06-17 17:51:32 UTC  
> Url: https://github.com/boostorg/uuid/pull/74#issuecomment-397895055  

I've added support for C++03 and compile-fail tests.

---

## Review 7 [Approved]

> Username: MarcelRaad  
> Created_at: 2018-06-17 19:00:01 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/uuid/pull/74#pullrequestreview-129397886  

Works fine for me with VS 2017 Update 7.3 and Update 8 Preview 2.0.

---

## Comment 8

> Username: jeking3  
> Created_at: 2018-06-18 14:24:24 UTC  
> Url: https://github.com/boostorg/uuid/pull/74#issuecomment-398072042  

Would be nice to add a unit test for the EINTR handling at some point.  Thanks for the help on move.

---
