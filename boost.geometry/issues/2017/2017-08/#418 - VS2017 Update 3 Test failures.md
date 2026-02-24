# #418 - VS2017 Update 3 Test failures [Open]

> Username: garyfurnish  
> Created at: 2017-08-23 05:19:36 UTC  
> Updated at: 2017-10-05 12:54:54 UTC  
> Url: https://github.com/boostorg/geometry/issues/418  

Running `bjam address-model=64 in libs/geometry/test`  
```  
$ grep -A5 -B10 failures out.txt  
common.mkdir ..\..\..\bin.v2\libs\geometry\test\formulas\formulas_vertex_longitude.test\msvc-14.1\debug\address-model-64\asynch-exceptions-on  
common.mkdir ..\..\..\bin.v2\libs\geometry\test\formulas\formulas_vertex_longitude.test\msvc-14.1\debug\address-model-64\asynch-exceptions-on\threading-multi  
msvc.manifest ..\..\..\bin.v2\libs\geometry\test\formulas\formulas_inverse.test\msvc-14.1\debug\address-model-64\asynch-exceptions-on\threading-multi\formulas_inverse.exe  
msvc.link ..\..\..\bin.v2\libs\geometry\test\formulas\formulas_direct.test\msvc-14.1\debug\address-model-64\asynch-exceptions-on\threading-multi\formulas_direct.exe  
testing.capture-output ..\..\..\bin.v2\libs\geometry\test\formulas\formulas_inverse.test\msvc-14.1\debug\address-model-64\asynch-exceptions-on\threading-multi\formulas_inverse.run  
====== BEGIN OUTPUT ======  
Running 1 test case...  
d:/dev/boost/libs/geometry/test/formulas/test_formula.hpp(52): error: in "test_main_caller(_argc,_argv_)": difference{0.0122974} between result{-88.906681419909546} and expected{-90} exceeds 0.1%  
d:/dev/boost/libs/geometry/test/formulas/test_formula.hpp(69): error: in "test_main_caller(_argc,_argv_)": result {-88.906681419909546094} and reference {-90} not close enough.  
  
*** 2 failures are detected in the test module "Test Program"  
  
EXIT STATUS: 201  
====== END OUTPUT ======  
  
  
--  
msvc.manifest ..\..\..\bin.v2\libs\geometry\test\formulas\formulas_vertex_longitude.test\msvc-14.1\debug\address-model-64\asynch-exceptions-on\threading-multi\formulas_vertex_longitude.exe  
compile-c-c++ ..\..\..\bin.v2\libs\geometry\test\algorithms\algorithms_centroid.test\msvc-14.1\debug\address-model-64\asynch-exceptions-on\threading-multi\centroid.obj  
centroid.cpp  
Unknown compiler version - please run the configure tests and report the results  
testing.capture-output ..\..\..\bin.v2\libs\geometry\test\formulas\formulas_vertex_longitude.test\msvc-14.1\debug\address-model-64\asynch-exceptions-on\threading-multi\formulas_vertex_longitude.run  
====== BEGIN OUTPUT ======  
Running 1 test case...  
d:/dev/boost/libs/geometry/test/formulas/test_formula.hpp(52): error: in "test_main_caller(_argc,_argv_)": difference{0.0247883} between result{0.77019371459144814} and expected{0.78928549265438153} exceeds 0.1%  
d:/dev/boost/libs/geometry/test/formulas/test_formula.hpp(69): error: in "test_main_caller(_argc,_argv_)": result {0.77019371459144814196} and reference {0.78539816339715939897} not close enough.  
  
*** 2 failures are detected in the test module "Test Program"  
  
EXIT STATUS: 201  
====== END OUTPUT ======  
```

---

## Comment 1

> Username: garyfurnish  
> Created at: 2017-08-27 18:56:45 UTC  
> Url: https://github.com/boostorg/geometry/issues/418#issuecomment-325217263  

Also present in 2017.4 preview.

---

## Comment 2

> Username: feverzsj  
> Created at: 2017-09-29 06:31:23 UTC  
> Url: https://github.com/boostorg/geometry/issues/418#issuecomment-333042576  

seems m$ broken their compiler again:  
  
test.cpp  
1>e:\vcpkg\installed\x64-windows\include\boost\geometry\algorithms\is_convex.hpp(165): fatal error C1001: An internal error has occurred in the compiler.  
1>(compiler file 'f:\dd\vctools\compiler\cxxfe\sl\p1\cxx\rdscope.cpp', line 867)  
1> To work around this problem, try simplifying or changing the program near the locations listed above.

---

## Comment 3

> Username: mloskot  
> Created at: 2017-09-29 07:24:43 UTC  
> Url: https://github.com/boostorg/geometry/issues/418#issuecomment-333051846  

If there is a serious suspicion there is a problem with Visual C++ in latest (or preview) VS2017, I'd suggest to mention that to Stephan T. Lavavey (STL). Ideally, by posting to [Boost Developers](https://lists.boost.org/mailman/listinfo.cgi/boost)'s mailing list or even perhaps via GH mention @StephanTLavavej  
  
AFAIK, Stephan monitors the Boost ml and asked for reports about Boost build breakage as his team wants to keep Visual C++ with Boost testing up to date and sound.

---

## Comment 4

> Username: StephanTLavavej  
> Created at: 2017-09-29 10:42:10 UTC  
> Url: https://github.com/boostorg/geometry/issues/418#issuecomment-333093951  

ICEs are always compiler bugs, by definition. If you can prepare a repro, I can file it in our internal database as a special favor to Boost developers. (You can also use Report A Problem in the IDE.)  
  
To investigate this ICE, we'll need a preprocessed repro, and the corresponding command line required to compile it. You can prepare this in 3 steps: `cl OPTIONS meow.cpp` to trigger the ICE, `cl OPTIONS /P meow.cpp` to preprocess, and finally `cl OPTIONS /TP meow.i` to verify that the preprocessed repro also ICEs. I need `meow.i` and the final command line.  
  
I have more detailed instructions if that summary is insufficient, although I am on vacation at the moment and won't be able to reply immediately.

---

## Comment 5

> Username: mloskot  
> Created at: 2017-09-29 18:24:43 UTC  
> Url: https://github.com/boostorg/geometry/issues/418#issuecomment-333201976  

@garyfurnish   
1. Which 15.3 update are you using exactly? There have been a few with current 15.3.5. Same for  2017.4 Preview, current full version is "2017.4 Preview 3".  
2. What Boost.Geometry are you using? Boost 1.65.0 from August release or the master branch?  
  
@feverzsj Same questions, please.

---

## Comment 6

> Username: garyfurnish  
> Created at: 2017-09-29 22:18:34 UTC  
> Updated at: 2017-09-29 22:24:45 UTC  
> Url: https://github.com/boostorg/geometry/issues/418#issuecomment-333252277  

Right now this is a non-issue on current-master, With 15.4.0 Preview 3 on head as of 4:12 9/29/2017 on commit 7afe64fccf1520377e53b1caba2295d7544ca9e1 I get the following errors that prevents further testing.  
https://pastebin.com/j5WTP4sD  
  
Appears to be something with MPL in geometry only?  I'll rerun the tests after it stops spamming compile failures on latest preview.  
  
On Edit to clarify: Boost head is Commit d104e7901d67c6a46c04e3fff3af56b7b5e059d2

---

## Comment 7

> Username: garyfurnish  
> Created at: 2017-09-29 22:21:59 UTC  
> Url: https://github.com/boostorg/geometry/issues/418#issuecomment-333252804  

To clarify it is a non issue because of compile-failures.

---

## Comment 8

> Username: StephanTLavavej  
> Created at: 2017-09-29 23:37:45 UTC  
> Url: https://github.com/boostorg/geometry/issues/418#issuecomment-333262751  

There may be a compiler regression with whatever BOOST_MPL_ASSERT_MSG is expanding to (it should just be a static_assert in this day and age; if it's some bizarre cast, our new parsing machinery may be unable to handle it). As with the ICE, a self-contained repro is necessary to investigate.

---

## Comment 9

> Username: garyfurnish  
> Created at: 2017-09-30 00:46:51 UTC  
> Url: https://github.com/boostorg/geometry/issues/418#issuecomment-333269521  

It is somehow related to using (types<Geometry1,Geometry2>) in the 3rd parameter to BOOST_MPL_ASSERT_MSG.  If you change that to say (int) or (::std::vector<int>)  there is no error.  If you use (types<int>) there is an error.  This looks related to types in mpl/assert.hpp but I have no idea what is going on in there.  Does anyone who knows more about MPL want to drop in?

---

## Comment 10

> Username: garyfurnish  
> Created at: 2017-09-30 01:34:32 UTC  
> Updated at: 2017-09-30 01:36:10 UTC  
> Url: https://github.com/boostorg/geometry/issues/418#issuecomment-333272919  

The following test case  
```#include <boost/mpl/assert.hpp>  
int main()  
{  
  using Geometry1=int;  
  using Geometry2=float;  
  BOOST_MPL_ASSERT_MSG((false),ASSIGN_IS_NOT_SUPPORTED_FOR_DIFFERENT_POINT_ORDER, ( types<Geometry1,Geometry2> ));  
  return 0;  
}  
```   
gives the following error  
```  
test.cpp(7): error C2664: 'int boost::mpl::assertion_failed<false>(boost::mpl::assert<false>::type)': cannot convert argument 1 from 'boost::mpl::failed ************(__cdecl main::ASSIGN_IS_NOT_SUPPORTED_FOR_DIFFERENT_POINT_ORDER::* ***********)(boost::mpl::assert_::types<Geometry1,Geometry2,boost::mpl::na,boost::mpl::na>)' to 'boost::mpl::assert<false>::type'  
test.cpp(7): note: No constructor could take the source type, or constructor overload resolution was ambiguous  
test.cpp(7): error C2789: 'mpl_assertion_in_line_0': an object of const-qualified type must be initialized  
test.cpp(7): note: see declaration of 'mpl_assertion_in_line_0'  
```   
  
However this minimal repro testcase:  
```  
#include <boost/mpl/assert.hpp>  
template<typename A, typename B>  
struct test_struct  
{  
  static inline void do_stuff()  
  {  
    BOOST_MPL_ASSERT_MSG((false),ASSIGN_IS_NOT_SUPPORTED_FOR_DIFFERENT_POINT_ORDER, ( types<A,B> ));  
  }  
}  
int main()  
{  
  test_struct<int,float>> ts;  
  return 0;  
}  
```  
produces   
```  
test.cpp(10): error C2143: syntax error: missing ';' before 'int'  
test.cpp(12): error C2059: syntax error: '>'  
```  
like in geometry.  
So the key is that using BOOST_MPL_ASSERT_MSG in a function declared in a templated struct results in BOOST_MPL_ASSERT_MSG not working correctly and thus this looks like a VS regression bug.  @StephanTLavavej I will work on a better writeup later.  Is it acceptable if the repro relies on boost headers?

---

## Comment 11

> Username: feverzsj  
> Created at: 2017-09-30 01:43:39 UTC  
> Url: https://github.com/boostorg/geometry/issues/418#issuecomment-333273482  

@StephanTLavavej  the ICE is triggered under /permissive-. Just include <boost/geometry.hpp> in an empty cpp file to see it.

---

## Comment 12

> Username: garyfurnish  
> Created at: 2017-09-30 05:41:56 UTC  
> Url: https://github.com/boostorg/geometry/issues/418#issuecomment-333285732  

@feverzsj which version is this on?  I did not try running the tests under permissive-, maybe this is a different bug?

---

## Comment 13

> Username: feverzsj  
> Created at: 2017-09-30 08:33:35 UTC  
> Url: https://github.com/boostorg/geometry/issues/418#issuecomment-333293656  

@garyfurnish boost 1.65 on vs2017.3, also tried on VisualCppTools.Community.Daily.VS2017Layout 14.11.25719-Pre.  
  
Yes, it should be different bug.

---

## Comment 14

> Username: mloskot  
> Created at: 2017-09-30 17:25:30 UTC  
> Url: https://github.com/boostorg/geometry/issues/418#issuecomment-333323241  

@garyfurnish Thank you very much for the updates, much appreciated  
  
@feverzsj  
> Yes, it should be different bug.  
  
Then, I'd suggest to report it as such and not making this issue confusing.  
  
Thanks.

---

## Comment 15

> Username: StephanTLavavej  
> Created at: 2017-10-04 23:37:51 UTC  
> Url: https://github.com/boostorg/geometry/issues/418#issuecomment-334319566  

@feverzsj, I cannot reproduce your ICE with our current development build (x86chk). I used Boost 1.65.1.  
  
```  
C:\Temp>type hiss.cpp  
#include <boost/geometry.hpp>  
  
C:\Temp>cl  
Microsoft (R) C/C++ Optimizing Compiler Version 19.12.25715 for x86  
Copyright (C) Microsoft Corporation.  All rights reserved.  
  
usage: cl [ option... ] filename... [ /link linkoption... ]  
  
C:\Temp>cl /EHsc /nologo /W4 /I boost_1_65_1 /c hiss.cpp  
hiss.cpp  
Unknown compiler version - please run the configure tests and report the results  
boost_1_65_1\boost/geometry/algorithms/detail/overlay/aggregate_operations.hpp(48): warning C4245: 'initializing': conversion from 'int' to '::size_t', signed/unsigned mismatch  
  
C:\Temp>cl /EHsc /nologo /W4 /I boost_1_65_1 /c /permissive- hiss.cpp  
hiss.cpp  
Unknown compiler version - please run the configure tests and report the results  
boost_1_65_1\boost/geometry/algorithms/detail/overlay/aggregate_operations.hpp(48): warning C4245: 'initializing': conversion from 'int' to '::size_t', signed/unsigned mismatch  
  
C:\Temp>  
```  
  
@garyfurnish, I can reproduce your bogus syntax error. But it's not a compiler bug - your `struct test_struct` ends with `}` instead of `};`. Additionally, `test_struct<int,float>> ts;` contains an extra `>`. When I fix these problems, I get successful compilation.  
  
If you still believe that there's a compiler bug here, please provide a corrected repro (Boost headers are okay, since I have them extracted).

---

## Comment 16

> Username: mloskot  
> Created at: 2017-10-05 07:13:39 UTC  
> Url: https://github.com/boostorg/geometry/issues/418#issuecomment-334379067  

@StephanTLavavej Thank you for looking at these issues.

---

## Comment 17

> Username: awulkiew  
> Created at: 2017-10-05 12:54:54 UTC  
> Url: https://github.com/boostorg/geometry/issues/418#issuecomment-334455917  

@garyfurnish Thanks for the report. I can also see it on Boost.Geometry regression tests page (http://www.boost.org/development/tests/develop/developer/geometry.html). The problem is located in `thomas_inverse` formula and the cause is lack of numerical robustness (see comment here: https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/formulas/thomas_inverse.hpp#L170). In this case when difference of longitudes of two points is close to `pi/2` the result is not accurate. It seems that in x64 mode some intermediate double values are slightly different than in x86 mode with this compiler and these differences are propagated and amplified in the final result.  
  
@feverzsj @StephanTLavavej Regarding other issues I have nothing to say about the internal compiler error but an issue with `BOOST_MPL_ASSERT_MSG` in `/permissive-` mode was reported before (https://github.com/boostorg/geometry/issues/415) and it seems it is fixed (https://developercommunity.visualstudio.com/content/problem/95255/cannot-compile-valid-c-file-with-permissive.html). However AFAIU this is not the same issue you're talking about.
