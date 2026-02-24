# #191 - add is_quotrem_order [Open]

> Username: gpeterhoff  
> Created at: 2024-02-04 15:57:38 UTC  
> Updated at: 2024-02-04 19:57:46 UTC  
> Url: https://github.com/boostorg/type_traits/issues/191  

Problem:  
std::Xdiv_t can be implemented as {quot, rem} or {rem, quot} (what a mess). This means that structured binding are not unique:  
auto [a, b] = std::div(x, y);  
is a quot or rem or is b quot or rem ?  
is_quotrem_order.hpp  
```  
//	Distributed under the Boost Software License Version 1.0 https://www.boost.org/LICENSE_1_0.txt  
//	Copyright Gero Peterhoff  
  
#ifndef BOOST_TYPE_TRAITS_IS_QUOTREM_ORDER_HPP  
#define BOOST_TYPE_TRAITS_IS_QUOTREM_ORDER_HPP  
  
#include <boost/type_traits/is_bool.hpp>  
#include <type_traits>  
#include <cmath>  
  
  
  
namespace boost  
{  
namespace detail  
{  
template <typename Type>  
inline constexpr bool	is_quotrem_order()	noexcept  
{  
	static_assert(std::is_arithmetic<Type>::value, "invalid type");  
  
	//	std::div need signed integer  
	using value_type = typename std::conditional_t  
		<  
			std::is_floating_point<Type>::value || boost::is_bool<Type>::value,  
			int,  
			typename std::make_signed<Type>::type  
		>;  
	using div_type = decltype(std::div(value_type{}, value_type{}));  
  
	constexpr div_type  
		div{value_type{0}, value_type{1}};  
  
	return div.quot == value_type{0};  
}  
}	//	detail  
  
  
  
template <typename Type> struct is_quotrem_order : public std::integral_constant  
<  
	bool,  
	detail::is_quotrem_order<Type>()  
>	{};  
  
  
#if !defined(BOOST_NO_CXX17_INLINE_VARIABLES)  
template <typename Type> inline constexpr bool is_quotrem_order_v = is_quotrem_order<Type>::value;  
#endif  
}	//	boost  
  
#endif	//	BOOST_TYPE_TRAITS_IS_QUOTREM_ORDER_HPP  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2024-02-04 17:58:33 UTC  
> Url: https://github.com/boostorg/type_traits/issues/191#issuecomment-1925865370  

I think this may be overkill: std::div is constexpr from C++23 only making this solution require C++23 as well, but off the top of my head a neater solution might be:  
  
```  
template <class D>  
auto div_to_tuple(const D& d)  
{  
    return std::make_tuple(d.quot, d.rem);  
}  
```  
  
So now you can write:  
  
```  
auto [q,r] = div_to_tuple(std::div(a,b));  
```  
  
and this works from C++11 onwards I think.

---

## Comment 2

> Username: gpeterhoff  
> Created at: 2024-02-04 19:57:45 UTC  
> Url: https://github.com/boostorg/type_traits/issues/191#issuecomment-1925897145  

No. There is std::div. Nothing more. Unfortunately, I can't tell you why the C++ committee is not able to do this in a standardized way, let alone provide std::div for unsigned.
