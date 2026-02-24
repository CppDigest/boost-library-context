# #190 - add is_bool [Open]

> Username: gpeterhoff  
> Created at: 2024-02-04 15:44:00 UTC  
> Updated at: 2024-02-05 10:30:38 UTC  
> Url: https://github.com/boostorg/type_traits/issues/190  

is_bool.hpp  
```  
//	Distributed under the Boost Software License Version 1.0 https://www.boost.org/LICENSE_1_0.txt  
//	Copyright Gero Peterhoff  
  
#ifndef BOOST_TYPE_TRAITS_IS_BOOL_HPP  
#define BOOST_TYPE_TRAITS_IS_BOOL_HPP  
  
#include <boost/type_traits/remove_cvref.hpp>  
#include <boost/type_traits/is_same.hpp>  
  
  
  
namespace boost  
{  
template <typename Type> struct is_bool : public integral_constant  
<  
	bool,  
	is_same<typename remove_cvref<Type>::type, bool>::value  
>	{};  
  
  
  
#if !defined(BOOST_NO_CXX17_INLINE_VARIABLES)  
template <typename Type> inline constexpr bool is_bool_v = is_bool<Type>::value;  
#endif  
}	//	boost  
  
#endif	//	BOOST_TYPE_TRAITS_IS_BOOL_HPP  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2024-02-04 18:00:03 UTC  
> Url: https://github.com/boostorg/type_traits/issues/190#issuecomment-1925865826  

Clearly we can add this, but in the many years of type_traits existence, no one has ever asked for it, looks a bit too trivial to me.

---

## Comment 2

> Username: gpeterhoff  
> Created at: 2024-02-04 19:45:22 UTC  
> Url: https://github.com/boostorg/type_traits/issues/190#issuecomment-1925894215  

It is therefore high time to make this available.  
I don't even want to know how many people have already programmed around it ...

---

## Comment 3

> Username: pdimov  
> Created at: 2024-02-05 10:30:37 UTC  
> Url: https://github.com/boostorg/type_traits/issues/190#issuecomment-1926668868  

The only use I can give for this (somewhat trivial) trait is that in Endian, a lot of the functions take "an integral type that is not bool".  
  
It only saves one `remove_cv` in comparison to `is_same<T, bool>`, but so does `is_void`.
