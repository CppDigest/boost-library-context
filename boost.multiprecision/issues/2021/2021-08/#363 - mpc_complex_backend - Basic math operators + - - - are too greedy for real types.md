# #363 - mpc_complex_backend : Basic math operators + - * / are too greedy for real types. [Closed]

> Username: cosurgi  
> Created at: 2021-08-30 15:09:35 UTC  
> Updated at: 2021-09-01 08:57:26 UTC  
> Closed at: 2021-09-01 08:57:26 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/363  

Observe following example code where I lose 400 digits of precision without even a single warning from compiler:  
  
```  
#include <iostream>  
#include <boost/core/demangle.hpp>  
#include <boost/multiprecision/mpc.hpp>  
#include <boost/multiprecision/mpfr.hpp>  
  
template<typename T>  
void print_name(T) {  
	std::cout << boost::core::demangle(typeid(T).name()) << std::endl;  
}  
  
int main() {  
	boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<500u>,  boost::multiprecision::et_off> a(1);  
	boost::multiprecision::number<boost::multiprecision::backends::mpc_complex_backend<100u>, boost::multiprecision::et_off> b(1);  
  
	// ERROR: 400 digits of precision are lost without even a warning from the compiler:  
	auto c1 = a + b;  
	auto c2 = b + a;  
  
	auto c3 = a - b;  
	auto c4 = b - a;  
  
	auto c5 = a * b;  
	auto c6 = b * a;  
  
	auto c7 = a / b;  
	auto c8 = b / a;  
  
	print_name(c1);  
	print_name(c2);  
	print_name(c3);  
	print_name(c4);  
	print_name(c5);  
	print_name(c6);  
	print_name(c7);  
	print_name(c8);  
}  
```  
  
I suppose that such conversion could be allowed, but only explicitly when I really want to do it. Not by mistake like in such an arithmetic operation.  
  
I encountered this problem when implementing a helper for upconverting precisions [1,2] which provides these four basic operations. My helper operators would create in example above an `mpc_complex_500` type here. But they cannot, because the operators + - * / are already provided for this combination of types.  
  
1. https://gitlab.com/yade-dev/trunk/-/blob/improveComplex/lib/high-precision/UpconversionOfBasicOperatorsHP.hpp  
  
2. https://gitlab.com/yade-dev/trunk/-/blob/master/lib/high-precision/UpconversionOfBasicOperatorsHP.hpp - when 1. gets merged see 2.

---

## Comment 1

> Username: cosurgi  
> Created at: 2021-08-30 17:22:32 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/363#issuecomment-908531259  

BTW: it feels similar to https://github.com/boostorg/math/issues/350

---

## Comment 2

> Username: jzmaddock  
> Created at: 2021-08-31 08:34:34 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/363#issuecomment-909023234  

That's a bug - all the machinery is in place to ensure that lossy conversions are always explicit, but somehow that one has escaped!  
  
I'll look into it.

---

## Comment 3

> Username: cosurgi  
> Created at: 2021-08-31 13:16:10 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/363#issuecomment-909227858  

Thank you! :)
