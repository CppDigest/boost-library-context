# #361 - Define BOOST_NO_CXX20_CHAR8_T [Open]

> Username: deadlocklogic  
> Created at: 2021-01-30 11:11:52 UTC  
> Updated at: 2021-01-30 11:20:12 UTC  
> Url: https://github.com/boostorg/config/issues/361  

Hi,  
Just a suggestion of defining `BOOST_NO_CXX20_CHAR8_T` like  
```C++  
#if !defined(__cpp_char8_t) || (__cpp_char8_t < 201811)  
#  define BOOST_NO_CXX20_CHAR8_T  
#endif  
```
