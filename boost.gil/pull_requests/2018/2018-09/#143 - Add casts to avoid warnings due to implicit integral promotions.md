# #143 Add casts to avoid warnings due to implicit integral promotions [Merged]

> Username: mloskot  
> Created at: 2018-09-24 09:14:31 UTC  
> Updated at: 2018-09-24 12:57:47 UTC  
> Merged at: 2018-09-24 12:57:45 UTC  
> Closed at: 2018-09-24 12:57:45 UTC  
> Url: https://github.com/boostorg/gil/pull/143  

The explicit casts help to avoid warnings when integer types smaller than `int` are implicitly converted during arithmetic operations.  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed  
  
-----  
  
This should fix warnings during compilation of the test:  
  
```  
promote_integral.cpp: In instantiation of ‘static void test_max_values<Integral, Promoted, Signed>::apply() [with Integral = signed char; Promoted = short int; bool Signed = true]’:  
promote_integral.cpp:157:65:   required from ‘static void test_promote_integral<PromoteUnsignedToUnsigned>::apply(const string&) [with Type = signed char; ExpectedPromotedType = short int; bool PromoteUnsignedToUnsigned = false; std::__cxx11::string = std::__cxx11::basic_string<char>]’  
promote_integral.cpp:222:45:   required from ‘static void test_promotion<T, PromoteUnsignedToUnsigned, IsSigned>::apply(std::__cxx11::string) [with T = signed char; bool PromoteUnsignedToUnsigned = false; bool IsSigned = true; std::__cxx11::string = std::__cxx11::basic_string<char>]’  
promote_integral.cpp:296:34:   required from here  
promote_integral.cpp:76:19: warning: conversion to ‘short int’ from ‘int’ may alter its value [-Wconversion]  
         min_value *= min_value;  
                   ^  
promote_integral.cpp:80:19: warning: conversion to ‘short int’ from ‘int’ may alter its value [-Wconversion]  
         max_value *= max_value;  
                   ^  
promote_integral.cpp: In instantiation of ‘static void test_max_values<Integral, Promoted, false>::apply() [with Integral = unsigned char; Promoted = short unsigned int]’:  
promote_integral.cpp:157:65:   required from ‘static void test_promote_integral<PromoteUnsignedToUnsigned>::apply(const string&) [with Type = unsigned char; ExpectedPromotedType = short unsigned int; bool PromoteUnsignedToUnsigned = true; std::__cxx11::string = std::__cxx11::basic_string<char>]’  
promote_integral.cpp:265:45:   required from ‘static void test_promotion<T, true, false>::apply(std::__cxx11::string) [with T = unsigned char; std::__cxx11::string = std::__cxx11::basic_string<char>]’  
promote_integral.cpp:299:42:   required from here  
promote_integral.cpp:98:46: warning: conversion to ‘short unsigned int’ from ‘int’ may alter its value [-Wconversion]  
         Promoted max_value_sqr = max_value * max_value;  
                                              ^  
promote_integral.cpp: In instantiation of ‘static T absolute_value<T, Signed>::apply(const T&) [with T = short int; bool Signed = true]’:  
promote_integral.cpp:77:9:   required from ‘static void test_max_values<Integral, Promoted, Signed>::apply() [with Integral = char; Promoted = short int; bool Signed = true]’  
promote_integral.cpp:157:65:   required from ‘static void test_promote_integral<PromoteUnsignedToUnsigned>::apply(const string&) [with Type = char; ExpectedPromotedType = short int; bool PromoteUnsignedToUnsigned = false; std::__cxx11::string = std::__cxx11::basic_string<char>]’  
promote_integral.cpp:222:45:   required from ‘static void test_promotion<T, PromoteUnsignedToUnsigned, IsSigned>::apply(std::__cxx11::string) [with T = char; bool PromoteUnsignedToUnsigned = false; bool IsSigned = true; std::__cxx11::string = std::__cxx11::basic_string<char>]’  
promote_integral.cpp:294:27:   required from here  
promote_integral.cpp:49:29: warning: conversion to ‘short int’ from ‘int’ may alter its value [-Wconversion]  
         return t < 0 ? -t : t;  
                             ^  
```

---

## Review 1 [Approved]

> Username: stefanseefeld  
> Created_at: 2018-09-24 11:44:42 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/143#pullrequestreview-158066221  

Thanks !

---
