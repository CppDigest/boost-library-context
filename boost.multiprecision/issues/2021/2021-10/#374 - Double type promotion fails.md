# #374 - Double type promotion fails [Closed]

> Username: ianhbell  
> Created at: 2021-10-05 12:55:43 UTC  
> Updated at: 2021-10-05 16:30:23 UTC  
> Closed at: 2021-10-05 16:30:23 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/374  

I would like to use ``boost::multiprecision`` inside containers, which sort of works, but some of the type-promotion does not.  For instance this is valid code, but ``boost::multiprecision`` doesn't figure out the promotion needed to do the multiplication and I get pages of errors that originate with a message like:  
```  
<source>:8:11: error: invalid operands to binary expression ('std::complex<my_float>' (aka 'complex<number<backends::cpp_bin_float<50>>>') and 'double')  
    auto c = a*b;  
```  
This doesn't compile on any compiler I have tried (clang, G++, MSVC), with boost 1.64, 1.77.  Compiler explorer: https://godbolt.org/z/Wr1v3Wh5P  
  
```c++  
#include <boost/multiprecision/cpp_bin_float.hpp>  
#include <complex>  
  
int main()  
{  
    using my_float = boost::multiprecision::cpp_bin_float_50;  
    std::complex<my_float> a{1.2, 5.6};  
    double b = 3.4;  
    auto c = a*b;  
}  
```

---

## Comment 1

> Username: ckormanyos  
> Created at: 2021-10-05 15:47:19 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/374#issuecomment-934530730  

Hi Ian,  
  
There are a few things we will need to run through on your example.  
  
Just a quick feedback here:  
  
- The C++ standard allows specialization of the type `std::complex` for `float`, `double` and `long` `double` only.  
- You might also have some issues specific to Boost.multiprecision.  
  
I will take a little time to work through your example, suggest a multiprecision complex adapter and we can probably find a good way for you to go forward.  
  
Give me a bit time to rework your example and provide the tips.  
  
Kind regards, Chris

---

## Comment 2

> Username: ianhbell  
> Created at: 2021-10-05 15:51:10 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/374#issuecomment-934534182  

Hmm didn't realize the standard only allowed ``std::complex`` to accept the "standard floats". Suppose that makes sense, but then maybe it would make sense for ``boost::multiprecision`` to have its own complex class? Or is that a bad idea?

---

## Comment 3

> Username: NAThompson  
> Created at: 2021-10-05 15:55:51 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/374#issuecomment-934538469  

@ianhbell : See [here](https://github.com/boostorg/multiprecision/blob/develop/include/boost/multiprecision/complex_adaptor.hpp) and [here](https://github.com/boostorg/multiprecision/blob/develop/include/boost/multiprecision/complex128.hpp).

---

## Comment 4

> Username: jzmaddock  
> Created at: 2021-10-05 16:06:55 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/374#issuecomment-934548586  

In addition to the comments above, std::complex does not support mixed-precision arithmetic at all, for example you will see the same issue with:  
  
```  
   std::complex<double> d{ 2, 3 };  
   d = d * 2.f;  
```  
  
Likewise if you multiply by an integer.  
  
BTW The main entry point for multiprecision complex number types is here: https://www.boost.org/doc/libs/1_77_0/libs/multiprecision/doc/html/boost_multiprecision/tut/complex.html

---

## Comment 5

> Username: ckormanyos  
> Created at: 2021-10-05 16:12:18 UTC  
> Updated at: 2021-10-05 16:14:09 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/374#issuecomment-934553578  

Hi @ianhbell, based on the suggestions from @NAThompson, let's try something like this:  
  
```  
#include <iomanip>  
#include <iostream>  
  
#include <boost/multiprecision/cpp_bin_float.hpp>  
#include <boost/multiprecision/complex_adaptor.hpp>  
  
int main()  
{  
  using my_float_backend_type = boost::multiprecision::cpp_bin_float<50U>;  
  using my_float_type         = boost::multiprecision::number<my_float_backend_type>;  
  using my_complex_type       = boost::multiprecision::number<boost::multiprecision::complex_adaptor<my_float_backend_type>>;  
  
  my_complex_type a { my_float_type(1.2), my_float_type(5.6) };  
  
  std::cout << std::setprecision(std::numeric_limits<my_float_type>::digits10)  
            << a  
            << std::endl;  
  
  const double b = 3.4;  
  
  auto c = a * b;  
  
  std::cout << std::setprecision(std::numeric_limits<my_float_type>::digits10)  
            << c  
            << std::endl;  
}  
```

---

## Comment 6

> Username: ckormanyos  
> Created at: 2021-10-05 16:16:43 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/374#issuecomment-934557345  

After running through that, you will find that the precision of `double` still permeates this example.  
  
This is because the multiprecision instances, when instantiated from built-in `double` maintain the exact precision of `double`.  
  
If you would like a multiprecision type closer to, say, 1.2, then you must construct as follows:  
  
```  
#include <iomanip>  
#include <iostream>  
  
#include <boost/multiprecision/cpp_bin_float.hpp>  
#include <boost/multiprecision/complex_adaptor.hpp>  
  
int main()  
{  
  using my_float_backend_type = boost::multiprecision::cpp_bin_float<50U>;  
  using my_float_type         = boost::multiprecision::number<my_float_backend_type>;  
  using my_complex_type       = boost::multiprecision::number<boost::multiprecision::complex_adaptor<my_float_backend_type>>;  
  
  my_complex_type a { my_float_type(12) / 10, my_float_type(56) / 10 };  
  
  std::cout << std::setprecision(std::numeric_limits<my_float_type>::digits10)  
            << a  
            << std::endl;  
  
  const my_float_type b = my_float_type(34) / 10;  
  
  auto c = a * b;  
  
  std::cout << std::setprecision(std::numeric_limits<my_float_type>::digits10)  
            << c  
            << std::endl;  
}  
```

---

## Comment 7

> Username: ckormanyos  
> Created at: 2021-10-05 16:19:00 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/374#issuecomment-934559232  

... and note the astute observation from @jzmaddock. Thanks John and Nick.  
  
Cc: @NAThompson

---

## Comment 8

> Username: ianhbell  
> Created at: 2021-10-05 16:19:54 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/374#issuecomment-934559977  

Yes, these subtle non-upcasting-double issues are real problem for me. Ultimately I'm trying to drop this adapter into existing code that I don't want to modify at all. I was able to simplify the example just a tiny bit:  
```c++  
#include <iomanip>  
#include <iostream>  
  
#include <boost/multiprecision/cpp_bin_float.hpp>  
#include <boost/multiprecision/complex_adaptor.hpp>  
  
int main()  
{  
  using my_float_type   = boost::multiprecision::number<boost::multiprecision::cpp_bin_float<50U>>;  
  using my_complex_type = boost::multiprecision::number<boost::multiprecision::complex_adaptor<boost::multiprecision::cpp_bin_float<50U>>>;  
  
  my_complex_type a { 1.2, 5.6 };  
  
  std::cout << std::setprecision(std::numeric_limits<my_float_type>::digits10)  
            << a  
            << std::endl;  
  
  const double b = 3.4;  
  
  auto c = a * b;  
  
  std::cout << std::setprecision(std::numeric_limits<my_float_type>::digits10)  
            << c  
            << std::endl;  
}  
```

---

## Comment 9

> Username: ianhbell  
> Created at: 2021-10-05 16:20:48 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/374#issuecomment-934560753  

But point taken, as 1.2 (and others) is not exactly representable in double precision, and so you lose a little bit (a very little bit) of precision going from double to extended.
