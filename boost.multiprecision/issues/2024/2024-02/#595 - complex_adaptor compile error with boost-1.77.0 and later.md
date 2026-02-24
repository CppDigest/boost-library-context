# #595 - complex_adaptor compile error with boost-1.77.0 and later [Closed]

> Username: vmauery  
> Created at: 2024-02-08 22:15:43 UTC  
> Updated at: 2024-02-10 09:05:59 UTC  
> Closed at: 2024-02-10 09:05:59 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/595  

I recently updated my OS, which included an update to boost (from 1.74 to 1.81). I suddenly found that my little personal calculator program would fail to build. I bisected between boost-1.74 and boost-1.81 and found that boost-1.76 was the last working release.  
  
Setup:  
```  
$ g++ -dumpversion  
13  
$ clang++ -dumpversion  
17.0.6  
```  
  
boost (current): 9f83dd55ea67f1bab62c20d29bc5527345aafc55  
multiprecision (current): de3aded8632e0ef0f17dcaf274f5699a25139738  
  
Test case:  
  
```  
/*  
Originally from  
https://www.boost.org/doc/libs/1_84_0/libs/multiprecision/doc/html/boost_multiprecision/tut/complex/cpp_complex.html  
but simplified to do fewer operations and modified to use a custom type.  
  
Compile-OK: g++ -DUSE_EXAMPLE_TYPES cpp_complex.cpp -o cpp_complex.cpp  
Compile-Fail: g++ cpp_complex.cpp -o cpp_complex.cpp -lgmp -lmpfr  
  
*/  
  
#include <iostream>  
  
#ifdef USE_EXAMPLE_TYPES  
#include <boost/multiprecision/cpp_complex.hpp>  
#include <complex>  
#else // USE_CUSTOM_TYPES  
#include <boost/multiprecision/complex_adaptor.hpp>  
#include <boost/multiprecision/mpfr.hpp>  
#include <boost/multiprecision/number.hpp>  
#endif  
  
template <class Complex>  
void complex_number_examples()  
{  
    Complex z1{0, 1};  
    std::cout << std::setprecision(  
        std::numeric_limits<typename Complex::value_type>::digits10);  
    std::cout << std::scientific << std::fixed;  
    std::cout << "Print a complex number: " << z1 << std::endl;  
    std::cout << "Square it             : " << z1 * z1 << std::endl;  
    std::cout << "Real part             : " << z1.real() << " = " << real(z1)  
              << std::endl;  
    std::cout << "Imaginary part        : " << z1.imag() << " = " << imag(z1)  
              << std::endl;  
}  
  
int main()  
{  
#ifdef USE_EXAMPLE_TYPES  
  
    std::cout  
        << "First, some operations we usually perform with std::complex:\n";  
    complex_number_examples<std::complex<double>>();  
    std::cout << "\nNow the same operations performed using quad precision "  
                 "complex numbers:\n";  
    complex_number_examples<boost::multiprecision::cpp_complex_quad>();  
  
#else // USE_CUSTOM_TYPES  
  
    using float_backend = boost::multiprecision::mpfr_float_backend<0>;  
    using complex_backend =  
        boost::multiprecision::complex_adaptor<float_backend>;  
    using mpc = boost::multiprecision::number<complex_backend,  
                                              boost::multiprecision::et_off>;  
    std::cout << "\nNow the same operations performed using custom complex "  
                 "numbers:\n";  
    complex_number_examples<mpc>();  
  
#endif  
    return 0;  
}  
```  
  
Compile matrix:  
```  
----------------------------------------------------------------------------------  
| command line               |  Boost version  |  Outcome  
----------------------------------------------------------------------------------  
| g++ -DUSE_EXAMPLE_TYPES    |  boost-1.76     |  OK  
----------------------------------------------------------------------------------  
| g++ -DUSE_EXAMPLE_TYPES    |  boost-latest   |  OK  
----------------------------------------------------------------------------------  
| g++ -lgmp -lmpfr           |  boost-1.76     |  OK  
----------------------------------------------------------------------------------  
| g++ -lgmp -lmpfr           |  boost-latest   |  Fail  
----------------------------------------------------------------------------------  
```  
  
I see the error with both g++ and clang++. The clang++ error is easier to read:  
```  
In file included from cpp_complex.cpp:7:  
In file included from /usr/local/include/boost/multiprecision/complex_adaptor.hpp:9:  
/usr/local/include/boost/multiprecision/number.hpp:1096:23: error: no member named 'thread_default_precision' in 'boost::multiprecision::backends::complex_adaptor<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>>'  
 1096 |       return Backend::thread_default_precision();  
      |              ~~~~~~~~~^  
/usr/local/include/boost/multiprecision/detail/precision.hpp:198:23: note: in instantiation of member function 'boost::multiprecision::number<boost::multiprecision::backends::complex_adaptor<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>>, boost::multiprecision::et_off>::thread_default_precision' requested here  
  198 |       m_old_prec = R::thread_default_precision();  
      |                       ^  
/usr/local/include/boost/multiprecision/detail/precision.hpp:178:7: note: in instantiation of member function 'boost::multiprecision::detail::scoped_default_precision<boost::multiprecision::number<boost::multiprecision::backends::complex_adaptor<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>>, boost::multiprecision::et_off>>::init' requested here   
  178 |       init(has_uniform_precision() ? R::thread_default_precision() : (std::max)((std::max)(current_precision_of<R>(a), current_precision_of<R>(b)), (std::max)(R::thread_default_precision(), current_precision_of<R>(c))));  
      |       ^  
/usr/local/include/boost/multiprecision/number.hpp:228:52: note: in instantiation of function template specialization 'boost::multiprecision::detail::scoped_default_precision<boost::multiprecision::number<boost::multiprecision::backends::complex_adaptor<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>>, boost::multiprecision::et_off>>::scoped_default_precision<int, int, boost::multiprecision::number<boost::multiprecision::backends::complex_adaptor<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>>, boost::multiprecision::et_off>>' requested here   
  228 |       detail::scoped_default_precision<self_type>  precision_guard(v1, v2, *this);  
      |                                                    ^  
cpp_complex.cpp:15:13: note: in instantiation of function template specialization 'boost::multiprecision::number<boost::multiprecision::backends::complex_adaptor<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>>, boost::multiprecision::et_off>::number<int, int>' requested here  
   15 |     Complex z1{0, 1};  
      |             ^  
cpp_complex.cpp:47:5: note: in instantiation of function template specialization 'complex_number_examples<boost::multiprecision::number<boost::multiprecision::backends::complex_adaptor<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>>, boost::multiprecision::et_off>>' requested here  
   47 |     complex_number_examples<mpc>();  
      |     ^  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2024-02-09 09:35:23 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/595#issuecomment-1935601029  

That smells like a bug (and a big gap in our testing if so), will investigate...

---

## Comment 2

> Username: jzmaddock  
> Created at: 2024-02-09 17:20:45 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/595#issuecomment-1936303601  

Confirmed, should be fixed in referenced PR.  
  
Note that your test case still fails due to setting the stream precision to INT_MAX or something similar - numeric_limits is not functional for complex types, and variable precision types have no meaningful value for numeric_limits<>::digits10.

---

## Comment 3

> Username: ckormanyos  
> Created at: 2024-02-09 17:22:17 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/595#issuecomment-1936305665  

Great find. I'd like to see if the new coverage process can pick up the new tests. Awesome!
