# #1579 Relax requirements for vector_body [Closed]

> Username: djarek  
> Created at: 2019-04-19 22:57:44 UTC  
> Updated at: 2019-09-10 21:04:12 UTC  
> Closed at: 2019-04-20 19:06:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1579  

std::byte is not an arithmetic type, which caused compilation to fail when it was used in vector_body.

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2019-04-19 23:07:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1579#pullrequestreview-228849194  

📁 test/beast/http/vector_body.cpp

```diff
  20 | BOOST_STATIC_ASSERT(is_body_reader<vector_body<char>>::value);
  21 | 
  22 |+ #if __cpp_lib_byte >= 201603
```

> Username: vinniefalco  
> Created_at: 2019-04-19 23:07:52 UTC  
> Url: https://github.com/boostorg/beast/pull/1579#discussion_r277109812  

is there a boost macro for this? and if so, should we use it instead? ping @glenfe

> Username: vinniefalco  
> Created_at: 2019-04-20 17:20:07 UTC  
> Updated_at: 2019-04-20 17:20:08 UTC  
> Url: https://github.com/boostorg/beast/pull/1579#discussion_r277140972  

I'm hearing that it is okay, this will go into v253.


---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2019-04-20 05:28:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1579#issuecomment-485060166  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1579?src=pr&el=h1) Report  
> Merging [#1579](https://codecov.io/gh/boostorg/beast/pull/1579?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/6e70c4260f51dc5bc667f8e9d972bc97e01ddd10?src=pr&el=desc) will **decrease** coverage by `0.01%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1579/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1579?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##           develop   #1579      +/-   ##  
==========================================  
- Coverage    92.62%   92.6%   -0.02%       
==========================================  
  Files          151     151                
  Lines        12131   12131                
==========================================  
- Hits         11236   11234       -2       
- Misses         895     897       +2  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1579?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/zlib/detail/inflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/1579/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9pbmZsYXRlX3N0cmVhbS5pcHA=) | `87.4% <0%> (-0.2%)` | :arrow_down: |  
| [include/boost/beast/zlib/detail/deflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/1579/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9kZWZsYXRlX3N0cmVhbS5pcHA=) | `80.1% <0%> (-0.11%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1579?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1579?src=pr&el=footer). Last update [6e70c42...345151f](https://codecov.io/gh/boostorg/beast/pull/1579?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2019-04-20 19:06:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1579#issuecomment-485151739  

Merged in v253

---
