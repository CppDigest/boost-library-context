# #80 - result_of cannot handle member function pointer to __cdecl function [Open]

> Username: Kojoley  
> Created at: 2021-10-12 22:41:30 UTC  
> Updated at: 2021-10-12 22:41:30 UTC  
> Url: https://github.com/boostorg/utility/issues/80  

Might be a root cause of https://github.com/boostorg/phoenix/issues/81  
  
```cpp  
#include <functional>  
#include <boost/utility/result_of.hpp>  
  
struct X  
{  
    bool __cdecl f();  
};  
  
using F = decltype(&X::f);  
  
using A = std::result_of<F(X*)>::type;   // fine  
using B = boost::result_of<F(X*)>::type; // fails  
```  
  
https://godbolt.org/z/WGMKezqd7  
  
There should be much more of these calling convention stuff that will brakes result of, see https://github.com/boostorg/type_traits/blob/30396ed792697b5a5a7c4f02f7e22ae8d6ad95fd/include/boost/type_traits/detail/is_member_function_pointer_cxx_11.hpp
