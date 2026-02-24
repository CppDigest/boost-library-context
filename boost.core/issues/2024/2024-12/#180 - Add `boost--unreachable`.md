# #180 - Add `boost::unreachable` [Open]

> Username: denzor200  
> Created at: 2024-12-13 19:14:03 UTC  
> Updated at: 2024-12-13 19:14:03 UTC  
> Url: https://github.com/boostorg/core/issues/180  

C++11 users need an analogue of `std::unreachable`. Might be easily implemented in the way cppreference suggests:  
```  
[[noreturn]] inline void unreachable()  
{  
#if defined(_MSC_VER) && !defined(__clang__) // MSVC  
    __assume(false);  
#else // GCC, Clang  
    __builtin_unreachable();  
#endif  
}  
```  
  
https://en.cppreference.com/w/cpp/utility/unreachable
