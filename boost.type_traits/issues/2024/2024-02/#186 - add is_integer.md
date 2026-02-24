# #186 - add is_integer [Closed]

> Username: gpeterhoff  
> Created at: 2024-02-04 15:21:53 UTC  
> Updated at: 2025-08-31 16:32:59 UTC  
> Closed at: 2024-02-04 17:38:26 UTC  
> Url: https://github.com/boostorg/type_traits/issues/186  

is_integer.hpp  
```  
//	Distributed under the Boost Software License Version 1.0 https://www.boost.org/LICENSE_1_0.txt  
//	Copyright Gero Peterhoff  
  
#ifndef BOOST_TYPE_TRAITS_IS_INTEGER_HPP  
#define BOOST_TYPE_TRAITS_IS_INTEGER_HPP  
  
#include <boost/type_traits/remove_cvref.hpp>  
#include <boost/type_traits/is_unsigned.hpp>  
#include <boost/type_traits/is_signed.hpp>  
#include <boost/type_traits/is_same.hpp>  
#include <boost/cstdint.hpp>  
  
  
namespace boost  
{  
template <typename Type> struct is_integer			: public false_type{};  
template <typename Type> struct is_integer<volatile const Type>	: public is_integer<Type>{};  
template <typename Type> struct is_integer<volatile Type>	: public is_integer<Type>{};  
template <typename Type> struct is_integer<const Type>		: public is_integer<Type>{};  
  
#if defined(BOOST_HAS_INT128)  
template <> struct is_integer<boost::uint128_type>	: public true_type{};  
template <> struct is_integer<boost::int128_type>	: public true_type{};  
#endif  
  
template <> struct is_integer<uint64_t>	: public true_type{};  
template <> struct is_integer<int64_t>	: public true_type{};  
  
template <> struct is_integer<uint32_t>	: public true_type{};  
template <> struct is_integer<int32_t>	: public true_type{};  
  
template <> struct is_integer<uint16_t>	: public true_type{};  
template <> struct is_integer<int16_t>	: public true_type{};  
  
template <> struct is_integer<uint8_t>	: public true_type{};  
template <> struct is_integer<int8_t>	: public true_type{};  
  
  
  
  
template <typename Type> struct is_unsigned_integer : public integral_constant  
<  
	bool,  
	is_integer<Type>::value && is_unsigned<Type>::value  
>	{};  
  
template <typename Type> struct is_signed_integer : public integral_constant  
<  
	bool,  
	is_integer<Type>::value && is_signed<Type>::value  
>	{};  
  
  
  
#if !defined(BOOST_NO_CXX17_INLINE_VARIABLES)  
template <typename Type> inline constexpr bool is_unsigned_integer_v	= is_unsigned_integer<Type>::value;  
template <typename Type> inline constexpr bool is_signed_integer_v	= is_signed_integer<Type>::value;  
template <typename Type> inline constexpr bool is_integer_v		= is_integer<Type>::value;  
#endif  
}	//	boost  
  
#endif	//	BOOST_TYPE_TRAITS_IS_INTEGER_HPP  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2024-02-04 17:38:26 UTC  
> Url: https://github.com/boostorg/type_traits/issues/186#issuecomment-1925854911  

This is effectively https://www.boost.org/doc/libs/1_84_0/libs/type_traits/doc/html/boost_typetraits/reference/is_integral.html, yes I realise you have a slightly different definition of what constitutes an integer, but was never our intention to provide super-fine grained categorisation.

---

## Comment 2

> Username: gpeterhoff  
> Created at: 2024-02-04 18:35:54 UTC  
> Updated at: 2024-02-04 20:10:21 UTC  
> Url: https://github.com/boostorg/type_traits/issues/186#issuecomment-1925875873  

That's exactly my point.  
You haven't thought about it, it doesn't exist in the standard.  
There are various use cases for this.  
  
please open this issues

---

## Comment 3

> Username: pdimov  
> Created at: 2024-02-05 10:24:21 UTC  
> Url: https://github.com/boostorg/type_traits/issues/186#issuecomment-1926657331  

The concept "integer type" does exist in the standard (but not with the above definition which is incorrect). See https://eel.is/c++draft/basic.fundamental#1 and https://eel.is/c++draft/basic.fundamental#2.  
  
Then https://eel.is/c++draft/basic.fundamental#11 muddies the water somewhat, but I think that "signed integer type" + "unsigned integer type" is a better definition for "integer type".  
  
Currently the best approximation of the above is `std::numeric_limits<T>::is_integer", e.g. https://github.com/boostorg/core/blob/0a35bb6a20bd13e85ff492a5be01b3894807a0d1/include/boost/core/bit.hpp#L299.

---

## Comment 4

> Username: gpeterhoff  
> Created at: 2025-08-31 15:11:49 UTC  
> Url: https://github.com/boostorg/type_traits/issues/186#issuecomment-3240213462  

See https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2025/p3701r0.html

---

## Comment 5

> Username: pdimov  
> Created at: 2025-08-31 15:20:02 UTC  
> Url: https://github.com/boostorg/type_traits/issues/186#issuecomment-3240218166  

I agree with everything P3701 proposes.

---

## Comment 6

> Username: gpeterhoff  
> Created at: 2025-08-31 16:32:59 UTC  
> Url: https://github.com/boostorg/type_traits/issues/186#issuecomment-3240259968  

Okay. So is_integer is still provided in boost::type_traits?
