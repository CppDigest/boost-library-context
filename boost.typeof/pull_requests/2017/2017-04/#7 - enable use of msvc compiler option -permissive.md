# #7 enable use of msvc compiler option /permissive- [Merged]

> Username: DanielaE  
> Created at: 2017-04-15 06:49:19 UTC  
> Updated at: 2017-12-19 15:52:50 UTC  
> Merged at: 2017-04-16 15:06:24 UTC  
> Closed at: 2017-04-16 15:06:24 UTC  
> Url: https://github.com/boostorg/typeof/pull/7  

The compiler hack used to emulate __typeof__ on msvc no longer works when compiler option /permissive- is engaged. Rather than preying on a compiler bug take advantage of modern c++ instead.  
  
Signed-off-by: Daniela Engert <dani@ngrt.de>

---

## Comment 1

> Username: DanielaE  
> Created_at: 2017-04-15 06:52:07 UTC  
> Updated_at: 2017-04-15 08:36:51 UTC  
> Url: https://github.com/boostorg/typeof/pull/7#issuecomment-294276450  

This applies to msvc 14.1 from Visual Studio 2017.  
  
The full Boost test-suite passes with identical results as without this patch applied. This fixes ticket #12900.

---

## Review 2 [Commented]

> Username: Lastique  
> Created_at: 2017-04-15 09:55:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/typeof/pull/7#pullrequestreview-32924400  

📁 include/boost/typeof/msvc/typeof_impl_decltype.hpp

```diff
   9 |+ #include <boost/type_traits/is_function.hpp> 
  10 |+ #include <boost/type_traits/remove_reference.hpp> 
  11 |+ #include <boost/utility/enable_if.hpp>
```

> Username: Lastique  
> Created_at: 2017-04-15 09:55:06 UTC  
> Updated_at: 2017-04-16 06:53:53 UTC  
> Url: https://github.com/boostorg/typeof/pull/7#discussion_r111662235  

This header was moved to Boost.Core: `boost/core/enable_if.hpp`.

> Username: DanielaE  
> Created_at: 2017-04-15 11:36:06 UTC  
> Updated_at: 2017-04-16 06:53:53 UTC  
> Url: https://github.com/boostorg/typeof/pull/7#discussion_r111663600  

Ok, then this applies to three other header files in this library as well. But IMHO this should be addressed by a different pull-request.


---
