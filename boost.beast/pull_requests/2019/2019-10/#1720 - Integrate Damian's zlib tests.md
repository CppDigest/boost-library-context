# #1720 Integrate Damian's zlib tests [Closed]

> Username: AeroStun  
> Created at: 2019-10-06 13:07:28 UTC  
> Updated at: 2019-10-08 00:55:02 UTC  
> Closed at: 2019-10-08 00:55:02 UTC  
> Url: https://github.com/boostorg/beast/pull/1720  

Step n°1 for #1719  
  
Damian:  
- Added an example  
- Fixed data races in http and websocket examples  
- Switched Azure Pipelines to Clang 9  
- Added documentation for configuration macros  
- Added various tests for zlib  
  
Builds and all tests pass on my machine; will see what the CI says  
(Had to use my own branch so I could cherry-pick and skip one commit that clashed with upstream)

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2019-10-06 13:14:18 UTC  
> Url: https://github.com/boostorg/beast/pull/1720#issuecomment-538746106  

There are commits unrelated to zlib, these need to go in their own pull request

---

## Comment 2

> Username: AeroStun  
> Created_at: 2019-10-06 13:15:02 UTC  
> Url: https://github.com/boostorg/beast/pull/1720#issuecomment-538746200  

Okay; I'll redispatch them

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2019-10-06 14:43:23 UTC  
> Url: https://github.com/boostorg/beast/pull/1720#issuecomment-538753795  

Where are the other commits?

---

## Comment 4

> Username: AeroStun  
> Created_at: 2019-10-06 14:44:21 UTC  
> Url: https://github.com/boostorg/beast/pull/1720#issuecomment-538753879  

Nowhere for now; I've been focusing on fixing these ones

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2019-10-06 14:44:47 UTC  
> Url: https://github.com/boostorg/beast/pull/1720#issuecomment-538753917  

The other commits have to go in first. If you roll them up into a pull request then I will review it and merge it.

---

## Comment 6

> Username: AeroStun  
> Created_at: 2019-10-06 14:45:06 UTC  
> Url: https://github.com/boostorg/beast/pull/1720#issuecomment-538753936  

Okay; will do

---

## Comment 7

> Username: codecov[bot]  
> Created_at: 2019-10-06 15:02:35 UTC  
> Updated_at: 2019-10-07 01:11:30 UTC  
> Url: https://github.com/boostorg/beast/pull/1720#issuecomment-538755578  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1720?src=pr&el=h1) Report  
> Merging [#1720](https://codecov.io/gh/boostorg/beast/pull/1720?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/1261e7773f391f02e9f09e3584ada9274c8b6987?src=pr&el=desc) will **increase** coverage by `0.69%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1720/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1720?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1720      +/-   ##  
===========================================  
+ Coverage    94.44%   95.13%   +0.69%       
===========================================  
  Files          156      156                
  Lines        11933    11944      +11       
===========================================  
+ Hits         11270    11363      +93       
+ Misses         663      581      -82  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1720?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/zlib/inflate\_stream.hpp](https://codecov.io/gh/boostorg/beast/pull/1720/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2luZmxhdGVfc3RyZWFtLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/zlib/detail/deflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/1720/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9kZWZsYXRlX3N0cmVhbS5pcHA=) | `88.19% <100%> (+4.87%)` | :arrow_up: |  
| [include/boost/beast/zlib/detail/inflate\_stream.hpp](https://codecov.io/gh/boostorg/beast/pull/1720/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9pbmZsYXRlX3N0cmVhbS5ocHA=) | `100% <0%> (ø)` | :arrow_up: |  
| [include/boost/beast/zlib/detail/window.hpp](https://codecov.io/gh/boostorg/beast/pull/1720/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC93aW5kb3cuaHBw) | `100% <0%> (ø)` | :arrow_up: |  
| [include/boost/beast/zlib/detail/inflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/1720/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9pbmZsYXRlX3N0cmVhbS5pcHA=) | `94.57% <0%> (+6.78%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1720?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1720?src=pr&el=footer). Last update [1261e77...b972fcb](https://codecov.io/gh/boostorg/beast/pull/1720?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 8

> Username: AeroStun  
> Created_at: 2019-10-06 23:28:02 UTC  
> Url: https://github.com/boostorg/beast/pull/1720#issuecomment-538799262  

Since Travis doesn't seem to start the build here, I triggered it on my fork, and everything passes  
https://travis-ci.org/AeroStun/beast/builds/594341155

---

## Review 9 [Commented]

> Username: AeroStun  
> Created_at: 2019-10-07 10:35:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1720#pullrequestreview-298036910  

📁 test/beast/zlib/inflate_stream.cpp

```diff
 461 |+         check({0x02, 0x08, 0x20, 0x80, 0x00, 0x03, 0x00},
 462 |+             error::end_of_stream);
 463 |+         // TODO: Excess data (from golang test inflate suite), should this be an error?
```

> Username: AeroStun  
> Created_at: 2019-10-07 10:35:16 UTC  
> Url: https://github.com/boostorg/beast/pull/1720#discussion_r331949463  

IMO excess data in inflate is definitely an error; the user should be able to provide the right compressed length


---

## Review 10 [Commented]

> Username: AeroStun  
> Created_at: 2019-10-07 10:38:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1720#pullrequestreview-298038524  

These changes fix a few issues and add a bunch of tests  
Waiting for VFalco to see if we remove that //TODO or if we do not consider it an error

---

## Comment 11

> Username: vinniefalco  
> Created_at: 2019-10-07 11:42:33 UTC  
> Url: https://github.com/boostorg/beast/pull/1720#issuecomment-538968592  

Does it fail on regular zlib?

---

## Comment 12

> Username: AeroStun  
> Created_at: 2019-10-07 12:06:43 UTC  
> Url: https://github.com/boostorg/beast/pull/1720#issuecomment-538977821  

Looks like it yes: https://github.com/madler/zlib/blob/cacf7f1d4e3d44d871b605da3b647f07d718623f/inflate.c#L891

---

## Comment 13

> Username: AeroStun  
> Created_at: 2019-10-07 19:11:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1720#issuecomment-539161129  

So I guess we'll simply pop off the comment  
Any changes you'd want to be done with it ?

---

## Comment 14

> Username: vinniefalco  
> Created_at: 2019-10-07 19:20:54 UTC  
> Url: https://github.com/boostorg/beast/pull/1720#issuecomment-539164302  

Not sure, I have to review it and things are getting heated on the wg21 mailing lists...

---
