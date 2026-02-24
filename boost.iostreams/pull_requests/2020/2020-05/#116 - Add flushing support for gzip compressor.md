# #116 Add flushing support for gzip compressor [Open]

> Username: sidhant007  
> Created at: 2020-05-20 10:39:33 UTC  
> Updated at: 2025-11-14 01:00:13 UTC  
> Url: https://github.com/boostorg/iostreams/pull/116  

Uses Z_FULL_FLUSH at the point where flushing is done. Therefore it enables the user to read data between any two flush points.  
  
Fixes [1656](https://svn.boost.org/trac10/ticket/1656#no1) for Gzip Filters. More context [here](https://groups.google.com/forum/#!topic/boost-list/0u584AOTIqM)  
  
Relies on Z_BUF_ERROR to determine when to stop deflating.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-05-26 09:06:14 UTC  
> Updated_at: 2020-05-26 09:11:19 UTC  
> Url: https://github.com/boostorg/iostreams/pull/116#issuecomment-633904121  

# [Codecov](https://codecov.io/gh/boostorg/iostreams/pull/116?src=pr&el=h1) Report  
> Merging [#116](https://codecov.io/gh/boostorg/iostreams/pull/116?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/iostreams/commit/601244b26aba14a2524ab1307243d0c5ea566206&el=desc) will **increase** coverage by `0.04%`.  
> The diff coverage is `72.22%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/iostreams/pull/116/graphs/tree.svg?width=650&height=150&src=pr&token=LBEfwtNfca)](https://codecov.io/gh/boostorg/iostreams/pull/116?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #116      +/-   ##  
===========================================  
+ Coverage    68.96%   69.00%   +0.04%       
===========================================  
  Files           80       80                
  Lines         3444     3462      +18       
  Branches      1027     1033       +6       
===========================================  
+ Hits          2375     2389      +14       
+ Misses         452      451       -1       
- Partials       617      622       +5       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/iostreams/pull/116?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [src/zlib.cpp](https://codecov.io/gh/boostorg/iostreams/pull/116/diff?src=pr&el=tree#diff-c3JjL3psaWIuY3Bw) | `92.85% <ø> (ø)` | |  
| [include/boost/iostreams/filter/symmetric.hpp](https://codecov.io/gh/boostorg/iostreams/pull/116/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9pb3N0cmVhbXMvZmlsdGVyL3N5bW1ldHJpYy5ocHA=) | `71.87% <50.00%> (-2.55%)` | :arrow_down: |  
| [include/boost/iostreams/filter/gzip.hpp](https://codecov.io/gh/boostorg/iostreams/pull/116/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9pb3N0cmVhbXMvZmlsdGVyL2d6aXAuaHBw) | `60.85% <100.00%> (+0.46%)` | :arrow_up: |  
| [include/boost/iostreams/filter/zlib.hpp](https://codecov.io/gh/boostorg/iostreams/pull/116/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9pb3N0cmVhbXMvZmlsdGVyL3psaWIuaHBw) | `75.00% <100.00%> (+2.11%)` | :arrow_up: |  
| [.../iostreams/detail/streambuf/indirect\_streambuf.hpp](https://codecov.io/gh/boostorg/iostreams/pull/116/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9pb3N0cmVhbXMvZGV0YWlsL3N0cmVhbWJ1Zi9pbmRpcmVjdF9zdHJlYW1idWYuaHBw) | `77.77% <0.00%> (ø)` | |  
| [.../iostreams/detail/adapter/non\_blocking\_adapter.hpp](https://codecov.io/gh/boostorg/iostreams/pull/116/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9pb3N0cmVhbXMvZGV0YWlsL2FkYXB0ZXIvbm9uX2Jsb2NraW5nX2FkYXB0ZXIuaHBw) | `76.47% <0.00%> (+5.88%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/iostreams/pull/116?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/iostreams/pull/116?src=pr&el=footer). Last update [601244b...c7230cc](https://codecov.io/gh/boostorg/iostreams/pull/116?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: sidhant007  
> Created_at: 2020-06-01 08:07:57 UTC  
> Url: https://github.com/boostorg/iostreams/pull/116#issuecomment-636683945  

Hey @jeking3, can you kindly review this PR

---

## Comment 3

> Username: R030t1  
> Created_at: 2021-05-15 02:50:59 UTC  
> Updated_at: 2021-05-15 03:36:19 UTC  
> Url: https://github.com/boostorg/iostreams/pull/116#issuecomment-841588365  

Yeet this please  
It is preeminently yeetable  
  
Worth noting, I guess, that you can use new/delete to force the desired behavior.  
  
Any chance to generalize this for the other compressors? I'll have a look at how hard it is to add zstd.

---

## Comment 4

> Username: subconscious0  
> Created_at: 2025-11-14 01:00:13 UTC  
> Url: https://github.com/boostorg/iostreams/pull/116#issuecomment-3530365077  

Hi can @jeking3 or someone kindly review this PR?

---
