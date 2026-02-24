# #21 Do not include container_hash if possible [Merged]

> Username: Kojoley  
> Created at: 2018-10-20 15:32:39 UTC  
> Updated at: 2018-10-20 17:20:31 UTC  
> Merged at: 2018-10-20 17:12:07 UTC  
> Closed at: 2018-10-20 17:12:07 UTC  
> Url: https://github.com/boostorg/type_index/pull/21  

The `boost/container_hash/hash.hpp` has significant inclusion times.  
Not including it results in 30% faster `boost/type_traits.hpp` parsing.  
(kind of regression introduced in 82ed01657794eeafb5452cb36ab9291e97c0978d)

---

## Review 1 [Changes requested]

> Username: apolukhin  
> Created_at: 2018-10-20 16:55:41 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/type_index/pull/21#pullrequestreview-166755280  

📁 include/boost/type_index/stl_type_index.hpp

```diff
  43 |+     || (defined(__GNUC__) && __GNUC__ == 4 && __GNUC_MINOR__ > 5 && defined(__GXX_EXPERIMENTAL_CXX0X__)) \
  44 |+     || (defined(__GNUC__) && __GNUC__ > 4 && __cplusplus >= 201103)
  45 |+ #   define BOOST_CLASSINFO_STD_TYPE_INDEX_HAS_HASH_CODE
```

> Username: apolukhin  
> Created_at: 2018-10-20 16:55:29 UTC  
> Updated_at: 2018-10-20 17:00:03 UTC  
> Url: https://github.com/boostorg/type_index/pull/21#discussion_r226832997  

Could you please change the macro name to start with `BOOST_TYPE_INDEX_`? I'm afraid that some day there will be a Boost.Classinfo library and a collision could happen.

> Username: Kojoley  
> Created_at: 2018-10-20 16:58:29 UTC  
> Updated_at: 2018-10-20 17:00:03 UTC  
> Url: https://github.com/boostorg/type_index/pull/21#discussion_r226833109  

Yes, sure. I was unsure which prefix to use and copied one from the other define.

> Username: apolukhin  
> Created_at: 2018-10-20 17:15:16 UTC  
> Url: https://github.com/boostorg/type_index/pull/21#discussion_r226833619  

Oh... I have to fix that. Thanks fot the heads up!

> Username: apolukhin  
> Created_at: 2018-10-20 17:20:31 UTC  
> Url: https://github.com/boostorg/type_index/pull/21#discussion_r226833762  

Fixed.


---

## Comment 2

> Username: apolukhin  
> Created_at: 2018-10-20 17:12:27 UTC  
> Url: https://github.com/boostorg/type_index/pull/21#issuecomment-431600332  

Many thanks!

---
