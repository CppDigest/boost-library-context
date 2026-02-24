# #262 - Incorrect results of complex tan, complex tanh functions. [Closed]

> Username: cosurgi  
> Created at: 2020-08-03 21:32:25 UTC  
> Updated at: 2020-08-10 15:53:45 UTC  
> Closed at: 2020-08-10 15:53:45 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/262  

Hi @jzmaddock , I followed [your advice](https://github.com/boostorg/multiprecision/pull/249#discussion_r439817643) to use float_distance when measuring ULP error. I have [implemented this check](https://gitlab.com/cosurgi/trunk/-/blob/bitCorrectnessFix162/py/high-precision/_RealHPDiagnostics.cpp#L359). For nearly all functions it was enough to set tolerance of [8 ULP](https://gitlab.com/cosurgi/trunk/-/blob/bitCorrectnessFix162/py/tests/testMath.py#L463) with two notable excepions: real part of `tan` and imag part of `tanh` functions. See the pipeline warnings (scroll up the output to around line 360):  
  
* for [float128](https://gitlab.com/cosurgi/trunk/-/jobs/669025543#L361)  
* for [mpfr](https://gitlab.com/cosurgi/trunk/-/jobs/669025545#L371)  
* and [for cpp_bin_float](https://gitlab.com/cosurgi/trunk/-/jobs/669025547#L362) there are w few more errors.  
  
Here is a short snippet to reproduce these errors:  
  
```  
#include <boost/core/demangle.hpp>  
#include <boost/math/complex.hpp>  
#include <boost/math/special_functions.hpp>  
#include <boost/multiprecision/float128.hpp>  
#include <boost/multiprecision/mpfr.hpp>  
#include <iostream>  
  
template <typename T, typename Ref> void test()  
{  
	bool first = true;  
	for (T x = -10.; x < 10.; x += 0.25)  
		for (T y = -10.; y < 10.; y += 0.25) {  
			std::complex<T>   z(x, y);  
			std::complex<Ref> r(static_cast<Ref>(x), static_cast<Ref>(y));  
			using boost::multiprecision::tan;  
			using boost::multiprecision::tanh;  
			auto z_tan  = tan(z).real();  
			auto r_tan  = tan(r).real();  
			auto z_tanh = tanh(z).imag();  
			auto r_tanh = tanh(r).imag();  
			if (first) {  
				std::cout << boost::core::demangle(typeid(decltype(z_tan)).name()) << "\n";  
				std::cout << boost::core::demangle(typeid(decltype(r_tan)).name()) << "\n";  
				first = false;  
			}  
			auto dist_tan  = boost::math::float_distance(static_cast<T>(r_tan), z_tan);  
			auto dist_tanh = boost::math::float_distance(static_cast<T>(r_tanh), z_tanh);  
			if (dist_tan > 290000000)  
				std::cout << "tan  error, args=" << z << " ULP dist=" << dist_tan << "\n";  
			if (dist_tanh > 290000000)  
				std::cout << "tanh error, args=" << z << " ULP dist=" << dist_tanh << "\n";  
		}  
	std::cout << "\n";  
}  
  
int main()  
{  
	test<boost::multiprecision::float128, boost::multiprecision::mpfr_float_50>();  
	test<boost::multiprecision::mpfr_float_50, boost::multiprecision::mpfr_float_100>();  
	test<boost::multiprecision::mpfr_float_100, boost::multiprecision::mpfr_float_500>();  
}  
```  
  
Here I print only ULP errors larger than 290000000, otherwise it's a real flood. And all other functions pass with ULP error < 8 (except for cpp_bin_float - there I've set the limit to 200 with some [exceptions for tgamma and lgamma](https://gitlab.com/cosurgi/trunk/-/blob/bitCorrectnessFix162/py/tests/testMath.py#L466)).  
  
Looks like I have stumbled upon a pretty large bug :).  
  
This sample program produces following output:  
  
```  
boost::multiprecision::number<boost::multiprecision::backends::float128_backend, (boost::multiprecision::expression_template_option)0>  
boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<50u, (boost::multiprecision::mpfr_allocation_type)1>, (boost::multiprecision::expression_template_option)1>  
tanh error, args=(-10,-2.75) ULP dist=3.21107e+08  
tanh error, args=(-10,3.25) ULP dist=3.36694e+08  
tanh error, args=(-10,6.5) ULP dist=3.85652e+08  
tanh error, args=(-9.75,-0.25) ULP dist=3.41471e+08  
tan  error, args=(-2.75,-10) ULP dist=3.21107e+08  
tan  error, args=(-0.25,-9.75) ULP dist=3.41471e+08  
tan  error, args=(-0.25,9.75) ULP dist=3.41471e+08  
tan  error, args=(3.25,-10) ULP dist=3.36694e+08  
tan  error, args=(6.5,-10) ULP dist=3.85652e+08  
tanh error, args=(9.75,-0.25) ULP dist=3.41471e+08  
  
boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<50u, (boost::multiprecision::mpfr_allocation_type)1>, (boost::multiprecision::expression_template_option)1>  
boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<100u, (boost::multiprecision::mpfr_allocation_type)1>, (boost::multiprecision::expression_template_option)1>  
tanh error, args=(-10,-7.75) ULP dist=2.90069e+08  
tan  error, args=(-7.75,-10) ULP dist=2.90069e+08  
  
boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<100u, (boost::multiprecision::mpfr_allocation_type)1>, (boost::multiprecision::expression_template_option)1>  
boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<500u, (boost::multiprecision::mpfr_allocation_type)1>, (boost::multiprecision::expression_template_option)1>  
tanh error, args=(-9.75,-6.25) ULP dist=4.31581e+08  
tanh error, args=(-9.75,-5.75) ULP dist=3.09252e+08  
tanh error, args=(-9.75,4) ULP dist=3.86202e+08  
tan  error, args=(-6.25,-9.75) ULP dist=4.31581e+08  
tan  error, args=(-6.25,9.75) ULP dist=4.31581e+08  
tan  error, args=(-5.75,-9.75) ULP dist=3.09252e+08  
tan  error, args=(-5.75,9.75) ULP dist=3.09252e+08  
tan  error, args=(4,-9.75) ULP dist=3.86202e+08  
tan  error, args=(4,9.75) ULP dist=3.86202e+08  
tanh error, args=(9.75,-6.25) ULP dist=4.31581e+08  
tanh error, args=(9.75,-5.75) ULP dist=3.09252e+08  
tanh error, args=(9.75,4) ULP dist=3.86202e+08  
```

---

## Comment 1

> Username: NAThompson  
> Created at: 2020-08-03 21:46:14 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/262#issuecomment-668257643  

`tan` is ill-conditioned in the regions you are seeing bad error; do `Plot[Abs[x*Sec[x]^2/Tan[x]], {x,0,10}]` in wolframalpha see the condition number of function evaluation.

---

## Comment 2

> Username: cosurgi  
> Created at: 2020-08-03 21:58:45 UTC  
> Updated at: 2020-08-04 09:34:46 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/262#issuecomment-668262117  

Here's a gnuplot plot of a slightly modified version of the code above. The errors are in entire domain.  
  
```  
#include <boost/core/demangle.hpp>  
#include <boost/math/complex.hpp>  
#include <boost/math/special_functions.hpp>  
#include <boost/multiprecision/float128.hpp>  
#include <boost/multiprecision/mpfr.hpp>  
#include <iostream>  
  
template <typename T, typename Ref> void test()  
{  
	for (T x = -10.; x < 10.; x += 0.25) {  
		for (T y = -10.; y < 10.; y += 0.25) {  
			std::complex<T>   z(x, y);  
			std::complex<Ref> r(static_cast<Ref>(x), static_cast<Ref>(y));  
			using boost::multiprecision::tan;  
			using boost::multiprecision::tanh;  
			auto z_tan    = tan(z).real();  
			auto r_tan    = tan(r).real();  
			auto dist_tan = boost::math::float_distance(static_cast<T>(r_tan), z_tan);  
			std::cout << z.real() << " " << z.imag() << " " << dist_tan << "\n";  
		}  
		std::cout << "\n";  
	}  
}  
  
int main() { test<boost::multiprecision::float128, boost::multiprecision::mpfr_float_50>(); }  
```  
  
![scr_0332](https://user-images.githubusercontent.com/1047765/89231222-2b5d8e00-d5e5-11ea-99b9-6deaa95ae707.png)  
  
I also made a denser plot with loops having `+=0.05`:  
  
![scr_0333](https://user-images.githubusercontent.com/1047765/89231458-a9ba3000-d5e5-11ea-9ba0-42d7f6680554.png)  
  
I used following gnuplot command:  
```  
splot '/tmp/ulp_tan_err.dat' u ($1):($2):(log($3)/log(2)) w l  
```  
  
note: this problem is not present in `double` and `long double`

---

## Comment 3

> Username: NAThompson  
> Created at: 2020-08-03 23:22:11 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/262#issuecomment-668287519  

You need to plot the ulp envelope to determine if those are acceptable or not.  
  
Try this: https://github.com/boostorg/math/blob/develop/include/boost/math/tools/ulps_plot.hpp  
  
Example: https://github.com/boostorg/math/blob/develop/example/airy_ulps_plot.cpp

---

## Comment 4

> Username: jzmaddock  
> Created at: 2020-08-04 16:18:15 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/262#issuecomment-668691637  

There's a number of issues here:  
  
* You're using `std::complex<UDT>` which invokes undefined behaviour.  
* Because you're using `std::complex` you're calling your std lib's version of tan and tanh.  
* For `float128` and `mpfr_float` all the std lib functions are evaluated by the quadmath and mpfr libraries rather than Boost.Multiprecision.  
  
How do things look if you use `mpc_complex` and/or `complex128` ?   See https://www.boost.org/doc/libs/1_73_0/libs/multiprecision/doc/html/boost_multiprecision/tut/complex.html.

---

## Comment 5

> Username: jzmaddock  
> Created at: 2020-08-04 17:27:13 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/262#issuecomment-668726268  

Interestingly your test program passes under msvc even when the tolerances are reduced to say 30.  I'm betting there's a hard coded value of PI at double precision in your std lib's `std::complex` implementation.

---

## Comment 6

> Username: cosurgi  
> Created at: 2020-08-04 18:24:35 UTC  
> Updated at: 2020-08-04 19:47:07 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/262#issuecomment-668753486  

Thanks, I checked that (previous) code on two versions:  
  
g++-10 (Debian 10.1.0-4) 10.1.0 with boost 1.71  
g++ (Debian 8.3.0-6) 8.3.0 with boost 1.67  
  
They both reproduce this problem. I suppose that they use their respective stdlib's versions. Probably they use `long double` version of PI, because that's how many digits it can make correct.  
  
I assume that you mean `std::complex<User Defined Type>` :) I checked [your complex tutorial](https://www.boost.org/doc/libs/1_73_0/libs/multiprecision/doc/html/boost_multiprecision/tut/complex.html), I missed it before because I mainly worked with boost 1.67. Sorry about that.  
  
> How do things look if you use mpc_complex and/or complex128 ?  
  
So here's a new version of the problematic code:  
  
```  
#include <boost/core/demangle.hpp>  
#include <boost/multiprecision/complex128.hpp>  
#include <boost/multiprecision/mpc.hpp>  
#include <iostream>  
  
template <typename T, typename Ref, typename C> void test(C tolerance)  
{  
	using Tr   = typename T::value_type;  
	using Refr = typename Ref::value_type;  
	bool first = true;  
	for (Tr x = -10.; x < 10.; x += 0.25)  
		for (Tr y = -10.; y < 10.; y += 0.25) {  
			T   z(x, y);  
			Ref r(static_cast<Refr>(x), static_cast<Refr>(y));  
			// this is to force ↓ evaluation of boost::multiprecision::detail::function (not sure why?)  
			auto z_tan  = static_cast<T  >(boost::multiprecision::tan(z) ).real();  
			auto r_tan  = static_cast<Ref>(boost::multiprecision::tan(r) ).real();  
			auto z_tanh = static_cast<T  >(boost::multiprecision::tanh(z)).imag();  
			auto r_tanh = static_cast<Ref>(boost::multiprecision::tanh(r)).imag();  
			if (first) {  
				std::cout << boost::core::demangle(typeid(decltype(z_tan)).name()) << "\n";  
				std::cout << boost::core::demangle(typeid(decltype(r_tan)).name()) << "\n";  
				first = false;  
			}  
			auto dist_tan  = boost::math::float_distance(static_cast<Tr>(r_tan), z_tan);  
			auto dist_tanh = boost::math::float_distance(static_cast<Tr>(r_tanh), z_tanh);  
			if (dist_tan > tolerance)  
				std::cout << "tan  error, args=" << z << " ULP dist=" << dist_tan << "\n";  
			if (dist_tanh > tolerance)  
				std::cout << "tanh error, args=" << z << " ULP dist=" << dist_tanh << "\n";  
		}  
	std::cout << "\n";  
}  
  
int main()  
{  
	test<boost::multiprecision::complex128, boost::multiprecision::mpc_complex_50>(290000000);  
	test<boost::multiprecision::mpc_complex_50, boost::multiprecision::mpc_complex_100>(4);  
	test<boost::multiprecision::mpc_complex_100, boost::multiprecision::mpc_complex_500>(4);  
}  
```  
  
I tested it with g++-10 and boost 1.71, I compiled it with:  
  
```  
g++-10 ./prec_test_boost_multiprec_issue_262.cpp -o prec_test_boost_multiprec_issue_262 -std=gnu++17 -lboost_system -lmpfr -lquadmath -lmpc -Werror -Wformat -Wformat-security -Wformat-nonliteral -Wall -Wextra -Wnarrowing -Wreturn-type -Wuninitialized -Wfloat-conversion -Wcast-align -Wdisabled-optimization -Wtrampolines -Wpointer-arith -Wswitch-bool -Wwrite-strings -Wnon-virtual-dtor -Wreturn-local-addr  
```  
  
Here's the result:  
  
```  
boost::multiprecision::number<boost::multiprecision::backends::float128_backend, (boost::multiprecision::expression_template_option)0>  
boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<50u, (boost::multiprecision::mpfr_allocation_type)1>, (boost::multiprecision::expression_template_option)1>  
tanh error, args=(-10,-2.75) ULP dist=4.13155e+08  
tanh error, args=(-10,6.5) ULP dist=4.61511e+08  
tanh error, args=(-10,6.75) ULP dist=4.72126e+08  
tanh error, args=(-10,7.75) ULP dist=4.27246e+08  
tan  error, args=(-2.75,-10) ULP dist=4.13155e+08  
tan  error, args=(6.5,-10) ULP dist=4.61511e+08  
tan  error, args=(6.75,-10) ULP dist=4.72126e+08  
tan  error, args=(7.75,-10) ULP dist=4.27246e+08  
  
boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<50u, (boost::multiprecision::mpfr_allocation_type)1>, (boost::multiprecision::expression_template_option)1>  
boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<100u, (boost::multiprecision::mpfr_allocation_type)1>, (boost::multiprecision::expression_template_option)1>  
  
boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<100u, (boost::multiprecision::mpfr_allocation_type)1>, (boost::multiprecision::expression_template_option)1>  
boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<500u, (boost::multiprecision::mpfr_allocation_type)1>, (boost::multiprecision::expression_template_option)1>  
```  
  
The problem disappeared with `mpc_complex`, but there is still a problem with `complex128`, any hints?

---

## Comment 7

> Username: cosurgi  
> Created at: 2020-08-04 18:27:45 UTC  
> Updated at: 2020-08-05 11:33:01 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/262#issuecomment-668754898  

I link here with `-lquadmath`, should I link with something else to make sure that `complex128` gets what it wants? But in that case I wonder why I don't get a linker error.  
  
EDIT: I forgot to take the absolute value of `float_distance`. The results are very similar though, only a few more errors appear. I can edit that example code a bit if you want, also change names to be more readable.

---

## Comment 8

> Username: jzmaddock  
> Created at: 2020-08-05 17:01:15 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/262#issuecomment-669312255  

I see the issue: if we take `tan(-8.75, -10)` as the canonical case, then it's cancellation error during the evaluation of the division of sin/cos - it's losing about 8 decimal places during the division.  
  
Interestingly, if we evaluate at double precision using `std::complex` (MSVC version), then the sin/cos division also loses precision, and I don't at present see what we can do about that.  MSVC's tan/tanh implementation uses a different method though which superficially looks much better... I need to investigate that, there are actually a wide range of possible formulas which could be used, it's figuring out which is best and avoid unintended consequences!

---

## Comment 9

> Username: cosurgi  
> Created at: 2020-08-06 11:46:49 UTC  
> Updated at: 2020-08-06 11:46:55 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/262#issuecomment-669879192  

I wonder why it works for `mpc_complex`. Do they use extra precision, or do they have a more stable formula?

---

## Comment 10

> Username: jzmaddock  
> Created at: 2020-08-06 20:57:33 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/262#issuecomment-670188144  

>I wonder why it works for mpc_complex. Do they use extra precision, or do they have a more stable formula?  
  
Both, I think.

---

## Comment 11

> Username: cosurgi  
> Created at: 2020-08-07 17:55:23 UTC  
> Updated at: 2020-08-07 18:32:48 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/262#issuecomment-670636953  

I **almost** identified a couple of similar errors for non-complex functions of `boost::multiprecision::number<boost::multiprecision::cpp_bin_float<62>, boost::multiprecision::et_off>` (I was testing 62 decimal places, so that these tests will be later useful with #184) I was testing against  `boost::multiprecision::number<boost::multiprecision::cpp_bin_float<124>, boost::multiprecision::et_off>`.  
  
The suspects are:  
  
* `sin`, `cos`, `tan` with 7e7 ULP error  
* `acos` with 10000 ULP error  
* `erfc` with 20000 ULP error  
* `lgamma` with 5e9 ULP error  
* `tgamma` with 10000 ULP error  
* `fma` with 2e5 ULP error  
  
All other `cpp_bin_float<62>` functions that I tested [[1]](https://gitlab.com/yade-dev/trunk/-/blob/bitCorrectness/py/high-precision/_RealHPDiagnostics.cpp#L432) have ULP error below 127.  
  
Also MPFR 62 [2] did not have such errors, when tested with the same arguments.  
  
I am now rerunning these tests with Boost 1.71 and g++-10 to be sure that I made no mistake.  
  
If I become certain and have working short examples producing these errors, should I open another issue for that?  
  
[1] https://gitlab.com/yade-dev/trunk/-/blob/c7f833ad810afc61b647bca3e3c19f3b3d09a97c/py/high-precision/_RealHPDiagnostics.cpp#L432  
[2] `boost::multiprecision::number<boost::multiprecision::mpfr_float_backend<62, boost::multiprecision::allocate_stack>, boost::multiprecision::et_off>`

---

## Comment 12

> Username: jzmaddock  
> Created at: 2020-08-07 19:04:12 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/262#issuecomment-670666152  

Yes please.  Note however that most of those functions have regions that  
are ill-conditioned.  I'm surprised about erf though.  
  
<http://www.avg.com/email-signature?utm_medium=email&utm_source=link&utm_campaign=sig-email&utm_content=webmail>  
Virus-free.  
www.avg.com  
<http://www.avg.com/email-signature?utm_medium=email&utm_source=link&utm_campaign=sig-email&utm_content=webmail>  
<#DAB4FAD8-2DD7-40BB-A1B8-4E2AA1F9FDF2>  
  
On Fri, 7 Aug 2020 at 18:55, Janek Kozicki <notifications@github.com> wrote:  
  
> I *almost* identified a couple of similar errors for non-complex  
> functions of boost::multiprecision::number<boost::multiprecision::cpp_bin_float<62>,  
> boost::multiprecision::et_off> (I was testing 62 decimal places, so that  
> these tests will be later useful with #184  
> <https://github.com/boostorg/multiprecision/issues/184>) I was testing  
> against boost::multiprecision::number<boost::multiprecision::cpp_bin_float<124>,  
> boost::multiprecision::et_off>.  
>  
> The suspects are:  
>  
>    - sin, cos, tan with 7e7 ULP error  
>    - acos with 10000 ULP error  
>    - erfc with 20000 ULP error  
>    - lgamma with 5e9 ULP error  
>    - tgamma with 10000 ULP error  
>    - fma with 2e5 ULP error  
>  
> All other cpp_bin_float<62> functions have ULP error below 127.  
>  
> Also MPFR 62 did not have such errors (so far).  
>  
> I am now rerunning these tests with Boost 1.71 and g++-10 to be sure that  
> I made no mistake.  
>  
> If I become certain and have working short examples producing these  
> errors, should I open another issue for that?  
>  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/multiprecision/issues/262#issuecomment-670636953>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/ABGHSOBLZWDBXL32TEVBWMDR7Q5ZRANCNFSM4PTXWQDQ>  
> .  
>

---

## Comment 13

> Username: cosurgi  
> Created at: 2020-08-08 00:58:06 UTC  
> Updated at: 2020-08-08 01:15:43 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/262#issuecomment-670799750  

A glimpse of these errors is here: https://gitlab.com/cosurgi/trunk/-/jobs/676648142 and https://gitlab.com/cosurgi/trunk/-/jobs/676832836  
  
but in that pipeline these are shorter tests, and they are done on `cpp_bin_float<45>`.  
  
Please ignore the `std::complex` errors shown there, they suffer problems which you listed [above](https://github.com/boostorg/multiprecision/issues/262#issuecomment-668691637), I'll fix this on my side ;)  
  
EDIT: this is the same test on MPFR 150: https://gitlab.com/cosurgi/trunk/-/jobs/676625239 and https://gitlab.com/cosurgi/trunk/-/jobs/676832835
