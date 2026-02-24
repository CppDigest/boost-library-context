# #1839 async_base example uses async_initiate [Closed]

> Username: madmongo1  
> Created at: 2020-02-06 15:11:36 UTC  
> Updated at: 2022-06-21 23:40:25 UTC  
> Closed at: 2022-06-21 23:40:25 UTC  
> Url: https://github.com/boostorg/beast/pull/1839  

I have not added any more narrative comments. Do we need to do so now that the op is a separate class and there is an initiator involved?

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-02-06 15:30:06 UTC  
> Updated_at: 2020-02-07 08:05:39 UTC  
> Url: https://github.com/boostorg/beast/pull/1839#issuecomment-582959576  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1839?src=pr&el=h1) Report  
> Merging [#1839](https://codecov.io/gh/boostorg/beast/pull/1839?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/c058567ec1b6b767343706cdc58f45e69d491dda?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1839/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1839?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1839   +/-   ##  
========================================  
  Coverage    95.17%   95.17%             
========================================  
  Files          156      156             
  Lines        11942    11942             
========================================  
  Hits         11366    11366             
  Misses         576      576  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1839?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/async\_base.hpp](https://codecov.io/gh/boostorg/beast/pull/1839/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2FzeW5jX2Jhc2UuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/zlib/detail/inflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/1839/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9pbmZsYXRlX3N0cmVhbS5pcHA=) | `94.37% <0%> (-0.2%)` | :arrow_down: |  
| [include/boost/beast/zlib/detail/deflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/1839/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9kZWZsYXRlX3N0cmVhbS5pcHA=) | `88.6% <0%> (+0.1%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1839?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1839?src=pr&el=footer). Last update [c058567...5c276e6](https://codecov.io/gh/boostorg/beast/pull/1839?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-06 16:45:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1839#pullrequestreview-354604883  

📁 include/boost/beast/core/async_base.hpp

```diff
  81 |-     typename net::async_result<ReadHandler, void(error_code, std::size_t)>::return_type
  82 |-     async_read(AsyncReadStream& stream, net::mutable_buffer buffer, ReadHandler&& handler)
  79 |+     template<class DeducedHandler, class AsyncReadStream>
```

> Username: vinniefalco  
> Created_at: 2020-02-06 16:45:43 UTC  
> Updated_at: 2020-02-07 07:52:58 UTC  
> Url: https://github.com/boostorg/beast/pull/1839#discussion_r375952279  

A class template parameter is never labeled "deduced" I only use that for function parameters. So just `Handler`.


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-06 16:46:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1839#pullrequestreview-354605394  

📁 include/boost/beast/core/async_base.hpp

```diff
  85 |+         std::size_t total_bytes_transferred_;
  86 |+ 
  87 |+         async_read_op(
```

> Username: vinniefalco  
> Created_at: 2020-02-06 16:46:24 UTC  
> Updated_at: 2020-02-07 07:52:58 UTC  
> Url: https://github.com/boostorg/beast/pull/1839#discussion_r375952650  

Here we need `template<class DeducedHandler>` and `DeducedHandler&&` parameter type

> Username: madmongo1  
> Created_at: 2020-02-06 17:14:30 UTC  
> Updated_at: 2020-02-07 07:52:58 UTC  
> Url: https://github.com/boostorg/beast/pull/1839#discussion_r375968989  

why? pass by value-move into place is a perfectly optimisable idiom.


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-06 16:47:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1839#pullrequestreview-354606182  

📁 include/boost/beast/core/async_base.hpp

```diff
 139 |+         return
 140 |+             net::async_initiate<ReadHandler,
 141 |+                                    void(error_code, std::size_t)>(
```

> Username: vinniefalco  
> Created_at: 2020-02-06 16:47:26 UTC  
> Updated_at: 2020-02-07 07:52:58 UTC  
> Url: https://github.com/boostorg/beast/pull/1839#discussion_r375953267  

uhh..


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-06 21:05:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1839#pullrequestreview-354771218  

📁 include/boost/beast/core/async_base.hpp

```diff
  92 |+             : async_base<Handler,
  93 |+                 typename AsyncReadStream::executor_type>
  94 |+                 (std::forward<DeducedHandler>(handler), stream.get_executor())
```

> Username: vinniefalco  
> Created_at: 2020-02-06 21:05:41 UTC  
> Updated_at: 2020-02-07 07:52:58 UTC  
> Url: https://github.com/boostorg/beast/pull/1839#discussion_r376081476  

```  
            : async_base<Handler,typename AsyncReadStream::executor_type>(  
                std::forward<DeducedHandler>(handler), stream.get_executor())  
```


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-06 21:09:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1839#pullrequestreview-354773449  

📁 include/boost/beast/core/async_base.hpp

```diff
  85 |+         std::size_t total_bytes_transferred_;
  86 |+ 
  87 |+         template<class DeducedHandler>
```

> Username: vinniefalco  
> Created_at: 2020-02-06 21:09:28 UTC  
> Updated_at: 2020-02-07 07:52:58 UTC  
> Url: https://github.com/boostorg/beast/pull/1839#discussion_r376083184  

but.. you said this wasn't needed, did you change your mind?

> Username: madmongo1  
> Created_at: 2020-02-06 23:01:10 UTC  
> Updated_at: 2020-02-07 07:52:58 UTC  
> Url: https://github.com/boostorg/beast/pull/1839#discussion_r376130150  

I'd started so I felt it easier to finish


---

## Comment 7

> Username: stale[bot]  
> Created_at: 2020-03-08 09:22:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1839#issuecomment-596183919  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2020-05-11 17:37:28 UTC  
> Url: https://github.com/boostorg/beast/pull/1839#issuecomment-626847286  

An automated preview of the documentation is available at [http://1839.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](http://1839.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---
