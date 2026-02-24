# #189 - add remove_cvptr [Open]

> Username: gpeterhoff  
> Created at: 2024-02-04 15:35:53 UTC  
> Updated at: 2024-02-05 11:00:41 UTC  
> Url: https://github.com/boostorg/type_traits/issues/189  

like remove_cvref  
remove_cvptr.hpp  
```  
//	Distributed under the Boost Software License Version 1.0 https://www.boost.org/LICENSE_1_0.txt  
//	Copyright Gero Peterhoff  
  
#ifndef BOOST_TYPE_TRAITS_REMOVE_CVPTR_HPP  
#define BOOST_TYPE_TRAITS_REMOVE_CVPTR_HPP  
  
#include <boost/type_traits/remove_pointer.hpp>  
#include <boost/type_traits/remove_cv.hpp>  
  
  
  
namespace boost  
{  
template <typename Type>  
struct remove_cvptr  
{  
	typedef typename remove_cv<typename remove_pointer<Type>::type>::type type;  
};  
  
#if !defined(BOOST_NO_CXX11_TEMPLATE_ALIASES)  
template <typename Type> using remove_cvptr_t = typename remove_cvptr<Type>::type;  
#endif  
}	//	boost  
  
#endif	//	BOOST_TYPE_TRAITS_REMOVE_CVPTR_HPP  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2024-02-05 11:00:40 UTC  
> Url: https://github.com/boostorg/type_traits/issues/189#issuecomment-1926720962  

This doesn't seem particularly useful to me.
