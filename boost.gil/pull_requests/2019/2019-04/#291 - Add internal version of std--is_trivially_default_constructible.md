# #291 Add internal version of std::is_trivially_default_constructible [Merged]

> Username: mloskot  
> Created at: 2019-04-16 21:51:34 UTC  
> Updated at: 2019-04-18 15:31:07 UTC  
> Merged at: 2019-04-18 15:30:50 UTC  
> Closed at: 2019-04-18 15:30:50 UTC  
> Url: https://github.com/boostorg/gil/pull/291  

Add similar for `std::is_trivially_destructible`.  
This should help to restore support for GCC<5 (credit to @pdimov).  
  
### References  
  
* Partially fixes #282  
  
### Tasklist  
  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2019-04-16 22:04:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/291#pullrequestreview-227468377  

📁 include/boost/gil/detail/type_traits.hpp

```diff
   9 |+ #define BOOST_GIL_DETAIL_TYPE_TRAITS_HPP
  10 |+ 
  11 |+ #include <type_traits>
```

> Username: pdimov  
> Created_at: 2019-04-16 22:04:12 UTC  
> Updated_at: 2019-04-18 13:12:35 UTC  
> Url: https://github.com/boostorg/gil/pull/291#discussion_r276011354  

You also need `boost/config.hpp` here, for `BOOST_LIBSTDCXX_VERSION`.

> Username: mloskot  
> Created_at: 2019-04-16 23:27:36 UTC  
> Updated_at: 2019-04-18 13:12:35 UTC  
> Url: https://github.com/boostorg/gil/pull/291#discussion_r276030000  

Thanks. It should be fixed now.


---

## Review 2 [Commented]

> Username: stefanseefeld  
> Created_at: 2019-04-18 04:03:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/291#pullrequestreview-228092730  

📁 include/boost/gil/algorithm.hpp

```diff
 443 |             <
 443 |-                 std::is_trivially_destructible<typename std::iterator_traits<Iterator>>
 444 |+                 detail::is_trivially_destructible<typename std::iterator_traits<Iterator>>
```

> Username: stefanseefeld  
> Created_at: 2019-04-18 04:03:50 UTC  
> Updated_at: 2019-04-18 13:12:35 UTC  
> Url: https://github.com/boostorg/gil/pull/291#discussion_r276510975  

What is the `typename` doing in the above template argument ? It looks wrong...

> Username: mloskot  
> Created_at: 2019-04-18 13:13:18 UTC  
> Updated_at: 2019-04-18 13:13:19 UTC  
> Url: https://github.com/boostorg/gil/pull/291#discussion_r276656555  

Well spotted! Fixed in separate 5af7a959f8dfa94011037597a9a2a880997dd34b and updated the PR.


---

## Comment 3

> Username: mloskot  
> Created_at: 2019-04-18 15:30:20 UTC  
> Url: https://github.com/boostorg/gil/pull/291#issuecomment-484562430  

AzP seem to be having some infrastructure/network troubles failing VS2015 build. The same build is fine on AppVeyor. Merging.

---
