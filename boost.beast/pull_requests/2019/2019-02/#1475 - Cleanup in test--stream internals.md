# #1475 Cleanup in test::stream internals: [Closed]

> Username: djarek  
> Created at: 2019-02-23 22:02:04 UTC  
> Updated at: 2019-09-10 21:03:24 UTC  
> Closed at: 2019-02-27 00:38:23 UTC  
> Url: https://github.com/boostorg/beast/pull/1475  

* avoid one instantiation of `bind_handler_front`  
* avoid going through the 2-arg post unnecessarily in `read_op`

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2019-02-24 00:43:16 UTC  
> Url: https://github.com/boostorg/beast/pull/1475#issuecomment-466719330  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1475?src=pr&el=h1) Report  
> Merging [#1475](https://codecov.io/gh/boostorg/beast/pull/1475?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/30382343760a2cded93540287154fc65ac97aa01?src=pr&el=desc) will **decrease** coverage by `0.02%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1475/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1475?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1475      +/-   ##  
===========================================  
- Coverage    92.91%   92.89%   -0.03%       
===========================================  
  Files          144      144                
  Lines        12062    12062                
===========================================  
- Hits         11208    11205       -3       
- Misses         854      857       +3  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1475?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/zlib/detail/deflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/1475/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9kZWZsYXRlX3N0cmVhbS5pcHA=) | `80.1% <0%> (-0.31%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1475?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1475?src=pr&el=footer). Last update [3038234...87f29ed](https://codecov.io/gh/boostorg/beast/pull/1475?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2019-02-24 00:54:39 UTC  
> Url: https://github.com/boostorg/beast/pull/1475#issuecomment-466720503  

Does this work? Do we want it?

---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2019-02-24 00:55:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1475#pullrequestreview-207131020  

📁 include/boost/beast/_experimental/test/impl/stream.hpp

```diff
 102 |+         auto alloc = net::get_associated_allocator(fn_.h_);
 103 |+         wg1_.get_executor().post(
 104 |+             beast::bind_front_handler(std::move(fn_), ec), alloc);
```

> Username: vinniefalco  
> Created_at: 2019-02-24 00:55:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1475#discussion_r259598921  

This doesn't really do anything, it all gets inlined anyway...

> Username: djarek  
> Created_at: 2019-02-24 01:34:26 UTC  
> Url: https://github.com/boostorg/beast/pull/1475#discussion_r259599639  

There's a significant difference between this and a 2-arg post. The 2-arg post does something like this, not counting hooks and work guards:  
```  
auto alloc = net::get_associated_allocator(fn_.h_);  
        wg1_.get_executor().post(  
          [f = std::move(f)](){  
              auto alloc = net::get_associated_allocator(f);  
              net::get_associated_executor(f).dispatch(std::move(f));  
          }, alloc);  
```


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2019-02-24 00:56:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1475#pullrequestreview-207131032  

📁 include/boost/beast/_experimental/test/impl/stream.hpp

```diff
 161 | 
 162 |         ++in_->nwrite;
 163 |+         auto const complete_op = [&](error_code ec, std::size_t n)
```

> Username: vinniefalco  
> Created_at: 2019-02-24 00:56:06 UTC  
> Url: https://github.com/boostorg/beast/pull/1475#discussion_r259598936  

This should be renamed to `upcall`, I think it would be better (I'll do it)


---

## Comment 5

> Username: vinniefalco  
> Created_at: 2019-02-26 17:43:03 UTC  
> Url: https://github.com/boostorg/beast/pull/1475#issuecomment-467539162  

merged, thank you

---
