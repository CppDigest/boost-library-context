# #194 - add dependent_false [Open]

> Username: gpeterhoff  
> Created at: 2024-02-04 16:27:24 UTC  
> Updated at: 2024-02-04 18:06:56 UTC  
> Url: https://github.com/boostorg/type_traits/issues/194  

always false idiome  
dependent_false.hpp  
```  
//	Distributed under the Boost Software License Version 1.0 https://www.boost.org/LICENSE_1_0.txt  
//	Copyright Gero Peterhoff  
  
#ifndef BOOST_TYPE_TRAITS_DEPENDENT_FALSE_HPP  
#define BOOST_TYPE_TRAITS_DEPENDENT_FALSE_HPP  
  
#include <boost/type_traits/integral_constant.hpp>  
  
  
  
namespace boost  
{  
template <typename... Types> struct dependent_false : public false_type{};  
  
  
#if !defined(BOOST_NO_CXX17_INLINE_VARIABLES)  
template <typename... Types> inline constexpr bool dependent_false_v = dependent_false<Types...>::value;  
#endif  
}	//	boost  
  
#endif	//	BOOST_TYPE_TRAITS_DEPENDENT_FALSE_HPP  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2024-02-04 17:43:51 UTC  
> Url: https://github.com/boostorg/type_traits/issues/194#issuecomment-1925860261  

Use case?

---

## Comment 2

> Username: gpeterhoff  
> Created at: 2024-02-04 18:05:50 UTC  
> Updated at: 2024-02-04 18:06:19 UTC  
> Url: https://github.com/boostorg/type_traits/issues/194#issuecomment-1925867549  

https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2022/p2593r0.html  
  
https://github.com/cplusplus/papers/issues/572
