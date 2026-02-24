# #30 - Compilation error under c++20 with constexpr [Closed]

> Username: mxp1983  
> Created at: 2022-08-11 06:24:11 UTC  
> Updated at: 2022-11-01 22:34:00 UTC  
> Closed at: 2022-11-01 22:34:00 UTC  
> Url: https://github.com/boostorg/static_string/issues/30  

Hello,  
  
I have compilation errors for the following code:  
```  
#include <iostream>  
#include <boost/static_string.hpp>  
  
constexpr auto getString()  
{  
    boost::static_strings::static_string<1024> content;  
    content.append("Hello world");  
    return content;  
}  
  
constexpr auto msg = getString();  
  
int main()  
{  
    std::cout << msg.c_str() << std::endl;  
    return 0;  
}  
```  
  
The code fails to compile under:  
* clang 13.1.6  
* VS2022 17.2.6  
  
The walk around I've found is to allow an array initialisation of `static_string_base` class, meaning replacing:  
```  
#ifdef BOOST_STATIC_STRING_CPP20  
    value_type data_[N + 1];  
#else  
    value_type data_[N + 1] {};  
#endif  
```  
with  
```  
    value_type data_[N + 1] {};  
```

---

## Comment 1

> Username: beached  
> Created at: 2022-08-11 12:22:47 UTC  
> Updated at: 2022-08-11 12:23:36 UTC  
> Url: https://github.com/boostorg/static_string/issues/30#issuecomment-1211916851  

It looks like the issue is that boost config is using `BOOST_STATIC_STRING_STANDARD_VERSION` to determine if `BOOST_STATIC_STRING_CPP20` is defined, which is essentially __cplusplus with fixes for MSVC.  This does not tell us that the compiler supports anything in C++20 other than the -std=c++20 flag has been set.  I think for the init stuff in C++20, there is a feature flag set by the compiler `__cpp_constexpr_dynamic_alloc >= 201907L`, but not 100% that is the correct flag.

---

## Comment 2

> Username: WPMGPRoSToTeMa  
> Created at: 2022-10-20 01:44:51 UTC  
> Url: https://github.com/boostorg/static_string/issues/30#issuecomment-1284793018  

@beached it should be `__cpp_constexpr >= 201907L`.  
https://en.cppreference.com/w/cpp/feature_test

---

## Comment 3

> Username: beached  
> Created at: 2022-10-20 01:56:55 UTC  
> Url: https://github.com/boostorg/static_string/issues/30#issuecomment-1284801356  

Ah ok, I see now.  I thought it might have came with the allocator support in [More Constexpr Containers](https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2019/p0784r7.html), I didn't know about [Permitting trivial default initialization in constexpr contexts](https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2019/p1331r2.pdf)

---

## Comment 4

> Username: alandefreitas  
> Created at: 2022-11-01 22:33:49 UTC  
> Url: https://github.com/boostorg/static_string/issues/30#issuecomment-1299312815  

This should have been fixed in #32
