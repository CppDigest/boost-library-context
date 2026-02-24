# #121 - Parse and Print Hexadecimal floating point literals? [Open]

> Username: NAThompson  
> Created at: 2019-03-02 18:09:29 UTC  
> Updated at: 2020-06-22 12:06:40 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/121  

Would it be nice to have the ability to parse and print hexadecimal floating point literals in multprecision? float128 can parse as a hexadecimal, but not print (maybe a quadmath enhancement rather than Boost.Multiprecision?) but `cpp_bin_float_50` can neither parse nor print.  
  
```cpp  
#include <iostream>  
#include <boost/multiprecision/cpp_bin_float.hpp>  
#include <boost/multiprecision/float128.hpp>  
  
using boost::multiprecision::cpp_bin_float_50;  
using boost::multiprecision::float128;  
  
int main()  
{  
    float128 x = 7;  
    std::cout << std::hexfloat << x << "\n";  
    cpp_bin_float_50 y = 7;  
  
    std::cout << std::hexfloat << y << "\n";  
    float128 z = 0x1p1Q;  
  
    std::cout << "z = " << z << "\n";  
    cpp_bin_float_50 w{"0x1p1"};  
}  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-03-02 18:24:22 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/121#issuecomment-468946517  

It would indeed be nice, we have an open request for it somewhere I think (ah it's here: https://svn.boost.org/trac10/ticket/12069).  
  
Fancy writing this?  ;)

---

## Comment 2

> Username: NAThompson  
> Created at: 2019-03-02 18:37:12 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/121#issuecomment-468947651  

I actually have been thinking about getting into the multiprecision codebase, and this looks like an achievable first task.  
  
Lemme take a look and if I get bogged down I'll holler . . .

---

## Comment 3

> Username: ambreshbiradar9  
> Created at: 2020-06-21 20:10:14 UTC  
> Updated at: 2020-06-21 20:14:18 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/121#issuecomment-647175635  

> Would it be nice to have the ability to parse and print hexadecimal floating point literals in multprecision? float128 can parse as a hexadecimal, but not print (maybe a quadmath enhancement rather than Boost.Multiprecision?) but `cpp_bin_float_50` can neither parse nor print.  
>   
> ```c++  
> #include <iostream>  
> #include <boost/multiprecision/cpp_bin_float.hpp>  
> #include <boost/multiprecision/float128.hpp>  
>   
> using boost::multiprecision::cpp_bin_float_50;  
> using boost::multiprecision::float128;  
>   
> int main()  
> {  
>     float128 x = 7;  
>     std::cout << std::hexfloat << x << "\n";  
>     cpp_bin_float_50 y = 7;  
>   
>     std::cout << std::hexfloat << y << "\n";  
>     float128 z = 0x1p1Q;  
>   
>     std::cout << "z = " << z << "\n";  
>     cpp_bin_float_50 w{"0x1p1"};  
> }  
> ```  
  
C:\..\..\boost/detail/iterator.hpp(13): note: This header is deprecated. Use <iterator> instead.  
C:\..\..\boost/multiprecision/float128.hpp(31): fatal error C1189: #error:  "Sorry compiler is neither GCC, not Intel, don't know how to configure this header."  
  
I used MSVC compiler

---

## Comment 4

> Username: NAThompson  
> Created at: 2020-06-21 20:29:17 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/121#issuecomment-647177756  

@ambreshbiradar9 : `float128` is only available with `gcc` and `intel` compiler. But I've already implemented the `float128` printing and parsing. So just remove the `float128` lines and everything should work.

---

## Comment 5

> Username: pabristow  
> Created at: 2020-06-22 12:03:37 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/121#issuecomment-647473795  

Correct, but if one wants to make code 'portable' over many systems, I have found this useful  
  
Assuming that you have some Boost include above this, so that Boost.Config has run to set up the Boost macros:  
  
`#ifdef BOOST_HAS_FLOAT128  
  // Use native 128 quad (software).  
  BOOST_PRAGMA_MESSAGE("Using native 128-bit quad type.") // Perhaps  
  #include <boost/multiprecision/float128.hpp> // Native 128-bit float not available for MSVC or Clang (yet).  
  using boost::multiprecision::float128;  
#endif`  
  
  
and  
  
#ifdef BOOST_HAS_FLOAT128 // Use native 128 quad (software).  
		do_something<float128>();  
#endif  
  
HTH

---

## Comment 6

> Username: pabristow  
> Created at: 2020-06-22 12:06:40 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/121#issuecomment-647475227  

And, yes of course it would be nice for std::hexfloat to work properly - in particular to ensure that loopback/round-tripping works.  Support is patchy, and not just for multiprecision :-(  
  
Values are already stored as significand and exponent, so what could possibly be difficult ;-)
