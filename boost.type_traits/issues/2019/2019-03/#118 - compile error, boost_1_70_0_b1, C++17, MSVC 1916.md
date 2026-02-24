# #118 - compile error, boost_1_70_0_b1, C++17, MSVC 1916 [Closed]

> Username: Cry-Thomas  
> Created at: 2019-03-20 19:47:07 UTC  
> Updated at: 2019-03-22 19:26:08 UTC  
> Closed at: 2019-03-21 10:23:34 UTC  
> Url: https://github.com/boostorg/type_traits/issues/118  

we found Boost version 1.70.0 b1 not compiling against our code base with C++17 and MSVC version 1916 in conjunction with the `/Zc:noexceptTypes-` compiler flag. Resulting error is of type:  
  
```  
boost\type_traits\detail\is_member_function_pointer_cxx_11.hpp(359): error C2953: 'boost::is_member_function_pointer<Ret(__cdecl C::* )(Args...)>': class template has already been defined  
boost\type_traits\detail\is_member_function_pointer_cxx_11.hpp(48): note: see declaration of 'boost::is_member_function_pointer<Ret(__cdecl C::* )(Args...)>'  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2019-03-20 23:47:18 UTC  
> Url: https://github.com/boostorg/type_traits/issues/118#issuecomment-475070764  

It's enough to `#include <boost/type_traits/is_function.hpp>` for the error to occur.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2019-03-21 10:24:23 UTC  
> Url: https://github.com/boostorg/type_traits/issues/118#issuecomment-475177020  

I've just pushed a fix for this, but a temporary workaround is to add -DBOOST_TT_NO_NOEXCEPT_SEPARATE_TYPE to the command line.

---

## Comment 3

> Username: Cry-Thomas  
> Created at: 2019-03-21 10:43:54 UTC  
> Updated at: 2019-03-21 10:44:38 UTC  
> Url: https://github.com/boostorg/type_traits/issues/118#issuecomment-475182588  

>   
>   
> I've just pushed a fix for this, but a temporary workaround is to add -DBOOST_TT_NO_NOEXCEPT_SEPARATE_TYPE to the command line.  
  
Thanks for the swift action! Can you comment on whether this will be part of the final 1.70.0 release?

---

## Comment 4

> Username: pdimov  
> Created at: 2019-03-21 13:29:13 UTC  
> Url: https://github.com/boostorg/type_traits/issues/118#issuecomment-475228526  

MSVC 2017 also has its own macro for noexcept function types (`_NOEXCEPT_TYPES_SUPPORTED`), that predates their implementing the SD6 macros: https://github.com/boostorg/bind/commit/070185914d721e8bc67e9585f4a33b963d67b741

---

## Comment 5

> Username: jzmaddock  
> Created at: 2019-03-22 19:26:08 UTC  
> Url: https://github.com/boostorg/type_traits/issues/118#issuecomment-475750536  

>MSVC 2017 also has its own macro for noexcept function types (_NOEXCEPT_TYPES_SUPPORTED), that predates their implementing the SD6 macros: boostorg/bind@0701859  
  
Thanks, support added.
