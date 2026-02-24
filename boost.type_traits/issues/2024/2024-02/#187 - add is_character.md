# #187 - add is_character [Open]

> Username: gpeterhoff  
> Created at: 2024-02-04 15:27:17 UTC  
> Updated at: 2024-02-05 10:28:16 UTC  
> Url: https://github.com/boostorg/type_traits/issues/187  

is_character.hpp  
```  
//	Distributed under the Boost Software License Version 1.0 https://www.boost.org/LICENSE_1_0.txt  
//	Copyright Gero Peterhoff  
  
#ifndef BOOST_TYPE_TRAITS_IS_CHARACTER_HPP  
#define BOOST_TYPE_TRAITS_IS_CHARACTER_HPP  
  
#include <boost/type_traits/integral_constant.hpp>  
#include <boost/type_traits/remove_cvref.hpp>  
  
  
  
namespace boost  
{  
template <typename Type> struct is_character				: public false_type{};  
template <typename Type> struct is_character<volatile const Type>	: public is_character<Type>{};  
template <typename Type> struct is_character<volatile Type>		: public is_character<Type>{};  
template <typename Type> struct is_character<const Type>		: public is_character<Type>{};  
  
#if defined(__cpp_unicode_characters)  
template <> struct is_character<char32_t>	: public true_type{};  
template <> struct is_character<char16_t>	: public true_type{};  
#endif  
  
#if defined(__cpp_char8_t)  
template <> struct is_character<char8_t>	: public true_type{};  
#endif  
  
template <> struct is_character<wchar_t>	: public true_type{};  
template <> struct is_character<char>		: public true_type{};  
  
  
  
#if !defined(BOOST_NO_CXX17_INLINE_VARIABLES)  
template <typename Type> inline constexpr bool is_character_v = is_character<Type>::value;  
#endif  
}	//	boost  
  
#endif	//	BOOST_TYPE_TRAITS_IS_CHARACTER_HPP  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2024-02-05 10:28:15 UTC  
> Url: https://github.com/boostorg/type_traits/issues/187#issuecomment-1926664690  

Character types _are_ a standard concept, defined in https://eel.is/c++draft/basic.fundamental#11, and the above looks like the correct implementation for it.  
  
This trait is occasionally useful to constrain functions taking C strings, e.g. https://github.com/boostorg/nowide/blob/e040930b0986d5a4b89d2b9b109a6233c2baa831/include/boost/nowide/detail/is_string_container.hpp#L26
