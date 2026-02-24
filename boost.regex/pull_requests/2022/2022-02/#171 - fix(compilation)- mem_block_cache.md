# #171 fix(compilation): mem_block_cache [Closed]

> Username: vulptex  
> Created at: 2022-02-04 06:52:14 UTC  
> Updated at: 2022-03-07 16:49:53 UTC  
> Closed at: 2022-03-07 16:33:37 UTC  
> Url: https://github.com/boostorg/regex/pull/171  

issue:  
- BOOST_REGEX_MAX_CACHE_BLOCKS=0 can't compile if std::atomic is available  
- BOOST_REGEX_MAX_CACHE_BLOCKS=0 does only work together with BOOST_NO_CXX11_HDR_ATOMIC  
  
fix:  
- hide mem_block_cache if BOOST_REGEX_MAX_CACHE_BLOCKS=0  
 - switched BOOST_REGEX_MAX_CACHE_BLOCKS check and only use mem_block_cache if block size != 0  
  
error:  
  
```shell  
mem_block_cache.hpp:75:60: error: too many initializers for 'std::atomic<void*> [0]   
75 |       static mem_block_cache block_cache = { { {nullptr} } };  
```

---

## Comment 1

> Username: vulptex  
> Created_at: 2022-02-20 19:38:51 UTC  
> Updated_at: 2022-02-21 11:43:04 UTC  
> Url: https://github.com/boostorg/regex/pull/171#issuecomment-1046306486  

@jzmaddock any chance to look at it?

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2022-03-07 16:33:37 UTC  
> Url: https://github.com/boostorg/regex/pull/171#issuecomment-1060885430  

Please see referenced PR above, thanks for the report!

---

## Comment 3

> Username: vulptex  
> Created_at: 2022-03-07 16:49:52 UTC  
> Url: https://github.com/boostorg/regex/pull/171#issuecomment-1060900725  

👍👌

---
