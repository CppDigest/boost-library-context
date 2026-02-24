# #718 - Video available [Closed]

> Username: LegalizeAdulthood  
> Created at: 2025-07-10 15:17:31 UTC  
> Updated at: 2025-07-14 10:58:35 UTC  
> Closed at: 2025-07-14 10:58:35 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/718  

If you want to link to the presentation on Boost.Multiprecision, it is on YouTube here:   
https://www.youtube.com/watch?v=mK4WjpvLj4c

---

## Comment 1

> Username: ckormanyos  
> Created at: 2025-07-11 18:28:43 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/718#issuecomment-3063313634  

> If you want to link to the presentation on Boost.Multiprecision, it is on YouTube here:  
  
The presentation and video are comprehensive, instructive, clear and very helpful. I'd like to find a way to link in the video to the docs.  
  
@jzmaddock just thinking out loud here, I do not see any conflict between linking in a YouTube video to the BSL-docs. The licensing of the video is probably not BSL. But simply linking the video should be unproblematic.  
  
Your thoughts on:  
  
- Link the video, yes or no?  
- If yes, where?  
- Do you see any licensing conflicts?  
  
Cc: @jzmaddock and @LegalizeAdulthood

---

## Comment 2

> Username: ckormanyos  
> Created at: 2025-07-11 18:29:19 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/718#issuecomment-3063315477  

See also #717

---

## Comment 3

> Username: LegalizeAdulthood  
> Created at: 2025-07-12 01:44:27 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/718#issuecomment-3064488457  

If it helps I always set the license on my youtube videos to creative commons, not the youtube license

---

## Comment 4

> Username: jzmaddock  
> Created at: 2025-07-12 16:41:29 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/718#issuecomment-3065845575  

I think as it's external content, the video is fine to link to regardless of licence.  
  
One comment: have we over-complicated cpp_complex_example.cpp?  All those using statements should be superfluous in this case: either we have `std::complex<>` which will find the `std::` functions via ADL, or else a multiprecision type which will do the same?

---

## Comment 5

> Username: jzmaddock  
> Created at: 2025-07-12 16:56:35 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/718#issuecomment-3065866576  

We also appear to have to have tommath_rational listed twice in the index?

---

## Comment 6

> Username: ckormanyos  
> Created at: 2025-07-12 18:31:03 UTC  
> Updated at: 2025-07-12 20:10:31 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/718#issuecomment-3065951641  

- [x] it's external content, the video is fine to link to regardless of licence.  
- [x] One comment: have we over-complicated cpp_complex_example.cpp  
- [x] We also appear to have to have tommath_rational listed twice in the index  
  
Work list created. I'll try to handle all this.  
  
Edit: All fixed by #721  
  
Thanks @jzmaddock   
  
Cc: @LegalizeAdulthood

---

## Comment 7

> Username: ckormanyos  
> Created at: 2025-07-12 19:01:31 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/718#issuecomment-3065967028  

> We also appear to have to have tommath_rational listed twice in the index  
  
Oh that was a good one. The files `tutorial_boost_rational.qbk` and `tutorial_tommath_rational.qbk` are identical, yet both included. Thereby the information is redundant.  
  
I will remove the former (aka `tutorial_boost_rational.qbk`).  
  
We will need to really double-check this when building the docs for 1.89.  
  
Cc: @jzmaddock and @LegalizeAdulthood

---

## Comment 8

> Username: LegalizeAdulthood  
> Created at: 2025-07-12 20:38:37 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/718#issuecomment-3066033290  

> have we over-complicated cpp_complex_example.cpp?   
  
I don't think so; it's good to see an explicit use of those math functions which aren't shown in any of the other examples.

---

## Comment 9

> Username: ckormanyos  
> Created at: 2025-07-13 06:21:31 UTC  
> Updated at: 2025-07-13 06:58:57 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/718#issuecomment-3066640077  

>> have we over-complicated cpp_complex_example.cpp?  
  
> I don't think so; it's good to see an explicit use of those math functions which aren't shown in any of the other examples.  
  
Oh well maybe I acted too quickly, I already removed them.  
  
Upon reflection, if we restore the `using` lines, we could extend the example and the text to call the functions for arguments ${\in}{\mathbb{R}}$ for `float`, `double` and `long double`? Then you might actually need to use `using`? I could extend the text and the example and restore the `using`.  
  
Thoughts.

---

## Comment 10

> Username: ckormanyos  
> Created at: 2025-07-13 06:58:30 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/718#issuecomment-3066674394  

The comments in the example might also be slightly outdated. The comments mention using `MPC`. But I believe the example now uses Multiprecision's own `complex_adaptor`.

---

## Comment 11

> Username: ckormanyos  
> Created at: 2025-07-13 06:59:50 UTC  
> Updated at: 2025-07-13 07:00:17 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/718#issuecomment-3066676325  

> Upon reflection, if we restore the `using` lines, we could extend the example and the text to call the functions for arguments ${\in}{\mathbb{R}}$ for `float`, `double` and `long double`?  
  
Oh this wouldn't immediately (without change) work since the example calls some complex-only functions such as `polar`.

---

## Comment 12

> Username: jzmaddock  
> Created at: 2025-07-13 08:13:51 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/718#issuecomment-3066711623  

> I don't think so; it's good to see an explicit use of those math functions which aren't shown in any of the other examples.  
  
Maybe we should update the other examples ;)  
  
My point was that in the special case of complex types, the `using std::whatever` declarations are superfluous, and just clutter up the example, where as for integer and floating point types where the std provided (built in) types aren't in namespace std then yes, we do need the using declarations.

---

## Comment 13

> Username: jzmaddock  
> Created at: 2025-07-13 08:14:56 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/718#issuecomment-3066712117  

BTW, I haven't quite got to the end of your video yet, but many thanks for this, it's actually quite interesting and really useful to have an outside take on things!

---

## Comment 14

> Username: ckormanyos  
> Created at: 2025-07-13 09:52:52 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/718#issuecomment-3066820564  

This is about as expressive as I can get the example, corrected in the comments and actually using `using`.  
  
```  
///////////////////////////////////////////////////////////////  
//  Copyright 2018 - 2025 Nick Thompson.  
//  Copyright 2025 John Maddock.  
//  Copyright 2025 Christopher Kormanyos.  
//  Distributed under the Boost  
//  Software License, Version 1.0. (See accompanying file  
//  LICENSE_1_0.txt or copy at https://www.boost.org/LICENSE_1_0.txt  
  
/*`This example demonstrates the usage of the complex_adaptor backend for multiprecision complex numbers.  
In the following, we will show how using the complex_adaptor backend together with number allows for the same operations as the C++ standard library complex numbers.  
*/  
  
//[cpp_complex_eg  
#include <boost/multiprecision/cpp_complex.hpp>  
  
#include <cmath>  
#include <complex>  
#include <iostream>  
  
template<class NumericType>  
void elementary_functions(const NumericType z1)  
{  
    std::cout << "\nElementary special functions:\n";  
    std::cout << "Absolute value        : " << abs(z1) << std::endl;  
    using std::arg;  
    std::cout << "Argument              : " << arg(z1) << std::endl;  
    using std::norm;  
    std::cout << "Norm                  : " << norm(z1) << std::endl;  
    using std::conj;  
    std::cout << "Complex conjugate     : " << conj(z1) << std::endl;  
    using std::proj;  
    std::cout << "Projection onto Riemann sphere: " <<  proj(z1) << std::endl;  
    std::cout << "exp(z1) = " << exp(z1) << std::endl;  
    std::cout << "log(z1) = " << log(z1) << std::endl;  
    std::cout << "log10(z1) = " << log10(z1) << std::endl;  
    std::cout << "pow(z1, z1) = " << pow(z1, z1) << std::endl;  
    std::cout << "Take its square root  : " << sqrt(z1) << std::endl;  
    std::cout << "sin(z1) = " << sin(z1) << std::endl;  
    std::cout << "cos(z1) = " << cos(z1) << std::endl;  
    std::cout << "tan(z1) = " << tan(z1) << std::endl;  
    std::cout << "asin(z1) = " << asin(z1) << std::endl;  
    std::cout << "acos(z1) = " << acos(z1) << std::endl;  
    std::cout << "atan(z1) = " << atan(z1) << std::endl;  
    std::cout << "sinh(z1) = " << sinh(z1) << std::endl;  
    std::cout << "cosh(z1) = " << cosh(z1) << std::endl;  
    std::cout << "tanh(z1) = " << tanh(z1) << std::endl;  
    std::cout << "asinh(z1) = " << asinh(z1) << std::endl;  
    std::cout << "acosh(z1) = " << acosh(z1) << std::endl;  
    std::cout << "atanh(z1) = " << atanh(z1) << std::endl;  
}  
  
template<class ComplexType>  
void complex_number_examples()  
{  
    using complex_type = ComplexType;  
    using real_type = typename complex_type::value_type;  
  
    complex_type z1 { real_type(0), real_type(1) };  
  
    std::cout << "Print a complex number: " << z1 << std::endl;  
    std::cout << "Square it             : " << z1*z1 << std::endl;  
    std::cout << "Real part             : " << z1.real() << " = " << real(z1) << std::endl;  
    std::cout << "Imaginary part        : " << z1.imag() << " = " << imag(z1) << std::endl;  
  
    real_type r { 1 };  
    real_type theta { 0.75 };  
    using std::polar;  
    std::cout << "Polar coordinates (phase = 0)    : " << polar(r) << std::endl;  
    std::cout << "Polar coordinates (phase !=0)    : " << polar(r, theta) << std::endl;  
  
    elementary_functions(z1);  
}  
  
int main()  
{  
    const auto flags_orig = std::cout.flags();  
  
    std::cout << std::setprecision(std::numeric_limits<typename std::complex<double>::value_type>::digits10);  
    std::cout << std::scientific << std::fixed;  
  
    std::cout << "First, some operations we usually perform with std::complex:\n";  
    complex_number_examples<std::complex<double>>();  
  
    std::cout << "\nNow the same operations performed using quad precision complex numbers:\n";  
    std::cout.flags(flags_orig);  
    std::cout << std::setprecision(std::numeric_limits<typename boost::multiprecision::cpp_complex_quad::value_type>::digits10);  
    complex_number_examples<boost::multiprecision::cpp_complex_quad>();  
  
    std::cout << "\nNow the same elementary functions performed using built-in real-valued numbers:\n";  
    std::cout.flags(flags_orig);  
    std::cout << std::setprecision(std::numeric_limits<float>::digits10);  
    elementary_functions(0.125F);  
  
    return 0;  
}  
//]  
  
/*  
  
//[cpp_complex_out  
  
Print a complex number: (0,1)  
Square it             : -1  
Real part             : 0 = 0  
Imaginary part        : 1 = 1  
Polar coordinates (phase = 0)    : 1  
Polar coordinates (phase !=0)    : (0.731688868873820886311838753000085,0.681638760023334166733241952779894)  
  
Elementary special functions:  
Absolute value        : 1  
Argument              : 1.57079632679489661923132169163975  
Norm                  : 1  
Complex conjugate     : (0,-1)  
Projection onto Riemann sphere: (0,1)  
exp(z1) = (0.540302305868139717400936607442977,0.841470984807896506652502321630299)  
log(z1) = (0,1.57079632679489661923132169163975)  
log10(z1) = (0,0.682188176920920673742891812715678)  
pow(z1, z1) = 0.207879576350761908546955619834979  
Take its square root  : (0.707106781186547524400844362104849,0.707106781186547524400844362104849)  
sin(z1) = (0,1.1752011936438014568823818505956)  
cos(z1) = 1.54308063481524377847790562075706  
tan(z1) = (0,0.761594155955764888119458282604794)  
asin(z1) = (0,0.881373587019543025232609324979792)  
acos(z1) = (1.57079632679489661923132169163975,-0.881373587019543025232609324979792)  
atan(z1) = (0,inf)  
sinh(z1) = (0,0.841470984807896506652502321630299)  
cosh(z1) = 0.540302305868139717400936607442977  
tanh(z1) = (0,1.55740772465490223050697480745836)  
asinh(z1) = (0,1.57079632679489661923132169163975)  
acosh(z1) = (0.881373587019543025232609324979792,1.57079632679489661923132169163975)  
atanh(z1) = (0,0.785398163397448309615660845819876)  
//]  
*/  
```

---

## Comment 15

> Username: ckormanyos  
> Created at: 2025-07-13 10:59:15 UTC  
> Updated at: 2025-07-13 11:00:00 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/718#issuecomment-3066970369  

> Maybe we should update the other examples  
  
I believe we should. I can give that a try, but for next release.  
  
For this release, we could use something like the corrected, modified code above (which does actually use `using`). And I could augment the docs accordingly.  
  
As a second step, we can upgrade some of the examples.  
  
The [Jahnke-Emden-Lambda example](https://www.boost.org/doc/libs/latest/libs/multiprecision/doc/html/boost_multiprecision/tut/floats/fp_eg/jel.html) makes use of `using`. But we could emphasize it even more.  
  
The [Calculating a Derivative example](https://www.boost.org/doc/libs/latest/libs/multiprecision/doc/html/boost_multiprecision/tut/floats/fp_eg/nd.html) could make much better use of `using`, particularly in the calls of the derivative functor(s).  
  
The [Calculating an Integral example](https://www.boost.org/doc/libs/latest/libs/multiprecision/doc/html/boost_multiprecision/tut/floats/fp_eg/gi.html) could be made much more generic. It seems to already be relying on ADL and does not use `using`. I'm not even sure it is set up properly for `float`, `double` or `long double`?  
  
Looking at [Polynomial Evaluation](https://www.boost.org/doc/libs/latest/libs/multiprecision/doc/html/boost_multiprecision/tut/floats/fp_eg/poly_eg.html) I have evolved in the past 20 years or so. These days, I tend to prefer Pade-like expansions for these in order to reduce the coefficient list(s). These still require polynomial evaluation for the numerator and denominator in the Pade approximation.  
  
In the [Gause-Laguerre example](https://www.boost.org/doc/libs/latest/libs/multiprecision/doc/html/boost_multiprecision/tut/floats/fp_eg/gauss_lagerre_quadrature.html), we could post the [full link](https://www.wolframalpha.com/input?i=Fit%5B%7B%7B21.0%2C+3.5%7D%2C+%7B51.0%2C+11.1%7D%2C+%7B101.0%2C+22.5%7D%2C+%7B201.0%2C+46.8%7D%7D%2C+%7B1%2C+d%2C+d%5E2%7D%2C+d%5D+FullSimplify%5B%25%5D) to the Wolfram alpha code for the guess at the numbers of coefficients.

---

## Comment 16

> Username: ckormanyos  
> Created at: 2025-07-13 11:01:02 UTC  
> Updated at: 2025-07-13 11:01:27 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/718#issuecomment-3066971443  

But for that scope of change, I suspect we'd need to wait for 1.90. For the moment, we could use my modified mixed complex/real code, as hinted above.

---

## Comment 17

> Username: ckormanyos  
> Created at: 2025-07-13 11:27:05 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/718#issuecomment-3066987534  

> for integer and floating point types where the std provided (built in) types aren't in namespace std then yes, we do need the using declarations.  
  
Hi John (@jzmaddock) I have a question about my own posted code here. In my posting, I also call one of the templates (`elementary_functions`) for the built-in type `float`. Why don't we need to use `using` for `exp()`, `log()` and all of those in this case? At least my compilers did not force me to use `using`.

---

## Comment 18

> Username: jzmaddock  
> Created at: 2025-07-13 12:30:05 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/718#issuecomment-3067019983  

> Hi John (@jzmaddock) I have a question about my own posted code here. In my posting, I also call one of the templates (elementary_functions) for the built-in type float. Why don't we need to use using for exp(), log() and all of those in this case? At least my compilers did not force me to use using.  
  
You do: it's a silent bug without the using declaration.  
  
Basically, without the using declaration you will call `::log(double)` regardless of whether your type is float/double or long double.  This is relatively harmless for type `float`, perfect for `double` and potentially a serious bug for `long double`.  It's one of the first gotcha's Paul Bristow and I hit in Boost.Math, so I wrote `std_real_concept` as a concept-checking tool which catches those errors.  It really pisses people off when their apparently perfectly compiling code fails that check, but it is actually needed ;)

---

## Comment 19

> Username: ckormanyos  
> Created at: 2025-07-13 13:48:17 UTC  
> Updated at: 2025-07-13 13:48:31 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/718#issuecomment-3067059220  

> You do: it's a silent bug without the using declaration.  
  
What a nightmare. That is a tricky one. I can still find this bug on GCC `x86_64` trunk at [Godbolt](https://godbolt.org/z/d3xnPW3MW). I find the bug locally on GCC too. You can observe it _in_ _action_  where `long double` is ten bytes (as it is on these platforms).  
  
If I can still find this bug on GCC and clang trunk, are we talking about a language bug or bug(s) in all these compiler implementations?

---

## Comment 20

> Username: jzmaddock  
> Created at: 2025-07-13 14:49:38 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/718#issuecomment-3067092321  

It's not so much a bug as a deliberate standardization choice: C++ maintains compatibility with the C headers, so math.h will always make `::log(double)` visible, and we can never really be sure that someone/something outside our control hasn't included that before us.  It used to be the case that `#include <cmath>` was NOT supposed to make the contents of math.h visible, but everyone widely ignored that, since the obvious way to implement <cmath> is in terms of math.h.  Remember also that math.h is typically supplied by the system, where as cmath comes from the compiler vendor who may have zero control over the C headers.

---

## Comment 21

> Username: LegalizeAdulthood  
> Created at: 2025-07-13 15:25:06 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/718#issuecomment-3067111139  

> Then you might actually need to use using?  
  
In generic code you do need to have the `using` because with `std::complex<double>` you're calling `std::sin` and with `boost::multiprecision::cpp_complex` you're calling `boost::multiprecision::sin`.

---

## Comment 22

> Username: jzmaddock  
> Created at: 2025-07-13 15:33:07 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/718#issuecomment-3067115723  

>In generic code you do need to have the using because with std::complex<double> you're calling std::sin and with boost::multiprecision::cpp_complex you're calling boost::multiprecision::sin.  
  
For float/double etc yes, for `std::complex<>` no, because the std:: versions will be found via ADL and anything in the global namespace - ::sin for example, will not be a viable overload.

---

## Comment 23

> Username: jzmaddock  
> Created at: 2025-07-13 16:02:45 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/718#issuecomment-3067130268  

For example I can gut the example down to:  
  
```  
#include <iostream>  
#include <complex>  
#include <boost/multiprecision/cpp_complex.hpp>  
  
template<class Complex>  
void complex_number_examples()  
{  
    Complex z1{0, 1};  
    std::cout << std::setprecision(std::numeric_limits<typename Complex::value_type>::digits10);  
    std::cout << std::scientific << std::fixed;  
    std::cout << "Print a complex number: " << z1 << std::endl;  
    std::cout << "Square it             : " << z1*z1 << std::endl;  
    std::cout << "Real part             : " << z1.real() << " = " << real(z1) << std::endl;  
    std::cout << "Imaginary part        : " << z1.imag() << " = " << imag(z1) << std::endl;  
    std::cout << "Absolute value        : " << abs(z1) << std::endl;  
    std::cout << "Argument              : " << arg(z1) << std::endl;  
    std::cout << "Norm                  : " << norm(z1) << std::endl;  
    std::cout << "Complex conjugate     : " << conj(z1) << std::endl;  
    std::cout << "Projection onto Riemann sphere: " <<  proj(z1) << std::endl;  
    typename Complex::value_type r = 1;  
    typename Complex::value_type theta = 0.8;  
    // We need a using declaration here, since polar is called with a scalar:  
    using std::polar;  
    std::cout << "Polar coordinates (phase = 0)    : " << polar(r) << std::endl;  
    std::cout << "Polar coordinates (phase !=0)    : " << polar(r, theta) << std::endl;  
  
    std::cout << "\nElementary special functions:\n";  
    std::cout << "exp(z1) = " << exp(z1) << std::endl;  
    std::cout << "log(z1) = " << log(z1) << std::endl;  
    std::cout << "log10(z1) = " << log10(z1) << std::endl;  
    std::cout << "pow(z1, z1) = " << pow(z1, z1) << std::endl;  
    std::cout << "Take its square root  : " << sqrt(z1) << std::endl;  
    std::cout << "sin(z1) = " << sin(z1) << std::endl;  
    std::cout << "cos(z1) = " << cos(z1) << std::endl;  
    std::cout << "tan(z1) = " << tan(z1) << std::endl;  
    std::cout << "asin(z1) = " << asin(z1) << std::endl;  
    std::cout << "acos(z1) = " << acos(z1) << std::endl;  
    std::cout << "atan(z1) = " << atan(z1) << std::endl;  
    std::cout << "sinh(z1) = " << sinh(z1) << std::endl;  
    std::cout << "cosh(z1) = " << cosh(z1) << std::endl;  
    std::cout << "tanh(z1) = " << tanh(z1) << std::endl;  
    std::cout << "asinh(z1) = " << asinh(z1) << std::endl;  
    std::cout << "acosh(z1) = " << acosh(z1) << std::endl;  
    std::cout << "atanh(z1) = " << atanh(z1) << std::endl;  
}  
  
int main()  
{  
    std::cout << "First, some operations we usually perform with std::complex:\n";  
    complex_number_examples<std::complex<double>>();  
    std::cout << "\nNow the same operations performed using quad precision complex numbers:\n";  
    complex_number_examples<boost::multiprecision::cpp_complex_quad>();  
  
    return 0;  
}  
```  
  
And everything works just fine.  Just the one using declaration needed, since we demonstrate the use of std::polar on a scalar (non-complex) tyoe.

---

## Comment 24

> Username: ckormanyos  
> Created at: 2025-07-13 16:10:04 UTC  
> Updated at: 2025-07-13 16:11:01 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/718#issuecomment-3067134093  

I appreciate the reduced example. And, yes, you will always need to be `using` the `polar` function.  
  
I think one of Richard's (@LegalizeAdulthood) points was to exemplify the use of `using`.  
  
As we evolve, I/we can commit to modernizing the examples. In that movement, we will get more generic and use `using` more frequently and more consistently (as we make certain examples more generic).  
  
My final question is:  
  - Is the reduced example from John agreed?  
  - It's OK for me.

---

## Comment 25

> Username: ckormanyos  
> Created at: 2025-07-13 18:05:38 UTC  
> Updated at: 2025-07-13 18:06:56 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/718#issuecomment-3067200699  

Just thinking a bit out loud here, ...  
  
Richard (@LegalizeAdulthood) had specifically pointed out the crossover of generic code working for complex/real/built-in/multiprecision types. It might be even better if we left this example terse.  
  
Then we could fabricate a new example that crosses over real/complex?  
  
Part of me thinks we are trying to do two unrelated things with this example. This example was all about `compex_adaptor`. The topic of crossing over templates real/complex is a different topic, probably deserving of a separate example. So I think we need two examples to really get down to the points of each.  
  
Thoughts?

---

## Comment 26

> Username: LegalizeAdulthood  
> Created at: 2025-07-14 02:03:21 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/718#issuecomment-3067536824  

Having one example that demonstrates the `using` trick when necessary, would be good.  
  
I like that the complex example invokes lots of math functions, because otherwise you need to dig into the reference for `number` to see all the functions that are supported.  Many people skip the reference pages and only read the tutorial pages.

---

## Comment 27

> Username: ckormanyos  
> Created at: 2025-07-14 07:50:33 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/718#issuecomment-3068215548  

> Having one example that demonstrates the `using` trick when necessary, would be good.  
  
See #725
