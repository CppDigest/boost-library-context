# #90 Add Intel ICC CI job on Travis CI [Closed]

> Username: jeking3  
> Created at: 2018-12-22 00:09:01 UTC  
> Updated at: 2019-06-25 17:22:23 UTC  
> Closed at: 2019-06-25 17:22:23 UTC  
> Url: https://github.com/boostorg/uuid/pull/90  

This build job requires a hidden Travis CI environment variable called INTEL_ICC_SERIAL_NUMBER to be added to the Travis CI settings for the repository.  This has been done for Boost.Uuid, and allows Travis CI to build against Intel ICC 1900 (20181018).

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-12-24 12:47:45 UTC  
> Updated_at: 2019-05-19 23:07:28 UTC  
> Url: https://github.com/boostorg/uuid/pull/90#issuecomment-449730246  

# [Codecov](https://codecov.io/gh/boostorg/uuid/pull/90?src=pr&el=h1) Report  
> Merging [#90](https://codecov.io/gh/boostorg/uuid/pull/90?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/uuid/commit/be5876a0fc9c2f7ad92eb95ba12e62d381e9068e?src=pr&el=desc) will **decrease** coverage by `2.25%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/uuid/pull/90/graphs/tree.svg?width=650&token=6falIr5npV&height=150&src=pr)](https://codecov.io/gh/boostorg/uuid/pull/90?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #90      +/-   ##  
===========================================  
- Coverage    81.84%   79.59%   -2.26%       
===========================================  
  Files           15       14       -1       
  Lines          639      642       +3       
  Branches       161      157       -4       
===========================================  
- Hits           523      511      -12       
  Misses          18       18                
- Partials        98      113      +15  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/uuid/pull/90?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/uuid/string\_generator.hpp](https://codecov.io/gh/boostorg/uuid/pull/90/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC91dWlkL3N0cmluZ19nZW5lcmF0b3IuaHBw) | `63.23% <0%> (-16.18%)` | :arrow_down: |  
| [include/boost/uuid/detail/random\_provider.hpp](https://codecov.io/gh/boostorg/uuid/pull/90/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC91dWlkL2RldGFpbC9yYW5kb21fcHJvdmlkZXIuaHBw) | `91.66% <0%> (-8.34%)` | :arrow_down: |  
| [include/boost/uuid/uuid\_io.hpp](https://codecov.io/gh/boostorg/uuid/pull/90/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC91dWlkL3V1aWRfaW8uaHBw) | `47.72% <0%> (-4.55%)` | :arrow_down: |  
| [include/boost/uuid/entropy\_error.hpp](https://codecov.io/gh/boostorg/uuid/pull/90/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC91dWlkL2VudHJvcHlfZXJyb3IuaHBw) | `100% <0%> (ø)` | :arrow_up: |  
| [include/boost/uuid/uuid\_hash.hpp](https://codecov.io/gh/boostorg/uuid/pull/90/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC91dWlkL3V1aWRfaGFzaC5ocHA=) | | |  
| [...nclude/boost/uuid/detail/random\_provider\_posix.ipp](https://codecov.io/gh/boostorg/uuid/pull/90/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC91dWlkL2RldGFpbC9yYW5kb21fcHJvdmlkZXJfcG9zaXguaXBw) | `79.31% <0%> (+0.73%)` | :arrow_up: |  
| [include/boost/uuid/random\_generator.hpp](https://codecov.io/gh/boostorg/uuid/pull/90/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC91dWlkL3JhbmRvbV9nZW5lcmF0b3IuaHBw) | `76.59% <0%> (+3.26%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/uuid/pull/90?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/uuid/pull/90?src=pr&el=footer). Last update [be5876a...b507d9a](https://codecov.io/gh/boostorg/uuid/pull/90?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: jeking3  
> Created_at: 2019-06-25 17:22:23 UTC  
> Url: https://github.com/boostorg/uuid/pull/90#issuecomment-505540895  

Closing until I have more time for it.

---
