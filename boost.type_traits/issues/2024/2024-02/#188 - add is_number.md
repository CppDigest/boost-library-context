# #188 - add is_number [Closed]

> Username: gpeterhoff  
> Created at: 2024-02-04 15:30:53 UTC  
> Updated at: 2024-02-05 10:59:15 UTC  
> Closed at: 2024-02-04 17:39:18 UTC  
> Url: https://github.com/boostorg/type_traits/issues/188  

is_number.hpp  
```  
//	Distributed under the Boost Software License Version 1.0 https://www.boost.org/LICENSE_1_0.txt  
//	Copyright Gero Peterhoff  
  
#ifndef BOOST_TYPE_TRAITS_IS_NUMBER_HPP  
#define BOOST_TYPE_TRAITS_IS_NUMBER_HPP  
  
#include <boost/type_traits/is_floating_point.hpp>  
#include <boost/type_traits/is_integer.hpp>  
  
  
  
namespace boost  
{  
template <typename Type>  
struct is_number : public integral_constant  
<  
	bool,  
	is_floating_point<Type>::value || is_integer<Type>::value  
>	{};  
  
  
  
#if !defined(BOOST_NO_CXX17_INLINE_VARIABLES)  
template <typename Type> inline constexpr bool is_number_v = is_number<Type>::value;  
#endif  
}	//	boost  
  
#endif	//	BOOST_TYPE_TRAITS_IS_NUMBER_HPP  
  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2024-02-04 17:39:18 UTC  
> Url: https://github.com/boostorg/type_traits/issues/188#issuecomment-1925855287  

This is spelled is_arithmetic.

---

## Comment 2

> Username: gpeterhoff  
> Created at: 2024-02-04 18:39:50 UTC  
> Updated at: 2024-02-04 20:11:35 UTC  
> Url: https://github.com/boostorg/type_traits/issues/188#issuecomment-1925876919  

No! is_arithmetic contains characters and bool.  
  
please open this issues.

---

## Comment 3

> Username: pdimov  
> Created at: 2024-02-05 10:59:14 UTC  
> Url: https://github.com/boostorg/type_traits/issues/188#issuecomment-1926718441  

If added should probably be `is_numeric`.
