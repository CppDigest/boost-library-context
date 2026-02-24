# #109 - error: could not convert ... when calling a template function [Closed]

> Username: pulver  
> Created at: 2019-01-27 20:15:55 UTC  
> Updated at: 2019-01-27 21:02:00 UTC  
> Closed at: 2019-01-27 21:02:00 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/109  

This minimal example code produces a compile-time `error: could not convert ...`  
  
```c++  
#include <boost/multiprecision/cpp_bin_float.hpp>  
#include <boost/multiprecision/cpp_dec_float.hpp>  
#include <iostream>  
  
template<typename T>  
T square(const T& x)  
{  
  return x*x;  
}  
  
int main()  
{  
  boost::multiprecision::cpp_bin_float_50 mp_bin = 2;  
  std::cout << "square(mp_bin*mp_bin) = " << square(mp_bin*mp_bin) << std::endl; // ok  
  
  boost::multiprecision::cpp_dec_float_50 mp_dec = 2;  
  std::cout << "square(mp_dec*mp_dec) = " << square(mp_dec*mp_dec) << std::endl; // error  
  
  return 0;  
}  
```  
  
Compiler version: g++ (GCC) 7.3.1 20180303 (Red Hat 7.3.1-5)  
Command line: g++ multiprecision_error.cpp  
Full error message:  
```  
multiprecision_error.cpp: In instantiation of ‘T square(const T&) [with T = boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates, boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50> >, boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50> >, void, void>]’:  
multiprecision_error.cpp:17:66:   required from here  
multiprecision_error.cpp:8:11: error: could not convert ‘boost::multiprecision::operator*<boost::multiprecision::detail::multiply_immediates, boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50> >, boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50> >, void, void, boost::multiprecision::detail::multiply_immediates, boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50> >, boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50> >, void, void>((* & x), (* & x))’ from ‘boost::multiprecision::detail::expression<boost::multiprecision::detail::multiplies, boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates, boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50> >, boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50> >, void, void>, boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates, boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50> >, boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50> >, void, void>, void, void>’ to ‘boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates, boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50> >, boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50> >, void, void>’  
   return x*x;  
          ~^~  
```  
  
I was led to this by investigating a possibly related issue: https://github.com/boostorg/math/pull/176#discussion_r251247721  
where the problem there is inverted, in that there is a problem between `autodiff_fvar<>` and `cpp_bin_float<>` with ambiguous operator overloads, but is fine with `cpp_dec_float<>`. Hopefully they can be resolved simultaneously.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-01-27 21:02:00 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/109#issuecomment-457953898  

That one is by design - see https://www.boost.org/doc/libs/1_66_0/libs/multiprecision/doc/html/boost_multiprecision/intro.html#boost_multiprecision.intro.expression_templates which mentions this issue: if you rely on template argument deduction, and pass the function an _expression template_ rather than a number, then compilation _will_ fail deep inside the function.    
  
In this case you are passing `square` the result of operator* which is an expression template and not  a number.  So you either need to cast the result of `mp_dec*mp_dec` to an `mp_dec` or provide an explicit template argument, or turn off expression templates (cpp_bin_float has them turned off by default since it does no memory allocation internally, and is therefore cheap-ish to copy).
