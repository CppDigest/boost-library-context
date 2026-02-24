# #103 Fix for typo (LICENCE) and suggestion for https links [Merged]

> Username: tinko92  
> Created at: 2019-04-21 20:39:57 UTC  
> Updated at: 2019-04-22 12:56:06 UTC  
> Merged at: 2019-04-22 12:56:06 UTC  
> Closed at: 2019-04-22 12:56:06 UTC  
> Url: https://github.com/boostorg/uuid/pull/103  

Hi, this PR is intentionally split into two commits in case I've overlooked some reason that speaks against the second one. The first one fixes some links that pointed to http://www.boost.org/LICENCE_1_0.txt rather than http://www.boost.org/LICENSE_1_0.txt which causes a 404 when clicked in the online documentation or copied into the URL field. The second one changes all occurrences of that link to https, which saves a redirection when clicked.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2019-04-22 01:27:51 UTC  
> Updated_at: 2019-04-22 01:28:43 UTC  
> Url: https://github.com/boostorg/uuid/pull/103#issuecomment-485298648  

# [Codecov](https://codecov.io/gh/boostorg/uuid/pull/103?src=pr&el=h1) Report  
> Merging [#103](https://codecov.io/gh/boostorg/uuid/pull/103?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/uuid/commit/638f72a70c1e014b5421596f0de552d9ae01b178?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/uuid/pull/103/graphs/tree.svg?width=650&token=6falIr5npV&height=150&src=pr)](https://codecov.io/gh/boostorg/uuid/pull/103?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff           @@  
##           develop    #103   +/-   ##  
=======================================  
  Coverage     81.3%   81.3%             
=======================================  
  Files           15      15             
  Lines          642     642             
  Branches       151     151             
=======================================  
  Hits           522     522             
  Misses          18      18             
  Partials       102     102  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/uuid/pull/103?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/uuid/detail/md5.hpp](https://codecov.io/gh/boostorg/uuid/pull/103/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC91dWlkL2RldGFpbC9tZDUuaHBw) | `88.19% <ø> (ø)` | :arrow_up: |  
| [include/boost/uuid/uuid.hpp](https://codecov.io/gh/boostorg/uuid/pull/103/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC91dWlkL3V1aWQuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/uuid/entropy\_error.hpp](https://codecov.io/gh/boostorg/uuid/pull/103/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC91dWlkL2VudHJvcHlfZXJyb3IuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [...de/boost/uuid/detail/random\_provider\_getrandom.ipp](https://codecov.io/gh/boostorg/uuid/pull/103/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC91dWlkL2RldGFpbC9yYW5kb21fcHJvdmlkZXJfZ2V0cmFuZG9tLmlwcA==) | `78.57% <ø> (ø)` | :arrow_up: |  
| [include/boost/uuid/nil\_generator.hpp](https://codecov.io/gh/boostorg/uuid/pull/103/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC91dWlkL25pbF9nZW5lcmF0b3IuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/uuid/uuid\_hash.hpp](https://codecov.io/gh/boostorg/uuid/pull/103/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC91dWlkL3V1aWRfaGFzaC5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/uuid/uuid\_io.hpp](https://codecov.io/gh/boostorg/uuid/pull/103/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC91dWlkL3V1aWRfaW8uaHBw) | `52.27% <ø> (ø)` | :arrow_up: |  
| [include/boost/uuid/detail/uuid\_x86.ipp](https://codecov.io/gh/boostorg/uuid/pull/103/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC91dWlkL2RldGFpbC91dWlkX3g4Ni5pcHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/uuid/basic\_name\_generator.hpp](https://codecov.io/gh/boostorg/uuid/pull/103/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC91dWlkL2Jhc2ljX25hbWVfZ2VuZXJhdG9yLmhwcA==) | `67.92% <ø> (ø)` | :arrow_up: |  
| [include/boost/uuid/string\_generator.hpp](https://codecov.io/gh/boostorg/uuid/pull/103/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC91dWlkL3N0cmluZ19nZW5lcmF0b3IuaHBw) | `69.11% <ø> (ø)` | :arrow_up: |  
| ... and [5 more](https://codecov.io/gh/boostorg/uuid/pull/103/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/uuid/pull/103?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/uuid/pull/103?src=pr&el=footer). Last update [638f72a...3d43ef4](https://codecov.io/gh/boostorg/uuid/pull/103?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
