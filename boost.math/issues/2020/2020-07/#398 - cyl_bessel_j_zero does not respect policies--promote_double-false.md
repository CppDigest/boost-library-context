# #398 - cyl_bessel_j_zero does not respect policies::promote_double<false> [Closed]

> Username: evanmiller  
> Created at: 2020-07-08 17:15:26 UTC  
> Updated at: 2020-07-31 17:55:50 UTC  
> Closed at: 2020-07-31 17:55:50 UTC  
> Url: https://github.com/boostorg/math/issues/398  

I'm auditing a code base to eliminate uses of `long double` on x86 (i.e. eliminate all x87 instructions). After compiling, I dump the disassembly and look for forbidden instructions and function calls.  
  
I found that the following small test program - which I am led to believe by Boost documentation will not promote doubles to long doubles - includes calls to a number of `long double` functions when compiled.  
  
```  
#include <boost/math/special_functions/bessel.hpp>  
  
int main() {  
    typedef boost::math::policies::policy<  
        boost::math::policies::promote_double<false>  
        > numeric_policy;  
  
    boost::math::cyl_bessel_j_zero((double)0.5, 1, numeric_policy());  
}  
```  
  
A sampling of disassembly:  
  
```  
$  otool -t -V a.out | grep 'long double' | head -n 5  
00000001000039a0	callq	__ZN5boost4math7lanczos19lanczos_initializerINS1_12lanczos17m64EeE4initC2Ev ## boost::math::lanczos::lanczos_initializer<boost::math::lanczos::lanczos17m64, long double>::init::init()  
000000010000db3e	callq	__ZL4fabse ## fabs(long double)  
000000010000db67	callq	__ZL3cose ## cos(long double)  
000000010000db98	callq	__ZL5floore ## floor(long double)  
000000010000dc5d	callq	__ZL3sine ## sin(long double)  
```  
  
Is this expected behavior, or a bug?  
  
I realize that I can disable x87 with a compiler flag - but my compiler is actually crashing when I try that. So I am hoping to resolve the issue within Boost.

---

## Comment 1

> Username: NAThompson  
> Created at: 2020-07-08 17:24:54 UTC  
> Updated at: 2020-07-08 17:27:33 UTC  
> Url: https://github.com/boostorg/math/issues/398#issuecomment-655652749  

The Lanczos one I recognize: It's required. There is an ill-conditioned filter computation that really does need that extra precision; see [here](https://github.com/boostorg/math/blob/develop/include/boost/math/differentiation/lanczos_smoothing.hpp#L264). Looks like its also responsible for the call to `abs`.

---

## Comment 2

> Username: evanmiller  
> Created at: 2020-07-08 17:35:27 UTC  
> Url: https://github.com/boostorg/math/issues/398#issuecomment-655658068  

I am porting code to a platform without native 80-bit or 128-bit (i.e. 'long double' is equivalent to 'double').  
  
Will the Lanczos code fail to converge on such a platform?

---

## Comment 3

> Username: NAThompson  
> Created at: 2020-07-08 17:40:01 UTC  
> Url: https://github.com/boostorg/math/issues/398#issuecomment-655660217  

@evanmiller : It won't fail to converge, the filter coefficients will just not be as accurate. For digital filters, we generally want half-ulp accuracy precomputed once, under the assumption that applying the filter to data will dominate the execution time.

---

## Comment 4

> Username: evanmiller  
> Created at: 2020-07-08 17:51:58 UTC  
> Url: https://github.com/boostorg/math/issues/398#issuecomment-655666309  

@NAThompson Is it reasonable to ask that this coefficient computation respect the `promote_double` policy? It is important for my application that the same result is returned on all platforms, even if that result is not as accurate as it could be.

---

## Comment 5

> Username: NAThompson  
> Created at: 2020-07-08 17:55:50 UTC  
> Updated at: 2020-07-08 17:59:30 UTC  
> Url: https://github.com/boostorg/math/issues/398#issuecomment-655668095  

There's almost zero hope you'll get the same result on all platforms, even if it does respect `promote_double`; see (for one of many reasons) [here](https://lemire.me/blog/2020/06/26/gcc-not-nearest/).  
  
The semantics of `promote_double` are somewhat different than what is being done in the Lanczos filters. The `promote_double` gives the option for a user to obtain half-ulp accuracy in a computation, rather than being limited to a weaker accuracy governed by the condition number of function evaluation.  
  
In the Lanczos filters, we're using argument promotion just to achieve *accuracy*, because the filter computation is very ill-conditioned.

---

## Comment 6

> Username: evanmiller  
> Created at: 2020-07-08 18:22:27 UTC  
> Url: https://github.com/boostorg/math/issues/398#issuecomment-655680637  

> There's almost zero hope you'll get the same result on all platforms, even if it does respect `promote_double`; see (for one of many reasons) [here](https://lemire.me/blog/2020/06/26/gcc-not-nearest/).  
  
Well, I don't need bit-for-bit compatibility. But if there are indeed significant accuracy issues introduced without 80-bit long double, I would like to have control over it for the sake of general cross-platform consistency.  
  
It seems to me that because `promote_double` defaults to `true`, a user who sets `false` is indicating a desire not to use long doubles internally.  
  
In any event, if the current algorithm only guarantees accuracy when 80-bit registers are present, then it appears I will need to find another algorithm.

---

## Comment 7

> Username: NAThompson  
> Created at: 2020-07-08 18:28:27 UTC  
> Updated at: 2020-07-08 18:29:36 UTC  
> Url: https://github.com/boostorg/math/issues/398#issuecomment-655683430  

> In any event, if the current algorithm only guarantees accuracy when 80-bit registers are present, then it appears I will need to find another algorithm.  
  
I did a very deep lit search for this algorithm, and I couldn't find another one that had the same performance and capabilities. So if you do go find another where you can get the filter coeffs without an ill-conditioned step, I'd be very happy about it.

---

## Comment 8

> Username: evanmiller  
> Created at: 2020-07-08 18:40:01 UTC  
> Url: https://github.com/boostorg/math/issues/398#issuecomment-655688876  

> I did a very deep lit search for this algorithm, and I couldn't find another one that had the same performance and capabilities. So if you do go find another where you can get the filter coeffs without an ill-conditioned step, I'd be very happy about it.  
  
Well, I'm just looking for a pure 64-bit Bessel algorithm - I'm not trying to reinvent the Lanczos wheel. It could be something as simple as replacing Boost's lgamma with the system's lgamma.  
  
I will need to investigate more to see exactly where the long double dependencies are for the functions that I'm calling.

---

## Comment 9

> Username: NAThompson  
> Created at: 2020-07-08 18:41:46 UTC  
> Url: https://github.com/boostorg/math/issues/398#issuecomment-655689730  

@evanmiller : Actually I think we're talking about a different Lanczos algorithm. I'm pretty sure there are no filter coefficients needed there. Probably @jzmaddock will know what the Bessel function is doing.

---

## Comment 10

> Username: jzmaddock  
> Created at: 2020-07-08 18:43:22 UTC  
> Url: https://github.com/boostorg/math/issues/398#issuecomment-655690442  

This does look like a bug to me, @NAThompson the code in question is my old lanczos gamma function approximation, and it should be using a double precision approximation in this case IMO.  @evanmiller can you get a call stack for where the long double calls occur?

---

## Comment 11

> Username: evanmiller  
> Created at: 2020-07-08 19:12:49 UTC  
> Url: https://github.com/boostorg/math/issues/398#issuecomment-655703853  

@jzmaddock I don't have a stack trace handy; I'm just disassembling a static binary right now.  
  
I *think* I hit some Lanczos code through this code path earlier today though:  
  
https://github.com/boostorg/math/blob/develop/include/boost/math/special_functions/detail/bessel_ik.hpp#L63  
  
I can post suspicious disassembly here as I come across it. It looks like there is a similar issue affecting the `beta` function.

---

## Comment 12

> Username: evanmiller  
> Created at: 2020-07-08 19:32:53 UTC  
> Url: https://github.com/boostorg/math/issues/398#issuecomment-655712905  

A long double of `cos_pi` is being compiled into the original test program. It looks like there are a number of places where `cos_pi` is not being passed a policy:  
  
```  
$ grep -rn cos_pi .  
./special_functions/detail/bessel_jy.hpp:409:            T cv = cos_pi(mod_v);  
./special_functions/detail/bessel_jy_derivatives_series.hpp:196:      prefix = boost::math::tgamma(-v, pol) * boost::math::cos_pi(v) * p / boost::math::constants::pi<T>();  
./special_functions/detail/bessel_jy_derivatives_asym.hpp:78:   const T ci = cos_pi(vd2shifted);  
./special_functions/detail/bessel_jy_derivatives_asym.hpp:111:   const T ci = cos_pi(vd2shifted);  
./special_functions/detail/bessel_jy_asym.hpp:83:   T ci = cos_pi(v / 2 + 0.25f);  
./special_functions/detail/bessel_jy_asym.hpp:115:   T ci = cos_pi(v / 2 + 0.25f);  
./special_functions/detail/bessel_jy_series.hpp:193:      prefix = boost::math::tgamma(-v, pol) * boost::math::cos_pi(v) * p / constants::pi<T>();  
```

---

## Comment 13

> Username: evanmiller  
> Created at: 2020-07-09 12:00:44 UTC  
> Url: https://github.com/boostorg/math/issues/398#issuecomment-656085850  

`cbrt` is another source of inadvertent double promotion.  
  
I'm making good progress eliminating the long doubles by passing policies around in more places - will open a PR later today.

---

## Comment 14

> Username: evanmiller  
> Created at: 2020-07-09 12:22:33 UTC  
> Url: https://github.com/boostorg/math/issues/398#issuecomment-656095485  

The `factorial` functions are another source of double promotion:  
  
https://github.com/boostorg/math/blob/6bb0e8dee02e48a3b637fde553929a4ac32b9ce3/include/boost/math/special_functions/detail/unchecked_factorial.hpp#L489-L500

---

## Comment 15

> Username: evanmiller  
> Created at: 2020-07-09 14:47:47 UTC  
> Url: https://github.com/boostorg/math/issues/398#issuecomment-656172637  

A small demonstration program is actually just:  
  
```C  
#include <boost/math/special_functions/beta.hpp>  
  
int main() { }  
```  
  
Based on the disassembly, it looks like an 80-bit Lanczos approximator is getting force-instantiated before any user code is called at all:  
  
```asm  
___cxx_global_var_init:  
0000000100002dd0        pushq   %rbp  
0000000100002dd1        movq    %rsp, %rbp  
0000000100002dd4        movq    0x1225(%rip), %rax ## literal pool symbol address: guard variable for boost::math::lanczos::lanczos_initializer<boost::math::lanczos::lanczos17m64, long double>::initializer  
0000000100002ddb        cmpb    $0x0, (%rax)  
0000000100002dde        jne     0x100002dfe  
0000000100002de4        movq    0x123d(%rip), %rdi ## literal pool symbol address: boost::math::lanczos::lanczos_initializer<boost::math::lanczos::lanczos17m64, long double>::initializer  
0000000100002deb        callq   boost::math::lanczos::lanczos_initializer<boost::math::lanczos::lanczos17m64, long double>::init::init() ## boost::math::lanczos::lanczos_initializer<boost::math::lanczos::lanczos17m64, long double>::init::init()  
0000000100002df0        movq    0x1209(%rip), %rax ## literal pool symbol address: guard variable for boost::math::lanczos::lanczos_initializer<boost::math::lanczos::lanczos17m64, long double>::initializer  
0000000100002df7        movq    $0x1, (%rax)  
0000000100002dfe        popq    %rbp  
0000000100002dff        retq  
```  
  
So it's possible that this Lanczos code is never actually hit in my test program.

---

## Comment 16

> Username: jzmaddock  
> Created at: 2020-07-09 18:10:48 UTC  
> Url: https://github.com/boostorg/math/issues/398#issuecomment-656274699  

I see this, here's a test program for you (requires current develop):  
  
```  
  
#include <type_traits>  
#include <cmath>  
  
#define BOOST_MATH_ASSERT_UNDEFINED_POLICY false  
//  
// Poison the long double std math functions so we can find accidental usage of these  
// when the user has requested that we do *not* use them.  
//  
// THIS IS NOT STD CONFORMING!!!  Is there a better way?  
//  
namespace std {  
      long double fabs(long double, void* = 0);  
      long double abs(long double, void* = 0);  
      long double sin(long double, void* = 0);  
      long double cos(long double, void* = 0);  
      long double tan(long double, void* = 0);  
      long double asin(long double, void* = 0);  
      long double acos(long double, void* = 0);  
      long double atan(long double, void* = 0);  
      long double exp(long double, void* = 0);  
      long double log(long double, void* = 0);  
      long double pow(long double, long double, void* = 0);  
}  
#define TEST_GROUP_5  
#define TEST_GROUP_6  
#include "compile_test/instantiate.hpp"  
  
int main()  
{  
   //boost::math::foo(0.0L);  
   instantiate(0.0);  
}  
  
```  
  
The instantiation call stack gives the source of the error(s), the first of which is caused by:  
  
```  
template <>  
inline float binomial_coefficient<float, policies::policy<> >(unsigned n, unsigned k, const policies::policy<>& pol)  
{  
   return policies::checked_narrowing_cast<float, policies::policy<> >(binomial_coefficient<double>(n, k, pol), "boost::math::binomial_coefficient<%1%>(unsigned,unsigned)");  
}  
```  
  
Which should be using `promote_double<false>` in it's forwarding-policy.  
  
Many thanks for looking into this!!

---

## Comment 17

> Username: evanmiller  
> Created at: 2020-07-09 18:34:50 UTC  
> Url: https://github.com/boostorg/math/issues/398#issuecomment-656285787  

Clever solution! Just so you know: `frexp` is another function popular with the long-double crowd - at least according to the disassembly I was examining.  
  
A complete audit is outside the scope of what I'm doing right now, but I'll send along pull requests for any more of those double-long weeds that I come across. The first batch of proposed changes is in #399.

---

## Comment 18

> Username: evanmiller  
> Created at: 2020-07-10 04:45:40 UTC  
> Url: https://github.com/boostorg/math/issues/398#issuecomment-656479488  

Changing `binomial_coefficient` gets rid of the 80-bit Lanczos, hooray! Here is my altered version... is this what you had in mind?  
  
```C++  
template <>  
inline float binomial_coefficient<float, policies::policy<> >(unsigned n, unsigned k, const policies::policy<>& pol)  
{  
    typedef policies::normalise<  
        policies::policy<>,  
        policies::promote_float<true>,  
        policies::promote_double<false>,  
        policies::discrete_quantile<>,  
        policies::assert_undefined<> >::type forwarding_policy;  
   return policies::checked_narrowing_cast<float, forwarding_policy>(binomial_coefficient<double>(n, k, forwarding_policy()), "boost::math::binomial_coefficient<%1%>(unsigned,unsigned)");  
}  
```  
  
If so, I will add it to my pull request.

---

## Comment 19

> Username: jzmaddock  
> Created at: 2020-07-10 08:19:11 UTC  
> Url: https://github.com/boostorg/math/issues/398#issuecomment-656550697  

> is this what you had in mind?  
  
Yes exactly so!  
  
Once your PR is in I'll try and do a thorough trawl for other issues.

---

## Comment 20

> Username: evanmiller  
> Created at: 2020-07-10 11:21:58 UTC  
> Url: https://github.com/boostorg/math/issues/398#issuecomment-656624646  

Ok! I've committed the `binomial_coefficient` change. Once Travis passes, the PR will be ready for review.  
  
The last function affecting my own code base is `unchecked_factorial`, as mentioned above. I tried rewriting it to be more generic (i.e. using a single type-independent array), but my template-fu was found wanting. So if your trawl could start there, I would appreciate it!

---

## Comment 21

> Username: evanmiller  
> Created at: 2020-07-13 19:41:55 UTC  
> Url: https://github.com/boostorg/math/issues/398#issuecomment-657753869  

With the PR merged, I am now down to two sources of `long double` with this `cyl_bessel_j_zero` function:  
  
* `unchecked_factorial`, as mentioned  
  
* `make_big_value`, as in (lots of these):  
  
```C++  
double const boost::math::tools::make_big_value<double>(long double, char const*, boost::integral_constant<bool, true> const&, boost::integral_constant<bool, false> const&)  
```  
  
I can't yet tell if I can expect identical behavior on 64-bit and 80-bit platforms. For example, `make_big_value` looks like it gets pulled in from `bessel_j0`, e.g.  
  
https://github.com/boostorg/math/blob/d4d02976268eb5ae2a03fa94e0a42545259a4271/include/boost/math/special_functions/detail/bessel_j0.hpp#L67-L75  
  
I *think* that because a `double` is being returned on both platforms the behavior will be identical, but I am not 100% certain.

---

## Comment 22

> Username: evanmiller  
> Created at: 2020-07-13 19:56:35 UTC  
> Url: https://github.com/boostorg/math/issues/398#issuecomment-657759968  

This seems to eliminate the `long double`s in `make_big_value` when compiling with `-mlong-double-64`:  
  
```  
diff --git a/include/boost/math/tools/big_constant.hpp b/include/boost/math/tools/big_constant.hpp  
index aad7ea133..727f44790 100644  
--- a/include/boost/math/tools/big_constant.hpp  
+++ b/include/boost/math/tools/big_constant.hpp  
@@ -33,9 +33,12 @@ struct numeric_traits<__float128>  
    static const int max_exponent = 16384;  
    static const bool is_specialized = true;  
 };  
-#else  
+#elif LDBL_DIG > DBL_DIG  
 typedef long double largest_float;  
 #define BOOST_MATH_LARGEST_FLOAT_C(x) x##L  
+#else  
+typedef double largest_float;  
+#define BOOST_MATH_LARGEST_FLOAT_C(x) x  
 #endif  
  
 template <class T>  
```
