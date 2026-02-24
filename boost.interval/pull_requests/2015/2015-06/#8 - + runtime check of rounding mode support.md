# #8 + runtime check of rounding mode support [Open]

> Username: AndreyG  
> Created at: 2015-06-12 14:49:27 UTC  
> Updated at: 2021-06-09 06:42:38 UTC  
> Url: https://github.com/boostorg/interval/pull/8  

For example, following program  
  
```  
#include <boost/numeric/interval.hpp>  
  
int main()  
{  
    boost::numeric::interval<double>::traits_type::rounding _;  
}  
```  
  
will work fine if is built with gcc using '-frounding-math', but will fail otherwise.  
Running on Valgrind is another practical case when this program will crash (even if it was correctly built).  
  
I am not sure about using `assert` for check, may be exception or some other mechanism should be used.

---

## Comment 1

> Username: mclow  
> Created_at: 2015-06-12 21:12:38 UTC  
> Url: https://github.com/boostorg/interval/pull/8#issuecomment-111620396  

Ok, I'm confused. I tried your program using gcc 5.1 on Mac OS X, and it did nothing with both `-frounding-math` on the command line and without.  
  
What is the behavior that you expect from this program (and what is the behavior that you observe)?

---

## Comment 2

> Username: AndreyG  
> Created_at: 2015-06-13 07:34:56 UTC  
> Url: https://github.com/boostorg/interval/pull/8#issuecomment-111684484  

I expect (and I observe with gcc 5.1.0, clang 3.6.0 on ArchLinux) that after applying my patch for Boost.Interval program built without -frounding-math aborted with error  
  
```  
... /boost/numeric/interval/hw_rounding.hpp:68: boost::numeric::interval_lib::detail::runtime_checker<T>::helper::helper() [with T = double]: Assertion `a != b' failed.  
```  
  
Possibly, absence of crash on MAC OS X can be explained by another set of predefined command line options for gcc. Could you try to pass `-fno-rounding-math` explicitly?  
Thanks.

---

## Comment 3

> Username: mcquay239  
> Created_at: 2016-08-22 10:45:24 UTC  
> Url: https://github.com/boostorg/interval/pull/8#issuecomment-241377031  

:+1:

---

## Comment 4

> Username: jeking3  
> Created_at: 2018-05-17 17:43:26 UTC  
> Url: https://github.com/boostorg/interval/pull/8#issuecomment-389950491  

Couldn't this be a compile-time check?

---

## Comment 5

> Username: AndreyG  
> Created_at: 2018-05-17 17:49:02 UTC  
> Url: https://github.com/boostorg/interval/pull/8#issuecomment-389952342  

I'm afraid no. Firstly constexpr evaluation doesn't support rounding mode. Secondly correctly built program will crash if run it on Valgrind.

---

## Comment 6

> Username: jeking3  
> Created_at: 2018-05-17 19:26:23 UTC  
> Url: https://github.com/boostorg/interval/pull/8#issuecomment-389980892  

Using the docker ubuntu bionic image (clang 6, gcc 7.3, valgrind 3.13, found in https://github.com/boostorg/boost/pull/184) I am able to reproduce this issue on the "pi" test in release mode:  
  
gcc with -fno-rounding-math:  
```  
boost@10c3dbfa93be:/boost/libs/numeric/interval/test$ ../../../../b2 toolset=gcc variant=release cxxflags=-fno-rounding-math pi                     /boost/libs/predef/check/../tools/check/predef.jam:46: Unescaped special character in argument $(language)::$(expression)  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached)  
    - default architecture     : x86 (cached)  
    - symlinks supported       : yes (cached)  
...patience...  
...found 2012 targets...  
...updating 2 targets...  
testing.capture-output ../../../../bin.v2/libs/numeric/interval/test/pi.test/gcc-7.3/release/pi.run  
====== BEGIN OUTPUT ======  
/boost/libs/numeric/interval/test/pi.cpp(37): test subset(pi_f, widen(I_f((float) PI), (std::numeric_limits<float> ::min)())) failed in function: 'int test_main(int, char**)'  
/boost/libs/numeric/interval/test/pi.cpp(38): test subset(pi_d, widen(I_d((double)PI), (std::numeric_limits<double>::min)())) failed in function: 'int test_main(int, char**)'  
  
**** 2 errors detected  
  
EXIT STATUS: 201  
```  
  
gcc with -frounding-math:  
```  
boost@10c3dbfa93be:/boost/libs/numeric/interval/test$ ../../../../b2 toolset=gcc variant=release cxxflags=-frounding-math pi  
/boost/libs/predef/check/../tools/check/predef.jam:46: Unescaped special character in argument $(language)::$(expression)  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached)  
    - default architecture     : x86 (cached)  
    - symlinks supported       : yes (cached)  
...patience...  
...found 2012 targets...  
...updating 4 targets...  
gcc.compile.c++ ../../../../bin.v2/libs/numeric/interval/test/pi.test/gcc-7.3/release/libs/numeric/interval/test/pi.o  
gcc.link ../../../../bin.v2/libs/numeric/interval/test/pi.test/gcc-7.3/release/pi  
testing.capture-output ../../../../bin.v2/libs/numeric/interval/test/pi.test/gcc-7.3/release/pi.run  
**passed** ../../../../bin.v2/libs/numeric/interval/test/pi.test/gcc-7.3/release/pi.test  
...updated 4 targets...  
```  
  
clang 6:  
```  
boost@10c3dbfa93be:/boost/libs/numeric/interval/test$ ../../../../b2 toolset=clang variant=release pi  
/boost/libs/predef/check/../tools/check/predef.jam:46: Unescaped special character in argument $(language)::$(expression)  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached)  
    - default architecture     : x86 (cached)  
    - symlinks supported       : yes (cached)  
...patience...  
...found 2012 targets...  
...updating 9 targets...  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/numeric/interval/test/pi.test/clang-linux-6.0/release/libs/numeric/interval/test/pi.o  
clang-linux.link ../../../../bin.v2/libs/numeric/interval/test/pi.test/clang-linux-6.0/release/pi  
testing.capture-output ../../../../bin.v2/libs/numeric/interval/test/pi.test/clang-linux-6.0/release/pi.run  
====== BEGIN OUTPUT ======  
/boost/libs/numeric/interval/test/pi.cpp(37): test subset(pi_f, widen(I_f((float) PI), (std::numeric_limits<float> ::min)())) failed in function: 'int test_main(int, char **)'  
/boost/libs/numeric/interval/test/pi.cpp(38): test subset(pi_d, widen(I_d((double)PI), (std::numeric_limits<double>::min)())) failed in function: 'int test_main(int, char **)'  
  
**** 2 errors detected  
  
EXIT STATUS: 201  
```  
  
Unable to use clang with -fno-rounding-math:  
```  
clang: warning: optimization flag '-fno-rounding-math' is not supported [-Wignored-optimization-argument]  
c  
```  
  
As it stands you can't use clang at all, and you can only use gcc in one mode.  I'm thinking it's the library or the test that's at fault here and not the compiler(s)...

---

## Review 7 [Commented]

> Username: jeking3  
> Created_at: 2018-12-26 16:48:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/interval/pull/8#pullrequestreview-187926400  

📁 include/boost/numeric/interval/hw_rounding.hpp

```diff
  63 |+       helper()
  64 |+       {
  65 |+         save_state<rounded_arith_opp<T> > rounding;
```

> Username: jeking3  
> Created_at: 2018-12-26 16:47:26 UTC  
> Updated_at: 2019-01-05 09:38:21 UTC  
> Url: https://github.com/boostorg/interval/pull/8#discussion_r244020743  

Given state was saved already (see new lines 80, 86, 92) is this necessary?

> Username: AndreyG  
> Created_at: 2019-01-05 09:38:56 UTC  
> Url: https://github.com/boostorg/interval/pull/8#discussion_r245472707  

You are right, it's redundant, I have removed it.

---

📁 include/boost/numeric/interval/hw_rounding.hpp

```diff
  85 | struct rounded_math<double>
  86 |   : save_state<rounded_arith_opp<double> >
  87 |+   , private detail::runtime_checker<double>
```

> Username: jeking3  
> Created_at: 2018-12-26 16:47:57 UTC  
> Updated_at: 2019-01-05 09:38:21 UTC  
> Url: https://github.com/boostorg/interval/pull/8#discussion_r244020809  

Won't this increase the runtime performance significantly if these are used in hot code paths?

> Username: AndreyG  
> Created_at: 2019-01-05 09:46:35 UTC  
> Url: https://github.com/boostorg/interval/pull/8#discussion_r245472851  

It shouldn't because it is invoking only once (`static helper _`, line 59). There is still some performance penalty due to the check that singleton of the type `helper` is initialized, but I believe that it's insignificant in comparison to the cost of switch of rounding mode.


---

## Review 8 [Changes requested]

> Username: jeking3  
> Created_at: 2018-12-26 16:49:50 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/interval/pull/8#pullrequestreview-187926768  

Please rebase on develop.

---
