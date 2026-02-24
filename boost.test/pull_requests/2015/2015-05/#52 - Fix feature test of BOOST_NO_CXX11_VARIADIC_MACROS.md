# #52 Fix feature test of BOOST_NO_CXX11_VARIADIC_MACROS [Merged]

> Username: jessicah  
> Created at: 2015-05-05 02:07:28 UTC  
> Updated at: 2015-05-05 07:31:40 UTC  
> Merged at: 2015-05-05 07:31:38 UTC  
> Closed at: 2015-05-05 07:31:38 UTC  
> Url: https://github.com/boostorg/test/pull/52  

Change b41a9354e9127e77d19a1b5c975c33729b033218 introduced regression failures due to preprocessor errors with respect to the && operator, leading to the following error:  
  
../boost/test/test_tools.hpp:50:87: error: operator '&&' has no right operand  
 #if !BOOST_PP_VARIADICS || ((__cplusplus >= 201103L) && BOOST_NO_CXX11_VARIADIC_MACROS)

---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2015-05-05 07:31:40 UTC  
> Url: https://github.com/boostorg/test/pull/52#issuecomment-98982637  

Applied, thanks!

---
