# #307 - bug in tgamma function for float128 [Closed]

> Username: cosurgi  
> Created at: 2020-01-17 13:55:39 UTC  
> Updated at: 2020-01-23 20:12:57 UTC  
> Closed at: 2020-01-23 20:12:57 UTC  
> Url: https://github.com/boostorg/math/issues/307  

Hi,   
  
As suggested in #303 I submit a separate bugreport here:  
  
the tgamma function for float128 produces incorrect results for negative arguments. I had to implement a [following workaround](https://gitlab.com/yade-dev/trunk/blob/highPrecisionReal/lib/high-precision/MathFunctions.hpp#L239) in my code:  
  
```cpp  
static_assert(std::is_same<UnderlyingReal, boost::multiprecision::float128>::value, "Incorrect type, please file a bug report.");  
  
inline Real tgamma(const Real& a)  
{  
	if (a >= 0) {  
		return YADE_REAL_MATH_NAMESPACE::tgamma(static_cast<UnderlyingReal>(a));  
	} else {  
		return abs(YADE_REAL_MATH_NAMESPACE::tgamma(static_cast<UnderlyingReal>(a)))  
		        * ((static_cast<unsigned long long>(floor(abs(a))) % 2 == 0) ? -1 : 1);  
	}  
}  
```

---

## Comment 1

> Username: ckormanyos  
> Created at: 2020-01-18 18:21:54 UTC  
> Url: https://github.com/boostorg/math/issues/307#issuecomment-575925451  

I will investigate this.

---

## Comment 2

> Username: emsr  
> Created at: 2020-01-18 22:54:58 UTC  
> Url: https://github.com/boostorg/math/issues/307#issuecomment-575945757  

On 1/18/20 1:21 PM, Christopher Kormanyos wrote:  
>  
> I will investigate this.  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub   
> <https://github.com/boostorg/math/issues/307?email_source=notifications&email_token=AAOYYX6GS3OXHJVAR3PWGUTQ6NCEHA5CNFSM4KIIUPM2YY3PNVWWK3TUL52HS4DFVREXG43VMVBW63LNMVXHJKTDN5WW2ZLOORPWSZGOEJJ6ZSY#issuecomment-575925451>,   
> or unsubscribe   
> <https://github.com/notifications/unsubscribe-auth/AAOYYX4X3IJWYT4RIQMOLHDQ6NCEHANCNFSM4KIIUPMQ>.  
>  
I remember something very much like this in gcc in libquadmath.  
  
They just pulled the latest from glibc for the whole lib.  
  
IIRC there was a sign error in the gamma reflection code around line 200.  
  
Good luck.  
  
Ed

---

## Comment 3

> Username: ckormanyos  
> Created at: 2020-01-19 13:22:34 UTC  
> Updated at: 2020-01-19 13:25:32 UTC  
> Url: https://github.com/boostorg/math/issues/307#issuecomment-576004157  

>> ...the tgamma function for float128 produces incorrect results for negative arguments.   
  
> I will investigate this.  
  
Yes. I can reproduce a suspicious (probably erroneous) sign on the result confirming the bug report. With the code below, it appears as though the result of tgamma(-3/2) has the wrong sign, in confirmation of the user's workaround.  
  
Test code:  
  
```  
#include <iostream>  
#include <iomanip>  
  
#include <boost/math/special_functions/gamma.hpp>  
#include <boost/multiprecision/float128.hpp>  
  
boost::multiprecision::float128 x3;  
  
int main()  
{  
  x3 = 3;  
  
  const boost::multiprecision::float128 tgamma__plus_three_half = tgamma(+x3 / 2);  
  const boost::multiprecision::float128 tgamma_minus_three_half = tgamma(-x3 / 2);  
  
  std::cout << "tgamma__plus_three_half: "  
            << std::setprecision(std::numeric_limits<boost::multiprecision::float128>::digits10)  
            << tgamma__plus_three_half  
            << std::endl;  
  
  std::cout << "tgamma_minus_three_half: "  
            << std::setprecision(std::numeric_limits<boost::multiprecision::float128>::digits10)  
            << tgamma_minus_three_half  
            << std::endl;  
}  
```  
  
Compiler:  
  
```  
Using built-in specs.  
COLLECT_GCC=g++  
COLLECT_LTO_WRAPPER=d:/mingw/bin/../libexec/gcc/x86_64-w64-mingw32/8.2.0/lto-wrapper.exe  
Target: x86_64-w64-mingw32  
Configured with: ../src/configure --enable-languages=c,c++ --build=x86_64-w64-mingw32 --host=x86_64-w64-mingw32 --target=x86_64-w64-mingw32 --disable-multilib --prefix=/c/temp/gcc/dest --with-sysroot=/c/temp/gcc/dest --disable-libstdcxx-pch --disable-libstdcxx-verbose --disable-nls --disable-shared --disable-win32-registry --with-tune=haswell --enable-threads=posix --enable-libgomp  
Thread model: posix  
gcc version 8.2.0 (GCC)  
```  
  
Compiler with:  
```  
g++ -O3 test.cpp -IC:/boost/boost_1_72_0 -lquadmath -o test.exe  
```  
  
Result is:  
```  
tgamma__plus_three_half: 0.886226925452758013649083741670573  
tgamma_minus_three_half: -2.36327180120735470306422331112153  
```  
  
Expected answers are:  
```  
N[Gamma[3/2], 34]  
0.8862269254527580136490837416705726  
  
N[Gamma[-3/2], 34]  
2.363271801207354703064223311121527  
```  
  
The fix is unclear. We could potentially fix in `float128.hpp` itself.  
I wonder if there is a range of compilers for which this occurs.

---

## Comment 4

> Username: ckormanyos  
> Created at: 2020-01-19 13:26:08 UTC  
> Url: https://github.com/boostorg/math/issues/307#issuecomment-576004502  

> I remember something very much like this in gcc in libquadmath.  
  
Me too.  
  
I wonder if there is a range of compilers for which this occurs?

---

## Comment 5

> Username: ckormanyos  
> Created at: 2020-01-19 14:57:12 UTC  
> Updated at: 2020-01-19 15:15:21 UTC  
> Url: https://github.com/boostorg/math/issues/307#issuecomment-576012830  

It appears as though tgammaq in at least some versions of libquadmath is neglecting the oscillatory part of Gamma on the negative real axis. Thisw is easy to do if you just take expq(lgammaq) for the result but forget to oscillate.  
  
```   
#include <iostream>  
#include <iomanip>  
  
#include <boost/math/special_functions/gamma.hpp>  
#include <boost/multiprecision/float128.hpp>  
  
boost::multiprecision::float128 one_third = 1 / boost::multiprecision::float128(3);  
  
int main()  
{  
  for(unsigned i = 0U; i < 11U; ++i)  
  {  
    const boost::multiprecision::float128 tgamma_minus_n_and_third = tgamma(-(one_third + i));  
  
    std::cout << "i: "  
              << i  
              << ", tgamma_minus_n_and_third: "  
              << std::setprecision(std::numeric_limits<boost::multiprecision::float128>::digits10)  
              << tgamma_minus_n_and_third  
              << std::endl;  
  }  
}  
```  
  
The output is  
  
```   
i: 0, tgamma_minus_n_and_third: -4.06235381827920125083586408446354  
i: 1, tgamma_minus_n_and_third: -3.04676536370940093812689806334766  
i: 2, tgamma_minus_n_and_third: -1.30575658444688611634009917000614  
i: 3, tgamma_minus_n_and_third: -0.391726975334065834902029751001841  
i: 4, tgamma_minus_n_and_third: -0.090398532769399808054314557923502  
i: 5, tgamma_minus_n_and_third: -0.0169497248942624640101839796106566  
i: 6, tgamma_minus_n_and_third: -0.00267627235172565221213431257010368  
i: 7, tgamma_minus_n_and_third: -0.000364946229780770756200133532286865  
i: 8, tgamma_minus_n_and_third: -4.37935475736924907440160238744236e-05  
i: 9, tgamma_minus_n_and_third: -4.69216581146705257971600255797396e-06  
i: 10, tgamma_minus_n_and_third: -4.54080562400037346424129279803932e-07  
```   
  
Compare at [WolframAlpha](http://www.wolframalpha.com) with  
  
```  
Table[N[Re[Gamma[-(i + (1/3))]], 34], {i, 0, 10, 1}]  
```

---

## Comment 6

> Username: NAThompson  
> Created at: 2020-01-19 15:42:42 UTC  
> Url: https://github.com/boostorg/math/issues/307#issuecomment-576017457  

So should this bug be forwarded to libquadmath?

---

## Comment 7

> Username: ckormanyos  
> Created at: 2020-01-19 16:50:42 UTC  
> Url: https://github.com/boostorg/math/issues/307#issuecomment-576023613  

> So should this bug be forwarded to libquadmath?  
  
Yes. I will also work out a proposal in Boost in a branch for a quicker fix until quadmath can be fixed. Then we can decide what we want to do.

---

## Comment 8

> Username: jzmaddock  
> Created at: 2020-01-19 17:57:55 UTC  
> Url: https://github.com/boostorg/math/issues/307#issuecomment-576029282  

This appears to have been fixed at some point, using @ckormanyos  code as above and gcc-9 I see:  
  
```  
g++ -Iboost t.cpp -lquadmath && ./a.out  
i: 0, tgamma_minus_n_and_third: -4.06235381827920125083586408446354  
i: 1, tgamma_minus_n_and_third: 3.04676536370940093812689806334766  
i: 2, tgamma_minus_n_and_third: -1.30575658444688611634009917000614  
i: 3, tgamma_minus_n_and_third: 0.391726975334065834902029751001841  
i: 4, tgamma_minus_n_and_third: -0.090398532769399808054314557923502  
i: 5, tgamma_minus_n_and_third: 0.0169497248942624640101839796106566  
i: 6, tgamma_minus_n_and_third: -0.00267627235172565221213431257010368  
i: 7, tgamma_minus_n_and_third: 0.000364946229780770756200133532286865  
i: 8, tgamma_minus_n_and_third: -4.37935475736924907440160238744237e-05  
i: 9, tgamma_minus_n_and_third: 4.69216581146705257971600255797396e-06  
i: 10, tgamma_minus_n_and_third: -4.54080562400037346424129279803932e-07  
```  
I see the same output with gcc 7 and 8 on Ubuntu-19.10 even if I use `-Bstatic -lquadmath`.  
  
One option for gcc prior to 9 might be to take the absolute value of `tgammaq` and then apply the sign correction.  Otherwise we risk correcting something that may or may not need fixing.

---

## Comment 9

> Username: ckormanyos  
> Created at: 2020-01-20 05:53:50 UTC  
> Url: https://github.com/boostorg/math/issues/307#issuecomment-576118191  

> One option for gcc prior to 9 might be to take the absolute value of `tgammaq` and then apply the sign correction.  
  
I was thinking to apply that sign correction regardless of GCC version since I was not sure if it was possible or not to say that all GCC for all targets (PC, embedded, ARM, MIPS, etc.) match the fixed libquadmath with GCC 9.  
  
What do you think of [this](http://github.com/boostorg/multiprecision/blob/i307/include/boost/multiprecision/float128.hpp#L649)?

---

## Comment 10

> Username: jzmaddock  
> Created at: 2020-01-20 09:22:22 UTC  
> Url: https://github.com/boostorg/math/issues/307#issuecomment-576182586  

>What do you think of this?  
  
Looks fine to me, can you add a simple test case to test_sf_import_c99.cpp as well?  There's a couple of quick sanity checks in there already, we just need a couple of negative test values added.  Thanks!

---

## Comment 11

> Username: ckormanyos  
> Created at: 2020-01-20 19:20:23 UTC  
> Url: https://github.com/boostorg/math/issues/307#issuecomment-576401568  

>> What do you think of this?  
  
> Looks fine to me, can you add a simple test case to test_sf_import_c99.cpp as well? There's a couple of quick sanity checks in there already, we just need a couple of negative test values added.  
  
The tests are running on the PR of branch i307. I added some relevant tests for tgamma for both positive and negative real values in the neighborhood of the origin.  
  
Let's make sure the tests cycle...  
  
Thanks folks for helping on this issue.  
  
Kind regards, Chris

---

## Comment 12

> Username: ckormanyos  
> Created at: 2020-01-20 19:23:27 UTC  
> Url: https://github.com/boostorg/math/issues/307#issuecomment-576402343  

Oh... Another remark.  
The PR and relevant tests are in Multiprecision, because that seems to be where they belong.

---

## Comment 13

> Username: cosurgi  
> Created at: 2020-01-20 21:35:42 UTC  
> Url: https://github.com/boostorg/math/issues/307#issuecomment-576437091  

Thanks a lot for fixing this :)

---

## Comment 14

> Username: ckormanyos  
> Created at: 2020-01-23 20:12:29 UTC  
> Url: https://github.com/boostorg/math/issues/307#issuecomment-577855424  

Fixed in Multiprecision PR #185.

---

## Comment 15

> Username: ckormanyos  
> Created at: 2020-01-23 20:12:57 UTC  
> Url: https://github.com/boostorg/math/issues/307#issuecomment-577855640  

Closed issue. Thanks folks.
