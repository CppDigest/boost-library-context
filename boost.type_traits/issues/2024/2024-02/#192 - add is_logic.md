# #192 - add is_logic [Closed]

> Username: gpeterhoff  
> Created at: 2024-02-04 16:11:52 UTC  
> Updated at: 2024-02-04 20:12:09 UTC  
> Closed at: 2024-02-04 17:42:09 UTC  
> Url: https://github.com/boostorg/type_traits/issues/192  

What do I mean?  
bool is always a logic type, of course. But there can be more logic types, e.g. boost::triboool.  
is_logic must be adapted in the relevant libraries.  
is_logic.hpp  
```  
//	Distributed under the Boost Software License Version 1.0 https://www.boost.org/LICENSE_1_0.txt  
//	Copyright Gero Peterhoff  
  
#ifndef BOOST_TYPE_TRAITS_IS_LOGIC_HPP  
#define BOOST_TYPE_TRAITS_IS_LOGIC_HPP  
  
#include <boost/type_traits/is_bool.hpp>  
#include <type_traits>  
  
  
  
namespace boost  
{  
template <typename Type>  
struct is_logic : public integral_constant  
<  
	bool,  
	is_bool<Type>::value  
>	{};  
  
  
  
#if !defined(BOOST_NO_CXX17_INLINE_VARIABLES)  
template <typename Type> inline constexpr bool is_logic_v = is_logic<Type>::value;  
#endif  
}	//	boost  
  
#endif	//	BOOST_TYPE_TRAITS_IS_LOGIC_HPP  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2024-02-04 17:42:09 UTC  
> Url: https://github.com/boostorg/type_traits/issues/192#issuecomment-1925858700  

This is not what type_traits is for - we do not provide specialization points for other libraries, we introspect the type system, nothing more or less.

---

## Comment 2

> Username: gpeterhoff  
> Created at: 2024-02-04 19:20:53 UTC  
> Updated at: 2024-02-04 20:12:09 UTC  
> Url: https://github.com/boostorg/type_traits/issues/192#issuecomment-1925887882  

tribool is a component of boost. As I wrote, such libraries must of course set is_logic (to true) themselves. But first a basis has to be created to represent this.  
As I wrote ages ago, I would like to provide classes for 4-value logic https://en.wikipedia.org/wiki/Many-valued_logic#Belnap_logic_(B4) and for 3-value logic (which could replace the old tribool).  
These would of course contain an is_logic.  
Now you have to decide:  
- is_logic is **not** implemented  
you are stuck with the stone old code (tribool), without the possibility to extend it  
- is_logic is implemented   
various logic classes can be categorized (there are even more than 2-, 3- and 4-value logics)  
  
please open this issues.
