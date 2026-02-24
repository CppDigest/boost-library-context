# #112 Fix using non-standard library name layout [Closed]

> Username: MarcelRaad  
> Created at: 2020-03-05 16:45:31 UTC  
> Updated at: 2020-03-24 11:11:08 UTC  
> Closed at: 2020-03-18 15:45:38 UTC  
> Url: https://github.com/boostorg/uuid/pull/112  

As `BOOST_AUTO_LINK_NOMANGLE` is checked after `BOOST_AUTO_LINK_TAGGED`  
and `BOOST_AUTO_LINK_SYSTEM` in boost/config/auto_link.hpp, using  
tagged or system layout would have used the wrong library names for  
linking.  
  
This change is based on https://github.com/boostorg/uuid/pull/110, but  
adapted to newer changes for clang-cl in  
https://github.com/boostorg/config/commit/a18911902dff03f37582696a13bf60768c183c55#diff-e96bc863ddd09e106922c7fbc8f81d1dR103.

---

## Comment 1

> Username: Lastique  
> Created_at: 2020-03-05 17:13:41 UTC  
> Url: https://github.com/boostorg/uuid/pull/112#issuecomment-595343878  

Maybe it'd be better to enhance `auto_link.hpp` so that it can be used to link libraries without mangling? Duplicating elaborate compiler checks doesn't look good.

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2020-03-06 00:09:08 UTC  
> Url: https://github.com/boostorg/uuid/pull/112#issuecomment-595508183  

# [Codecov](https://codecov.io/gh/boostorg/uuid/pull/112?src=pr&el=h1) Report  
> :exclamation: No coverage uploaded for pull request base (`develop@ca0185f`). [Click here to learn what that means](https://docs.codecov.io/docs/error-reference#section-missing-base-commit).  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/uuid/pull/112/graphs/tree.svg?width=650&height=150&src=pr&token=6falIr5npV)](https://codecov.io/gh/boostorg/uuid/pull/112?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##             develop     #112   +/-   ##  
==========================================  
  Coverage           ?   81.84%             
==========================================  
  Files              ?       15             
  Lines              ?      639             
  Branches           ?      161             
==========================================  
  Hits               ?      523             
  Misses             ?       18             
  Partials           ?       98             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/uuid/pull/112?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/uuid/detail/uuid\_generic.ipp](https://codecov.io/gh/boostorg/uuid/pull/112/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC91dWlkL2RldGFpbC91dWlkX2dlbmVyaWMuaXBw) | `100.00% <0.00%> (ø)` | |  
| [include/boost/uuid/detail/md5.hpp](https://codecov.io/gh/boostorg/uuid/pull/112/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC91dWlkL2RldGFpbC9tZDUuaHBw) | `88.19% <0.00%> (ø)` | |  
| [include/boost/uuid/uuid\_hash.hpp](https://codecov.io/gh/boostorg/uuid/pull/112/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC91dWlkL3V1aWRfaGFzaC5ocHA=) | `100.00% <0.00%> (ø)` | |  
| [include/boost/uuid/basic\_name\_generator.hpp](https://codecov.io/gh/boostorg/uuid/pull/112/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC91dWlkL2Jhc2ljX25hbWVfZ2VuZXJhdG9yLmhwcA==) | `67.92% <0.00%> (ø)` | |  
| [include/boost/uuid/string\_generator.hpp](https://codecov.io/gh/boostorg/uuid/pull/112/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC91dWlkL3N0cmluZ19nZW5lcmF0b3IuaHBw) | `79.41% <0.00%> (ø)` | |  
| [include/boost/uuid/detail/uuid\_x86.ipp](https://codecov.io/gh/boostorg/uuid/pull/112/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC91dWlkL2RldGFpbC91dWlkX3g4Ni5pcHA=) | `100.00% <0.00%> (ø)` | |  
| [include/boost/uuid/random\_generator.hpp](https://codecov.io/gh/boostorg/uuid/pull/112/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC91dWlkL3JhbmRvbV9nZW5lcmF0b3IuaHBw) | `73.33% <0.00%> (ø)` | |  
| [...nclude/boost/uuid/detail/random\_provider\_posix.ipp](https://codecov.io/gh/boostorg/uuid/pull/112/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC91dWlkL2RldGFpbC9yYW5kb21fcHJvdmlkZXJfcG9zaXguaXBw) | `78.57% <0.00%> (ø)` | |  
| [...de/boost/uuid/detail/random\_provider\_getrandom.ipp](https://codecov.io/gh/boostorg/uuid/pull/112/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC91dWlkL2RldGFpbC9yYW5kb21fcHJvdmlkZXJfZ2V0cmFuZG9tLmlwcA==) | `78.57% <0.00%> (ø)` | |  
| [include/boost/uuid/uuid\_io.hpp](https://codecov.io/gh/boostorg/uuid/pull/112/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC91dWlkL3V1aWRfaW8uaHBw) | `52.27% <0.00%> (ø)` | |  
| ... and [5 more](https://codecov.io/gh/boostorg/uuid/pull/112/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/uuid/pull/112?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/uuid/pull/112?src=pr&el=footer). Last update [ca0185f...f72af64](https://codecov.io/gh/boostorg/uuid/pull/112?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 3

> Username: MarcelRaad  
> Created_at: 2020-03-06 08:20:58 UTC  
> Url: https://github.com/boostorg/uuid/pull/112#issuecomment-595655980  

@Lastique Thanks, great idea, and even trivial! I'm letting the tests cycle in my fork at https://github.com/MarcelRaad/config/pull/1 and close this one if it gets accepted.

---

## Comment 4

> Username: MarcelRaad  
> Created_at: 2020-03-18 11:45:20 UTC  
> Url: https://github.com/boostorg/uuid/pull/112#issuecomment-600576372  

PR for Config: https://github.com/boostorg/config/pull/325

---

## Comment 5

> Username: mclow  
> Created_at: 2020-03-18 15:24:36 UTC  
> Url: https://github.com/boostorg/uuid/pull/112#issuecomment-600686694  

So, the plan is to abandon this change?

---

## Comment 6

> Username: MarcelRaad  
> Created_at: 2020-03-18 15:45:38 UTC  
> Url: https://github.com/boostorg/uuid/pull/112#issuecomment-600699610  

If there's nothing unexpected that would lead to the Config PR being rejected, yes. Closing this for now.

---
