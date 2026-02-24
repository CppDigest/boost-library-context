# #1340 Use built-in union in handler_ptr [Closed]

> Username: djarek  
> Created at: 2018-12-03 02:34:07 UTC  
> Updated at: 2019-09-10 21:03:41 UTC  
> Closed at: 2018-12-04 00:53:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1340  

Replace aligned_union in handler_ptr with the built-in one to reduce the number of template instantiations and remove the reinterpret_casts that were required.

---

## Review 1 [Commented]

> Username: glenfe  
> Created_at: 2018-12-03 02:48:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1340#pullrequestreview-180599714  

📁 include/boost/beast/core/impl/handler_ptr.ipp

```diff
  55 |-         new(&h_) Handler(std::move(other.handler()));
  56 |-         other.handler().~Handler();
  55 |+         new(&h_) Handler(std::move(other.h_));
```

> Username: glenfe  
> Created_at: 2018-12-03 02:47:58 UTC  
> Updated_at: 2018-12-03 03:10:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1340#discussion_r238132693  

No `addressof(h)`? Before you didn't have to worry about `T` having overloaded `&`, but now you do.

> Username: djarek  
> Created_at: 2018-12-03 03:00:58 UTC  
> Updated_at: 2018-12-03 03:10:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1340#discussion_r238133947  

Damn, forgot about that. Done.

> Username: vinniefalco  
> Created_at: 2018-12-03 03:01:27 UTC  
> Updated_at: 2018-12-03 03:10:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1340#discussion_r238133997  

Also fully-qualify `::new`?

> Username: djarek  
> Created_at: 2018-12-03 03:11:06 UTC  
> Updated_at: 2018-12-03 03:11:07 UTC  
> Url: https://github.com/boostorg/beast/pull/1340#discussion_r238134893  

Done.


---

## Comment 2

> Username: vinniefalco  
> Created_at: 2018-12-03 02:48:48 UTC  
> Url: https://github.com/boostorg/beast/pull/1340#issuecomment-443572833  

Reducing instantiations **and** removing `reinterpret_cast`? Great!!

---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2018-12-03 03:02:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1340#pullrequestreview-180601417  

📁 include/boost/beast/core/impl/handler_ptr.ipp

```diff
  55 |-         new(&h_) Handler(std::move(other.handler()));
  56 |-         other.handler().~Handler();
  55 |+         ::new((void*)std::addressof(h_))
```

> Username: vinniefalco  
> Created_at: 2018-12-03 03:02:06 UTC  
> Updated_at: 2018-12-03 03:10:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1340#discussion_r238134066  

I would prefer to leave out C-style casts completely, because it is almost impossible to search for them

> Username: djarek  
> Created_at: 2018-12-03 03:11:11 UTC  
> Url: https://github.com/boostorg/beast/pull/1340#discussion_r238134905  

Done.


---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2018-12-03 05:15:24 UTC  
> Url: https://github.com/boostorg/beast/pull/1340#issuecomment-443591435  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1340?src=pr&el=h1) Report  
> Merging [#1340](https://codecov.io/gh/boostorg/beast/pull/1340?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/a5739c3ea0526ecf960a19491cac3caa74667137?src=pr&el=desc) will **increase** coverage by `<.01%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1340/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1340?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1340      +/-   ##  
===========================================  
+ Coverage    92.05%   92.06%   +<.01%       
===========================================  
  Files          124      124                
  Lines        11536    11537       +1       
===========================================  
+ Hits         10620    10621       +1       
  Misses         916      916  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1340?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/impl/handler\_ptr.ipp](https://codecov.io/gh/boostorg/beast/pull/1340/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvaGFuZGxlcl9wdHIuaXBw) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/core/handler\_ptr.hpp](https://codecov.io/gh/boostorg/beast/pull/1340/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2hhbmRsZXJfcHRyLmhwcA==) | `100% <100%> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1340?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1340?src=pr&el=footer). Last update [a5739c3...e970a3f](https://codecov.io/gh/boostorg/beast/pull/1340?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2018-12-04 00:38:51 UTC  
> Url: https://github.com/boostorg/beast/pull/1340#issuecomment-443925159  

merged

---
