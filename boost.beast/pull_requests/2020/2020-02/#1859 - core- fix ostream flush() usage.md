# #1859 core: fix ostream flush() usage [Closed]

> Username: reddwarf69  
> Created at: 2020-02-25 11:41:25 UTC  
> Updated at: 2020-02-29 22:32:22 UTC  
> Closed at: 2020-02-29 22:32:22 UTC  
> Url: https://github.com/boostorg/beast/pull/1859  

After calling the DynamicBuffer commit function the buffer received via  
prepare() can be invalidated but the ostream kept using it.  
  
Fixes #1853.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-02-25 12:04:29 UTC  
> Url: https://github.com/boostorg/beast/pull/1859#issuecomment-590833999  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1859?src=pr&el=h1) Report  
> Merging [#1859](https://codecov.io/gh/boostorg/beast/pull/1859?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/c058567ec1b6b767343706cdc58f45e69d491dda?src=pr&el=desc) will **increase** coverage by `<.01%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1859/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1859?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1859      +/-   ##  
===========================================  
+ Coverage    95.17%   95.17%   +<.01%       
===========================================  
  Files          156      156                
  Lines        11942    11943       +1       
===========================================  
+ Hits         11366    11367       +1       
  Misses         576      576  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1859?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/detail/ostream.hpp](https://codecov.io/gh/boostorg/beast/pull/1859/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC9vc3RyZWFtLmhwcA==) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/zlib/detail/inflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/1859/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9pbmZsYXRlX3N0cmVhbS5pcHA=) | `94.37% <0%> (-0.2%)` | :arrow_down: |  
| [include/boost/beast/zlib/detail/deflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/1859/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9kZWZsYXRlX3N0cmVhbS5pcHA=) | `88.6% <0%> (+0.1%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1859?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1859?src=pr&el=footer). Last update [c058567...71a4051](https://codecov.io/gh/boostorg/beast/pull/1859?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: madmongo1  
> Created_at: 2020-02-25 12:06:17 UTC  
> Url: https://github.com/boostorg/beast/pull/1859#issuecomment-590834645  

Nice, thank you.

---

## Comment 3

> Username: reddwarf69  
> Created_at: 2020-02-25 12:51:44 UTC  
> Updated_at: 2020-02-25 12:53:20 UTC  
> Url: https://github.com/boostorg/beast/pull/1859#issuecomment-590851247  

I took a quick look at the AppVeyor output but couldn't see the error (other than the `Build execution time has reached the maximum allowed time for your plan (60 minutes).` thing)  
Is this a problem or an "ignore it, AppVeyor has been failing for ages" situation?

---

## Comment 4

> Username: madmongo1  
> Created_at: 2020-02-25 16:19:07 UTC  
> Url: https://github.com/boostorg/beast/pull/1859#issuecomment-590950402  

> Build execution time has reached the maximum allowed time for your plan (60 minutes).  
  
Probably a question for @vinniefalco

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2020-02-25 21:28:43 UTC  
> Url: https://github.com/boostorg/beast/pull/1859#issuecomment-591078385  

We could remove a matrix item from Appveyor and make sure it is covered on Azure?

---
