# #458 - Gamma pdf raises Overflow fpu exception but only on Clang [Closed]

> Username: r-f-a  
> Created at: 2020-12-18 04:17:58 UTC  
> Updated at: 2020-12-18 19:28:28 UTC  
> Closed at: 2020-12-18 17:47:06 UTC  
> Url: https://github.com/boostorg/math/issues/458  

I'm running Boost Math 1.74,  
  
and the following test code raises Overflow and Inexact FPU exceptions  
_but only on Clang_. On the other compilers it raises Inexact only.  
  
If I just go ahead and ignore the Overflow, the actual returned result is correct  
(or at the very least matches what Matlab's `gampdf` function returns too).  
  
Could someone help me understand why ?  
  
# Test code  
  
```cpp  
#include <boost/math/distributions/gamma.hpp>  
#include <iostream>  
#include <cfenv>  
#include <string>  
  
void test() {  
  std::feclearexcept(FE_ALL_EXCEPT);  
  
  auto fpes = std::fetestexcept(FE_ALL_EXCEPT);  
  std::cout << "Before: " << fpes << std::endl;  
  
  double a = 7.525242623272228;  
  double b = 0.790775193798450;  
  auto dist = boost::math::gamma_distribution<double>(a, b);  
  
  fpes = std::fetestexcept(FE_ALL_EXCEPT);  
  std::cout << "After gamma_distribution: " << fpes << std::endl;  
  
  double v = 0.8;  
  auto res = boost::math::pdf<double>(dist, v);  // It fails here  
  
  fpes = std::fetestexcept(FE_ALL_EXCEPT);  
  std::cout << "After pdf: " << fpes << std::endl;  
  
  std::string summary{};  
  summary += ((fpes & FE_INVALID) != 0) ? "Invalid, " : "";  
  summary += ((fpes & FE_DIVBYZERO) != 0) ? "DivByZero, " : "";  
  summary += ((fpes & FE_OVERFLOW) != 0) ? "Overflow, " : "";  
  summary += ((fpes & FE_UNDERFLOW) != 0) ? "Underflow, " : "";  
  summary += ((fpes & FE_INEXACT) != 0) ? "Inexact, " : "";  
  if (!summary.empty()) {  
    summary.pop_back();  
    summary.pop_back();  
  }  
  std::cout << "After pdf (summary): "<< summary << std::endl;  
}  
```  
  
  
# Clang  
  
## Env  
  
-- The CXX compiler identification is Clang 10.0.0  
  
clang version 10.0.0-4ubuntu1  
Target: x86_64-pc-linux-gnu  
Thread model: posix  
  
  
## Output  
  
Before: 0  
After gamma_distribution: 0  
After pdf: 40  
After pdf (summary): Overflow, Inexact  
  
  
# GCC  
  
## Env  
  
-- The CXX compiler identification is GNU 9.3.0  
  
g++ (Ubuntu 9.3.0-17ubuntu1~20.04) 9.3.0  
  
  
## Output  
  
Before: 0  
After gamma_distribution: 0  
After pdf: 32  
After pdf (summary): Inexact  
  
  
# Windows MSVC   
  
## Env  
  
-- Selecting Windows SDK version 10.0.18362.0 to target Windows 10.0.18363.  
-- The CXX compiler identification is MSVC 19.28.29335.0  
  
  
## Output  
  
Before: 0  
After gamma_distribution: 0  
After pdf: 1  
After pdf (summary): Inexact

---

## Comment 1

> Username: ckormanyos  
> Created at: 2020-12-18 05:16:46 UTC  
> Url: https://github.com/boostorg/math/issues/458#issuecomment-747873223  

> Could someone help me understand why ?  
  
Thank you for your report.  
  
This is probably the same issue that has also been reported in #396. It is a bug and it is correct of you to report it. The fix is in progress in #433.  
  
When calculating Bernoulli numbers (needed for `tgamma()` and various other related functions), there can for certain digit counts be a floating-point exception raised in the template function `minimum_argument_for_bernoulli_recursion()` in the header `<boost/math/special_functions/gamma.hpp>`.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2020-12-18 09:04:52 UTC  
> Url: https://github.com/boostorg/math/issues/458#issuecomment-747959282  

>This is probably the same issue that has also been reported in #396. It is a bug and it is correct of you to report it. The fix is in progress in #433.  
  
No this is all double-precision code, #396 applies to multiprecision types only.  
  
In general, I would expect to see differences between compilers and platforms here, but I'll investigate and try and see what's going on.

---

## Comment 3

> Username: ckormanyos  
> Created at: 2020-12-18 09:36:45 UTC  
> Url: https://github.com/boostorg/math/issues/458#issuecomment-747977403  

> No this is all double-precision code, #396 applies to multiprecision types only.  
  
OK. Thanks. If it's in the same area, maybe potential fix(es) might be similar or in similar areas.

---

## Comment 4

> Username: ckormanyos  
> Created at: 2020-12-18 09:39:52 UTC  
> Url: https://github.com/boostorg/math/issues/458#issuecomment-747980854  

I ran the test code provided through LLVM-clang provided by VS2019 but was not able to detect any abnormalities.  
  
![grafik](https://user-images.githubusercontent.com/2404721/102599138-591f1b80-411d-11eb-9289-06a157a01f5d.png)

---

## Comment 5

> Username: r-f-a  
> Created at: 2020-12-18 11:56:02 UTC  
> Url: https://github.com/boostorg/math/issues/458#issuecomment-748046375  

Thanks for trying it out.  
  
The test code was extracted from a larger project  
with dependencies managed by vcpkg  
and it was part of a test suite using Catch2.  
  
Perhaps it could be that Boost Math has been adapted by vcpkg somehow.  
  
Let me do fresh downloads of 1.74 and 1.75 and see if I can isolate the pieces  
before I get back to you.

---

## Comment 6

> Username: ckormanyos  
> Created at: 2020-12-18 12:06:36 UTC  
> Updated at: 2020-12-18 12:11:05 UTC  
> Url: https://github.com/boostorg/math/issues/458#issuecomment-748050757  

> The test code was extracted from a larger project  
  
Here is a slightly modified version of the test code you provided. I'm not sure, but it seems like this test code can elicit `FE_INEXACT`. I get this exception on VC14.2 (VS2019), GCC-MinGW-x86_64 v8.3.1, and LLVM clang  
  
```  
#include <boost/math/distributions/gamma.hpp>  
#include <iostream>  
#include <cfenv>  
#include <string>  
  
struct turn_on_floating_exceptions  
{  
  turn_on_floating_exceptions()  
  {  
    std::fenv_t env;  
  
    std::fegetenv(&env);  
  }  
  
  void show()  
  {  
    std::cout << "exception(s) raised: " << std::endl;  
    if(fetestexcept(FE_DIVBYZERO))     std::cout << "  FE_DIVBYZERO" << std::endl;  
    if(fetestexcept(FE_INEXACT))       std::cout << "  FE_INEXACT"   << std::endl;  
    if(fetestexcept(FE_INVALID))       std::cout << "  FE_INVALID"   << std::endl;  
    if(fetestexcept(FE_OVERFLOW))      std::cout << "  FE_OVERFLOW"  << std::endl;  
    if(fetestexcept(FE_UNDERFLOW))     std::cout << "  FE_UNDERFLOW" << std::endl;  
    if(fetestexcept(FE_ALL_EXCEPT)==0) std::cout << "  none"         << std::endl;  
  }  
};  
  
turn_on_floating_exceptions yes_turn_on_floating_exceptions;  
  
void test()  
{  
  // Activate this to get exceptions.  
  #if 1  
  
  std::feclearexcept(FE_ALL_EXCEPT);  
  
  auto fpes = std::fetestexcept(FE_ALL_EXCEPT);  
  std::cout << "Before: " << fpes << std::endl;  
  
  double a = 7.525242623272228;  
  double b = 0.790775193798450;  
  auto dist = boost::math::gamma_distribution<double>(a, b);  
  
  fpes = std::fetestexcept(FE_ALL_EXCEPT);  
  std::cout << "After gamma_distribution: " << fpes << std::endl;  
  
  double v = 0.8;  
  auto res = boost::math::pdf<double>(dist, v);  // It fails here  
  
  std::cout << "Be sure to use res: " << res << std::endl;  
  
  fpes = std::fetestexcept(FE_ALL_EXCEPT);  
  std::cout << "After pdf: " << fpes << std::endl;  
  
  #else  
  #endif  
  
  yes_turn_on_floating_exceptions.show();  
}  
  
int main()  
{  
  test();  
}  
```

---

## Comment 7

> Username: r-f-a  
> Created at: 2020-12-18 12:36:18 UTC  
> Updated at: 2020-12-18 12:37:01 UTC  
> Url: https://github.com/boostorg/math/issues/458#issuecomment-748062264  

I tested with 1.74 and 1.75 (no vcpkg this time).  
This is what I could noticed so far:  
  
- On Debug: Only Inexact is raise.  
- On Release (or RelWithDebInfo) build types: Inexact and Overflow are raised.  
  
Are you getting only Inexact results with Release builds as well?

---

## Comment 8

> Username: r-f-a  
> Created at: 2020-12-18 13:04:09 UTC  
> Url: https://github.com/boostorg/math/issues/458#issuecomment-748073333  

I set up this repo with code and build scripts  
able to repro and compare the results across {Clang, GCC} x {release, debug}:  
  
https://github.com/r-f-a/boost-math-issue-458-repro

---

## Comment 9

> Username: r-f-a  
> Created at: 2020-12-18 14:12:49 UTC  
> Updated at: 2020-12-18 14:13:35 UTC  
> Url: https://github.com/boostorg/math/issues/458#issuecomment-748104910  

I was able to repro this on CI servers too,  
through Github actions running on Ubuntu 20.04:  
  
Unfortunately they only have boost 1.71 but the result stays the same  
and it helped me confirm it's not something specific to my environment:  
  
https://github.com/r-f-a/boost-math-issue-458-repro/runs/1577109998?check_suite_focus=true

---

## Comment 10

> Username: ckormanyos  
> Created at: 2020-12-18 17:10:59 UTC  
> Url: https://github.com/boostorg/math/issues/458#issuecomment-748210850  

> I set up this repo with code and build scripts  
  
Cool  
  
> I was able to repro this on CI servers too,  
  
Good info. Thx.  
  
> Are you getting only Inexact results with Release builds as well?  
  
At the moment, I am only getting the `FE_INEXACT` exception flag raised, but, in fact, getting that consistetly on 3 different compilers in either debug or release modes.  
  
For GCC, I was wondering is setting `-ffast-math` might help, but it changed nothing and `FE_INEXACT` still was observed.  
  
As a next step, will do some binary searching through the code in an attempt to find the offending area(s). Some of this action might occur during the os-called _static initialization_ that runs before the jump to `main()`, but that is not yet confirmed.

---

## Comment 11

> Username: jzmaddock  
> Created at: 2020-12-18 17:47:05 UTC  
> Url: https://github.com/boostorg/math/issues/458#issuecomment-748227294  

OK, I've tracked this down (I think anyhow), and it's not our problem.  
  
In gamma.hpp:1770 we have this check:  
  
```  
   if((x < 1) && (tools::max_value<T>() * x < f1))  
   {  
      // overflow:  
      return policies::raise_overflow_error<T>("boost::math::gamma_p_derivative<%1%>(%1%, %1%)", 0, pol);  
   }  
```  
  
Up until this line the overflow flag is not set, after the check it is, I tried disassembling but got nothing comprehensible (to be anyhow!!), I think what's happening is the compiler is engaging in a bit of speculative execution and evaluating the `tools::max_value<T>() * x` *before* the `x < 1` check is complete.  
  
So... then I checked the clang docs and found this:  
  
```  
-ffp-exception-behavior=<value>  
  
    Specify the floating-point exception behavior.  
  
    Valid values are: ignore, maytrap, and strict. The default value is ignore. Details:  
  
        ignore The compiler assumes that the exception status flags will not be read and that floating point exceptions will be masked.  
        maytrap The compiler avoids transformations that may raise exceptions that would not have been raised by the original code. Constant folding performed by the compiler is exempt from this option.  
        strict The compiler ensures that all transformations strictly preserve the floating point exception semantics of the original code.  
```  
  
In other words the default behaviour is to generate code which renders the floating point exception flags meaningless, where as compiling with either `-ffp-exception-behavior=maytrap` or ` -ffp-exception-behavior=strict` fixes the issue for me.  
  
So... I'm closing this for now, but please do re-open if I've missed something or misunderstood.

---

## Comment 12

> Username: r-f-a  
> Created at: 2020-12-18 19:10:08 UTC  
> Updated at: 2020-12-18 19:11:19 UTC  
> Url: https://github.com/boostorg/math/issues/458#issuecomment-748268085  

I see...  
  
So even with pre-condition checks like `x < 1` across the code  
a floating point exception is not entirely out of question because of speculative execution.  
That's really good insight.  
  
Indeed, by setting the compiler flag to `-ffp-exception-behavior=maytrap`  
the Overflow problem goes away. There's only the Inexact left but I suppose that's OK.  
  
Logs:  
https://github.com/r-f-a/boost-math-issue-458-repro/runs/1578525450?check_suite_focus=true  
  
Thank you!

---

## Comment 13

> Username: ckormanyos  
> Created at: 2020-12-18 19:28:27 UTC  
> Url: https://github.com/boostorg/math/issues/458#issuecomment-748278852  

> a floating point exception is not entirely out of question because of speculative execution.  
  
A comment from a static-analysis point of view could potentially disapprove of that compiler choice, if it is a _choice_. It's not really clear if that should really be happening --- even with an optimizing compiler chewing through template code.
