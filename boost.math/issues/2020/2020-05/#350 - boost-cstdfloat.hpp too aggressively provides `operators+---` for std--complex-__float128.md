# #350 - boost/cstdfloat.hpp too aggressively provides `operators+-/*` for std::complex<__float128> [Closed]

> Username: cosurgi  
> Created at: 2020-05-03 14:20:00 UTC  
> Updated at: 2021-08-30 17:23:02 UTC  
> Closed at: 2020-08-03 17:51:29 UTC  
> Url: https://github.com/boostorg/math/issues/350  

The problem is following:  
  
`template<class A,class B> std::complex<__float128> operator+(A, B);`  
  
is too aggressively accepting other complex types. Even when they have significantly higher precision. Same problem is with other operators: `+ - / *`.  
  
The example code (below) is compiling without even a single warning, even though I compile with every possible warning enabled. This is the compile command:  
  
```  
g++ test.cpp -o test -std=gnu++17 -lboost_system -lmpfr -lquadmath -Werror -Wformat -Wformat-security -Wformat-nonliteral -Wall -Wextra -Wnarrowing -Wreturn-type -Wuninitialized -Wfloat-conversion -Wcast-align -Wdisabled-optimization -Wtrampolines -Wpointer-arith -Wswitch-bool -Wwrite-strings -Wnon-virtual-dtor -Wreturn-local-addr  
```  
  
The example which demonstrates the problem is following:  
  
```  
#include <boost/cstdfloat.hpp>  
#include <boost/multiprecision/mpfr.hpp>  
#include <boost/multiprecision/float128.hpp>  
#include <boost/core/demangle.hpp>  
#include <iostream>  
  
int main()  
{  
	std::complex<boost::multiprecision::mpfr_float_50  > v1(-2.5);  
	std::complex<boost::multiprecision::mpfr_float_100 > v2(-2.5);  
	std::complex<boost::multiprecision::float128 >       v3(-2.5);  
	auto r1 = v1+v2; // same problem with operators - * /  
	std::cout << r1 << "\n";  
	std::cout << boost::core::demangle(typeid(decltype(r1)).name()) << "\n\n";  
	auto r2 = v1+v3; // same problem with operators - * /  
	std::cout << r2 << "\n";  
	std::cout << boost::core::demangle(typeid(decltype(r2)).name()) << "\n\n";  
}  
```  
  
Running it gives following output:  
  
```  
(-5,0)  
std::complex<__float128>  
(-5,0)  
std::complex<__float128>  
```  
  
The problem is that I have nowhere declared that I want to use `std::complex<__float128>` and yet I end up with this type. I have found out that disabling `#include <boost/cstdfloat.hpp>` solves the problem: the compiler complains about non-existing `operator+`. And this problem I can readily solve by `#include <complex>` and then doing the casting by hand to the "higher precision" type. Which produces a following "workaround" code which works as expected:  
  
```  
#include <complex>  
#include <boost/multiprecision/mpfr.hpp>  
#include <boost/multiprecision/float128.hpp>  
#include <boost/core/demangle.hpp>  
#include <iostream>  
  
int main()  
{  
	std::complex<boost::multiprecision::mpfr_float_50  > v1(-2.5);  
	std::complex<boost::multiprecision::mpfr_float_100 > v2(-2.5);  
	std::complex<boost::multiprecision::float128 >       v3(-2.5);  
	auto r1 = static_cast<decltype(v2)>(v1)+v2;  
	std::cout << r1 << "\n";  
	std::cout << boost::core::demangle(typeid(decltype(r1)).name()) << "\n\n";  
	auto r2 = v1+static_cast<decltype(v1)>(v3);  
	std::cout << r2 << "\n";  
	std::cout << boost::core::demangle(typeid(decltype(r2)).name()) << "\n\n";  
}  
```  
  
And then I get the expected program output:  
  
```  
(-5,0)  
std::complex<boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<100u, (boost::multiprecision::mpfr_allocation_type)1>, (boost::multiprecision::expression_template_option)1> >  
  
(-5,0)  
std::complex<boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<50u, (boost::multiprecision::mpfr_allocation_type)1>, (boost::multiprecision::expression_template_option)1> >  
```

---

## Comment 1

> Username: cosurgi  
> Created at: 2020-05-03 16:13:46 UTC  
> Url: https://github.com/boostorg/math/issues/350#issuecomment-623133775  

I discovered that by doing `#define BOOST_CSTDFLOAT_NO_LIBQUADMATH_COMPLEX` prior to including `<boost/cstdfloat.hpp>` I can alleviate this problem.   
I need to do more testing to know if this workaround covers all my use cases.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2020-07-31 18:01:00 UTC  
> Url: https://github.com/boostorg/math/issues/350#issuecomment-667257348  

Confirmed: it's actually the converting constructor for complex<__float128> which is too permissive.

---

## Comment 3

> Username: cosurgi  
> Created at: 2021-08-30 17:23:01 UTC  
> Url: https://github.com/boostorg/math/issues/350#issuecomment-908531636  

I have just reported a similar bug: https://github.com/boostorg/multiprecision/issues/363
