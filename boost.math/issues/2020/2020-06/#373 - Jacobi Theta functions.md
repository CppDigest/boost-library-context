# #373 - Jacobi Theta functions [Closed]

> Username: evanmiller  
> Created at: 2020-06-19 02:49:53 UTC  
> Updated at: 2020-08-16 01:22:30 UTC  
> Closed at: 2020-08-16 01:22:30 UTC  
> Url: https://github.com/boostorg/math/issues/373  

Hello! I have begun an implementation of the Jacobi Theta functions ([DLMF](https://dlmf.nist.gov/20)). My personal aspiration is to use them to implement a Kolmorogov-Smirnov distribution, but they could also be used as a complex basis for Boost's Jacobi elliptical functions. I have a few questions before I get any deeper into the implementation.  
  
* Is there any interest in these functions?  
  
* I haven't seen much complex-number code, nor do I have much C++ experience, but here is my proposed interface: (`template` keywords omitted)  
  
```C++  
// First function (complex)  
std::complex<T> jacobi_theta1(std::complex<T> z, std::complex<T> q, const Policy& pol);  
std::complex<T> jacobi_theta1(std::complex<T> z, std::complex<T> q);  
// First function (real-valued)  
T jacobi_theta1(T z, T q, const Policy& pol);  
T jacobi_theta1(T z, T q);  
  
// Second function (complex)  
std::complex<T> jacobi_theta2(std::complex<T> z, std::complex<T> q, const Policy& pol);  
std::complex<T> jacobi_theta2(std::complex<T> z, std::complex<T> q);  
// Second function (real-valued)  
T jacobi_theta2(T z, T q, const Policy& pol);  
T jacobi_theta2(T z, T q);  
  
// Third function (complex)  
std::complex<T> jacobi_theta3(std::complex<T> z, std::complex<T> q, const Policy& pol);  
std::complex<T> jacobi_theta3(std::complex<T> z, std::complex<T> q);  
// Third function (real-valued)  
T jacobi_theta3(T z, T q, const Policy& pol);  
T jacobi_theta3(T z, T q);  
  
// Third function minus 1, for accuracy (complex)  
std::complex<T> jacobi_theta3m1(std::complex<T> z, std::complex<T> q, const Policy& pol);  
std::complex<T> jacobi_theta3m1(std::complex<T> z, std::complex<T> q);  
// Third function minus 1 (real-valued)  
T jacobi_theta3m1(T z, T q, const Policy& pol);  
T jacobi_theta3m1(T z, T q);  
  
// Fourth function (complex)  
std::complex<T> jacobi_theta4(std::complex<T> z, std::complex<T> q, const Policy& pol);  
std::complex<T> jacobi_theta4(std::complex<T> z, std::complex<T> q);  
// Fourth function (real-valued)  
T jacobi_theta4(T z, T q, const Policy& pol);  
T jacobi_theta4(T z, T q);  
  
// Fourth function minus 1, for accuracy (complex)  
std::complex<T> jacobi_theta4m1(std::complex<T> z, std::complex<T> q, const Policy& pol);  
std::complex<T> jacobi_theta4m1(std::complex<T> z, std::complex<T> q);  
// Fourth function minus 1 (real-valued)  
T jacobi_theta4m1(T z, T q, const Policy& pol);  
T jacobi_theta4m1(T z, T q);  
```  
  
* Does this API seem right?  The Fourier series for functions 3 & 4 have a big fat `1.0 +` out front, which explains why they have "minus 1" variants above but functions 1 & 2 do not. Also I should note that Mathematica takes the function number as an integer argument, but that seems insane to me.  
  
* Should I be doing the type promotion stuff in the API? Will that work with complex numbers?  
  
* Is there a testing quick start somewhere? I've seen https://www.boost.org/doc/libs/1_66_0/libs/math/doc/html/math_toolkit/special_tut/special_tut_test.html and https://www.boost.org/doc/libs/1_64_0/libs/test/doc/html/boost_test/runtime_config/test_unit_filtering.html but I am not any closer to running the test suite. What commands do I run and where? If I can get the Jacobi elliptic tests running then I can quickly implement them in terms of the theta functions, in which case the existing test coverage should cover them pretty well. (I'll add my own tests though!)  
  
I think that about covers it. I'd also like to know what the general expectations are before opening a pull request (test coverage? documentation? or just what I have so far?).

---

## Comment 1

> Username: evanmiller  
> Created at: 2020-06-19 11:09:06 UTC  
> Url: https://github.com/boostorg/math/issues/373#issuecomment-646577756  

Scratch the question about testing - the README in this project should probably mention that it is necessary to run `./boostrap.sh` to make `b2` appear.

---

## Comment 2

> Username: NAThompson  
> Created at: 2020-06-19 13:04:38 UTC  
> Url: https://github.com/boostorg/math/issues/373#issuecomment-646624592  

I've thought about implementing these myself, so yes this is a very welcome MR.  
  
The interface looks pretty good, but I would suggest one thing: It needs to work with boost.multiprecision complex numbers, so `std::complex<T>` won't work; it requires `T` to be `float`, `double`, or `long double` for `std::complex`. There is a `boost::is_complex<Complex>` trait that you can `static_assert` off of, IIRC.  
  
The expectations are:  
  
- Produce documentation in (say) `boost/libs/math/doc/sf/jacob_theta.qbk`. Link it in using `boost/libs/math/doc/math.qbk`.  
- Produce a google benchmark, put it in `boost/libs/math/reporting/performance/`.  
- Produce ULPs plots; see `example/airy_ai_ulps.cpp` for example code. Put them in `boost/libs/math/reporting/accuracy`.  
- Produce unit tests; run them under `-fsanitize=address -fsanitize=undefined`.  
  
Then keep an eye on this repo for bug reports.

---

## Comment 3

> Username: evanmiller  
> Created at: 2020-06-19 18:12:06 UTC  
> Url: https://github.com/boostorg/math/issues/373#issuecomment-646800176  

Thank you for the comments @NAThompson.  
  
After some more fiddling I am leaning toward two separate parameterizations:  
  
* A complex version parameterized by `tau`  
* A real-valued version parameterized by `q`  
  
`q` and `tau` are related by:  
  
```  
q = exp(i*π*τ)  
```  
  
This will allow for more precise calculations in the common scenario where `q` is an exponential; users will be able to access the increased precision by invoking the complex-valued version.  
  
I will need to research the issues surrounding complex numbers before proposing a revised API. The good news is that most of the elliptic tests are passing after changing the implementation to leverage the new theta functions - It is likely slower, but I will work on speed after getting the tests back up to 100%.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2020-06-19 19:07:13 UTC  
> Url: https://github.com/boostorg/math/issues/373#issuecomment-646820970  

Sorry for being late to the party on this:  
  
* Yes this sounds like a good idea.  
* Yes by all means test using the Jacobi elliptic data - I wouldn't change the implementation of the existing Jacobi elliptic functions as implementation using Carlson's relations seems to be the accepted method (though Carlson has written most of the literature or course!).  
* The internal boilerplate for promotion and mixed argument handling is covered in https://www.boost.org/doc/libs/1_73_0/libs/math/doc/html/math_toolkit/special_tut/special_tut_impl.html.  I think personally I wouldn't do this for the complex valued functions.  
  
With regard for function interfaces, we have a trait `boost::math::tools::is_complex_type` for detecting complex types - it's there as a specialization point for complex multiprecision types (which Boost.Multiprecision does) as well as detecting `std::complex`.  So the usual enable_if logic would seem to be the way to go:  
  
```  
template <class T>  
typename boost::enable_if_c<boost::math::tools::is_complex_type<T>::value, T>::type   
   jacobi_theta1(T z, T q, const Policy& pol);  // complex version  
template <class T>  
typename boost::disable_if_c<boost::math::tools::is_complex_type<T>::value, T>::type   
   jacobi_theta1(T z, T q, const Policy& pol);  // real valued version  
```

---

## Comment 5

> Username: evanmiller  
> Created at: 2020-06-19 21:27:22 UTC  
> Url: https://github.com/boostorg/math/issues/373#issuecomment-646866546  

@jzmaddock Thanks for the additional comments. The elliptical test suite has been very useful for ironing out bugs in the theta functions. However, in light of your comments, I will plan to leave the elliptical implementation alone when it comes time for a PR.  
  
I will take a look at `is_complex_type`. I guess that will offer the most flexibility. In that case I may offer both parameterizations (`q` and `tau`) with both real and complex versions.  
  
```  
jacobi_theta1tau(T z, T tau);  
jacobi_theta1(T z, T q);  
```  
  
`tau` is usually imaginary with no real component, so the real-valued API could just take in the imaginary component as a real type.

---

## Comment 6

> Username: evanmiller  
> Created at: 2020-06-19 23:21:47 UTC  
> Url: https://github.com/boostorg/math/issues/373#issuecomment-646894661  

Question for the group: Is `Complex` preferable to `typename boost::enable_if_c<boost::math::tools::is_complex_type<T>::value, T>::type`? For guidance I am looking at  
  
https://www.boost.org/doc/libs/1_72_0/libs/multiprecision/doc/html/boost_multiprecision/tut/complex/cpp_complex.html  
  
A proposed function signature would then be  
  
```C++  
template <class Complex, class Policy>  
inline Complex jacobi_theta1(Complex z, Complex q, const Policy& pol);  
```  
  
Thanks for the hand-holding here.

---

## Comment 7

> Username: evanmiller  
> Created at: 2020-06-20 00:19:56 UTC  
> Url: https://github.com/boostorg/math/issues/373#issuecomment-646904849  

Seeing as there are over a dozen functions without complex versions of everything, I am going to skip the complex API and only provide real-valued versions for now. Initially I thought the complex versions would be helpful for implementing the computational [hot tip](https://dlmf.nist.gov/20.14) when q is close to 1, but I will see how far I can get without making a public-facing complex-number API. So ignore my previous comment.  
  
I will still provide the `tau` parameterization, as a real data type interpreted as an imaginary number.

---

## Comment 8

> Username: NAThompson  
> Created at: 2020-06-20 12:43:51 UTC  
> Updated at: 2020-06-20 12:52:55 UTC  
> Url: https://github.com/boostorg/math/issues/373#issuecomment-646989918  

> Seeing as there are over a dozen functions without complex versions of everything, I am going to skip the complex API and only provide real-valued versions for now.   
  
Yeah, we've never prioritized the complex versions of most of these functions. The thought of doing implementations has floated into my mind fairly often, but there's always things that seems more fun to me.  
  
The Jacobi thetas though, feel much more natural in the complex plane . . .  
  
BTW, it looks like Wikipedia links to a nice set of [explicit values for unit tests](https://core.ac.uk/download/pdf/82662862.pdf)

---

## Comment 9

> Username: evanmiller  
> Created at: 2020-06-20 15:38:12 UTC  
> Url: https://github.com/boostorg/math/issues/373#issuecomment-647010922  

I agree Jacobi thetas work well in the complex plane - I would consider sticking with it if I found a more natural way to promote reals to complex values in a way that is compatible with both `std::complex` and the multiprecision types. The code was just getting out of control without a perceptible benefit.  
  
The reference you linked looks useful for the third Jacobi theta function. For general tables to 12D (which include the elliptic functions) I am looking at:  
  
[Smithsonian Mathematical Formulae and Tables of Elliptic Functions](https://repository.si.edu/handle/10088/23627) (1922)  
  
[Smithsonian Elliptic Functions Tables](https://repository.si.edu/handle/10088/23743) (1947)
