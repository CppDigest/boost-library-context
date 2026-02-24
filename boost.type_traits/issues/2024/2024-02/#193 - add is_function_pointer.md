# #193 - add is_function_pointer [Open]

> Username: gpeterhoff  
> Created at: 2024-02-04 16:16:56 UTC  
> Updated at: 2024-02-04 16:16:56 UTC  
> Url: https://github.com/boostorg/type_traits/issues/193  

is_function_pointer like is_member_function_pointer  
```  
//	Distributed under the Boost Software License Version 1.0 https://www.boost.org/LICENSE_1_0.txt  
//	Copyright Gero Peterhoff  
  
#ifndef BOOST_TYPE_TRAITS_IS_FUNCTION_POINTER_HPP  
#define BOOST_TYPE_TRAITS_IS_FUNCTION_POINTER_HPP  
  
#include <boost/type_traits/remove_pointer.hpp>  
#include <boost/type_traits/is_function.hpp>  
#include <boost/type_traits/is_pointer.hpp>  
  
  
  
namespace boost  
{  
template <typename Type>  
struct is_function_pointer : public integral_constant  
<  
	bool,  
	is_pointer<Type>::value && is_function<typename remove_pointer<Type>::type>::value  
>	{};  
  
#if !defined(BOOST_NO_CXX17_INLINE_VARIABLES)  
template <typename Type> inline constexpr bool is_function_pointer_v = is_function_pointer<Type>::value;  
#endif  
}	//	boost  
  
#endif	//	BOOST_TYPE_TRAITS_IS_FUNCTION_POINTER_HPP  
```
