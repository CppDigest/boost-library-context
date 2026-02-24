# #277 Added missing const overload of dynabuf_readstream<>::next_layer(). [Closed]

> Username: florianbehrens  
> Created at: 2017-03-04 20:31:53 UTC  
> Updated at: 2017-03-16 11:40:09 UTC  
> Closed at: 2017-03-16 11:40:09 UTC  
> Url: https://github.com/boostorg/beast/pull/277  



---

## Comment 1

> Username: codecov-io  
> Created_at: 2017-03-04 20:52:25 UTC  
> Updated_at: 2017-03-04 20:52:34 UTC  
> Url: https://github.com/boostorg/beast/pull/277#issuecomment-284180026  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/277?src=pr&el=h1) Report  
> Merging [#277](https://codecov.io/gh/vinniefalco/Beast/pull/277?src=pr&el=desc) into [http](https://codecov.io/gh/vinniefalco/Beast/commit/4f0c6b32d7ec55c6f4c5ed0fd9caf664931eb997?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
  
```diff  
@@           Coverage Diff           @@  
##             http     #277   +/-   ##  
=======================================  
  Coverage   93.57%   93.57%             
=======================================  
  Files          93       93             
  Lines        6104     6104             
=======================================  
  Hits         5712     5712             
  Misses        392      392  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/277?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/core/dynabuf_readstream.hpp](https://codecov.io/gh/vinniefalco/Beast/compare/4f0c6b32d7ec55c6f4c5ed0fd9caf664931eb997...dc98a9f3ee8ba54e3b1f08ffba05600282a820af?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2R5bmFidWZfcmVhZHN0cmVhbS5ocHA=) | `100% <ø> (ø)` | :white_check_mark: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/277?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/277?src=pr&el=footer). Last update [4f0c6b3...dc98a9f](https://codecov.io/gh/vinniefalco/Beast/compare/4f0c6b32d7ec55c6f4c5ed0fd9caf664931eb997...dc98a9f3ee8ba54e3b1f08ffba05600282a820af?el=footer&src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2017-03-05 02:19:37 UTC  
> Url: https://github.com/boostorg/beast/pull/277#issuecomment-284199902  

This will go into **1.0.0-b31**:  
https://github.com/vinniefalco/Beast/tree/b31

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2017-03-16 11:40:09 UTC  
> Url: https://github.com/boostorg/beast/pull/277#issuecomment-287032389  

Part of b31 now

---
