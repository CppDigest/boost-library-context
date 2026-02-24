# #327 - Tests done on Intel + AVX&AVX2 + -march=native should fail [Closed]

> Username: trex58  
> Created at: 2020-03-26 16:27:30 UTC  
> Updated at: 2020-03-27 19:04:22 UTC  
> Closed at: 2020-03-27 11:43:37 UTC  
> Url: https://github.com/boostorg/math/issues/327  

Hi,  
I am porting Boost 1.69 to AIX.  
I have issues with -O2 due to the Power **fmadd** assembler instruction being used with -O2 and thus generating NOT PERFECT results.  
See: https://github.com/boostorg/geometry/issues/686  for my issue on AIX, dealing with a test of "geometry".  
I explain in this 686 issue that we see the same kind of issue with Intel **vfmadd132sd** assembler instruction, on a small non-Boost example.  
  
Now, when running a math test on Intel HW with **AVX & AVX2** features, and when using **-march=native** , I see the same issue on Intel that I have on AIX.  
(for now, we have GCC 7.5 only. Will move to GCC 8.4 asap)  
  
With math test:  
  ../bin.v2/libs/math/test/test_lambert_w.test/gcc-7.5.0/debug/link-static/visibility-hidden/**test_lambert_w**.run…  
we have:  
  
**AIX error message:**  
```  
    ../libs/math/test/test_lambert_w.cpp(1034): error:  
         in "test_range_of_double_values":  
         difference{2.61848e-09}  
         between  
            lambert_w0(create_test_value<RealType>( -0.36787944117144228L, "-0.36787944117144228",  
            boost::mpl::bool_< std::numeric_limits<RealType>::is_specialized && (std::numeric_limits<RealType>::radix == 2)  
            && (std::numeric_limits<RealType>::digits <= std::numeric_limits<long double>::digits)  
            && boost::is_convertible<largest_float, RealType>::value>(),  
            boost::mpl::bool_< boost::is_constructible<RealType, const char*>::value>())){-0.99999998154505632}  
         and  
                       create_test_value<RealType>( -0.99999997892657588L, "-0.99999997892657588",  
            boost::mpl::bool_< std::numeric_limits<RealType>::is_specialized && (std::numeric_limits<RealType>::radix == 2)  
            && (std::numeric_limits<RealType>::digits <= std::numeric_limits<long double>::digits)  
            && boost::is_convertible<largest_float, RealType>::value>(),  
            boost::mpl::bool_< boost::is_constructible<RealType, const char*>::value>()) {-0.99999997892657588}  
         exceeds  
                 2.2204460492503131e-16  
```  
  
**Intel error message:**  
```  
Test Lambert W function type double for range of values.  
Tolerance 1 * epsilon == 2.22045e-16  
../libs/math/test/test_lambert_w.cpp(1034): error: in "test_range_of_double_values":  
  difference{2.61848e-09}  
  between  
        lambert_w0(create_test_value <RealType>( -0.36787944117144228Q, "-0.36787944117144228",  
     boost::mpl::bool_< std::numeric_limits<RealType>::is_specialized &&  
     (std::numeric_limits<RealType>::radix == 2) && (std::numeric_limits<RealType>::digits <= 113)  
     && boost::is_convertible<largest_float, RealType>::value>(),  
     boost::mpl::bool_< boost::is_constructible<RealType, const char*>::value>())){-0.99999998154505632}  
   and  
                    create_test_value<RealType>( -0.99999997892657588Q, "-0.99999997892657588",  
     boost::mpl::bool_< std::numeric_limits<RealType>::is_specialized &&  
     (std::numeric_limits<RealType>::radix == 2) && (std::numeric_limits<RealType>::digits <= 113)  
     && boost::is_convertible<largest_float, RealType>::value>(),  
     boost::mpl::bool_< boost::is_constructible<RealType, const char*>::value>()){-0.99999997892657588}  
   exceeds  
              2.2204460492503131e-16  
```  
  
Note how close are the 2 error messages.  
  
On Intel, we compiled with:  
```  
gcc.compile.c++ ../bin.v2/libs/math/test/test_lambert_w.test/gcc-7.5.0/debug/link-static/visibility-hidden/test_lambert_w.o  
  
    "g++"  -march=native -fvisibility-inlines-hidden -m64 -g -fvisibility=hidden -O2 -g -m64 -fmessage-length=0 -D_FORTIFY_SOURCE=2 -fstack-protector -funwind-tables -fasynchronous-unwind-tables -fno-strict-aliasing -Wno-unused-local-typedefs -Wno-deprecated-declarations  -DBOOST_ALL_NO_LIB=1 -DBOOST_CHRONO_STATIC_LINK=1 -DBOOST_CHRONO_THREAD_DISABLED -DBOOST_HAS_ICU=1 -DBOOST_SYSTEM_STATIC_LINK=1 -DBOOST_TEST_NO_AUTO_LINK=1 -DBOOST_TIMER_STATIC_LINK=1 -DBOOST_UBLAS_UNSUPPORTED_COMPILER=0  -I".." -I"../libs/math/include_private" -I"../libs/math/test" -I"/usr/include" -c -o "../bin.v2/libs/math/test/test_lambert_w.test/gcc-7.5.0/debug/link-static/visibility-hidden/test_lambert_w.o" "../libs/math/test/test_lambert_w.cpp"  
  
```  
We are now rebuilding Boost and retesting the "math" module on same Intel HW after adding the **-ffp-contract=off** option, in order to NOT use **fmadd** instruction. And I'll be able to provide the result soon. It should show that the above issue disappears.  
  
As a **conclusion**, I think that it would be worth to use the GCC -march=native option on INTEL hardware having AVX & AVX2 features, in order to see how much tests are broken by -O2 and thus the fmadd instruction.

---

## Comment 1

> Username: NAThompson  
> Created at: 2020-03-26 17:00:03 UTC  
> Url: https://github.com/boostorg/math/issues/327#issuecomment-604548942  

Wait, is it just the tests not passing or is the result quantitatively wrong? In general I expect some small issues when compiling with fma instructions, just from test values being slightly off, but in general I expect an improvement in the result.

---

## Comment 2

> Username: trex58  
> Created at: 2020-03-26 17:02:42 UTC  
> Url: https://github.com/boostorg/math/issues/327#issuecomment-604550480  

Issue confirmed. When compiling with **-ffp-contract=off** (NO fmadd !) in addition to **-march=native** , the issue disappears.  
  
```  
 "g++"  -march=native -ffp-contract=off  -fvisibility-inlines-hidden -m64 -g -fvisibility=hidden -O2 -g -m64 -fmessage-length=0 -D_FORTIFY_SOURCE=2  
 -fstack-protector -funwind-tables -fasynchronous-unwind-tables -fno-strict-aliasing -Wno-unused-local-typedefs -Wno-deprecated-declarations  -DBOOST_ALL_NO_LIB=1  
-DBOOST_CHRONO_STATIC_LINK=1 -DBOOST_CHRONO_THREAD_DISABLED -DBOOST_HAS_ICU=1 -DBOOST_SYSTEM_STATIC_LINK=1 -DBOOST_TEST_NO_AUTO_LINK=1  
 -DBOOST_TIMER_STATIC_LINK=1 -DBOOST_UBLAS_UNSUPPORTED_COMPILER=0  -I".."  
 -I"../libs/math/include_private" -I"../libs/math/test" -I"/usr/include" -c   
-o "../bin.v2/libs/math/test/  
test_lambert_w.test/gcc-7.5.0/debug/link-static/visibility-hidden/test_lambert_w.o"   
"../libs/math/test/test_lambert_w.cpp"  
  
**passed** ../bin.v2/libs/math/test/test_lambert_w.test/gcc-7.5.0/debug/link-static/visibility-hidden/test_lambert_w.test  
  
```

---

## Comment 3

> Username: trex58  
> Created at: 2020-03-26 17:10:57 UTC  
> Url: https://github.com/boostorg/math/issues/327#issuecomment-604554905  

Hi Nick  
I'm not able yet to say if it is only tests not passing or if the result is wrong. What I see is that, in some cases, that breaks the logic and some fix is required (like adding bounded() ). A fix that I added in the test code, about this only one case I've analyzed completely and fixed. I have to do more experiments to know. Would appreciate Boost experts to do same experiments in official environment. Moreover, I'm a Boost beginner so I do not master all of this.  
  
About the first issue I've been able to assign to fmadd, without it I get exactly -1 and with fmadd I get -1.00000000000....22 (1 bit added).  
  
I do not have the skills about Boost required to say if this is an real fundamental issue or if it only deals with tests. However, these issues made my life complicated when porting Boost to AIX, since I saw many NaNQ when compiling with -O2 , but none with -O0, and now none when compiling with  -ffp-contract=off .  
And it's hell to find the exact place where something is refused by the test. And I do not have the skills for knowing if it is acceptable or not.

---

## Comment 4

> Username: NAThompson  
> Created at: 2020-03-26 17:25:24 UTC  
> Url: https://github.com/boostorg/math/issues/327#issuecomment-604563438  

> About the first issue I've been able to assign to fmadd, without it I get exactly -1 and with fmadd I get -1.00000000000....22 (1 bit added).  
  
In general you cannot expect half ULP accuracy; see [here](https://blogs.mathworks.com/cleve/2017/01/23/ulps-plots-reveal-math-function-accurary/), so this is not a problem.  
  
>  However, these issues made my life complicated when porting Boost to AIX, since I saw many NaNQ when compiling with -O2 , but none with -O0, and now none when compiling with -ffp-contract=off .  
  
Can you compile with `-fsanitize=address -fsanitize=undefined` on AIX? But just from your description I suspect a compiler bug or a hardware bug.

---

## Comment 5

> Username: ckormanyos  
> Created at: 2020-03-26 17:41:16 UTC  
> Url: https://github.com/boostorg/math/issues/327#issuecomment-604575016  

Somehow in the original message it looks like the tolerance in this quote:  
> 2.2204460492503131e-16  
  
is for float64, but the test values seem to be built from float32.  
  
Is it possible that the compiler found a way to use float32 when the test case expects float64?  
  
Those mentioned hardware instructions operate on packed 32-bit floating point values, if memory serves.  
  
Just a thought.  
  
Kind regards, Chris

---

## Comment 6

> Username: trex58  
> Created at: 2020-03-27 08:44:06 UTC  
> Url: https://github.com/boostorg/math/issues/327#issuecomment-604883837  

> In general you cannot expect half ULP accuracy; see here, so this is not a problem.  
  
I'm not expecting anything. Just seeing that the test fails since -1,00...02 is not -1 and that acos(x) function returns NaN when x is out of [-1,1]. Adding bounded() did easily fix the issue. But my hard time was to find WHERE is the issue, since I'm not a Boost expert nor a FFP computation expert. Moreover, often the root cause of a test failure is very far from the place where some check fails, which makes finding the root cause so difficult. Moreover, since gdb does not work perfectly when -O2 is used, that's often hell and it takes me days to understand what's happening.  
  
Thanks for the link. I'll read it carefuly.  
  
> Can you compile with -fsanitize=address -fsanitize=undefined on AIX? But just from your description I suspect a compiler bug or a hardware bug.  
  
GCC provides thes options on AIX, I think. However, they may have the full same behavior that they have withing Linux/Intel. I'll try them asap.  
And, yes, we have already found GCC bugs breaking some tests of Boost C++ code. However, since NaN is returned as an error flag from several math routines, like acos(), that may show some other places where bounded() is missing. And that would help a lot to build and test Boost on a Intel + AVX&AVX2 machine and with -O2 -march=native, so that Boost experts could easily understand where the issue comes and fix it much faster than I can, so that I could focus to AIX-specific issues.

---

## Comment 7

> Username: pabristow  
> Created at: 2020-03-27 09:03:21 UTC  
> Url: https://github.com/boostorg/math/issues/327#issuecomment-604891486  

The imprecision of floating-point is indeed a minefield (and sympathy to you in having to wander in it).  
  
The number of variables in this saga is not helpful?  
  
It seems to me unwise to be using old compiler versions.  Moving up to gcc 8 when 10 has been out for months seems unambitious. The theory is that the latest version has least bugs! Getting at least to the penultimate version could deal with some optimisations issue?.  I'd skip to the latest compiler, <cmath>. and Boost for a start.  At least it would cut the number of variables.

---

## Comment 8

> Username: trex58  
> Created at: 2020-03-27 09:04:05 UTC  
> Url: https://github.com/boostorg/math/issues/327#issuecomment-604891764  

> Somehow in the original message it looks like the tolerance in this quote:  
>   
> 2.2204460492503131e-16  
>   
> is for float64, but the test values seem to be built from float32.  
> Is it possible that the compiler found a way to use float32 when the test case expects float64?  
> Those mentioned hardware instructions operate on packed 32-bit floating point values, if memory serves.  
  
By default, Floating Point operations on AIX are made using 64bit registers.  
AIX executables can be built either in 32bit or 64bit, but still with 64bit FP registers. I'm testing both cases.  
However, yes, maybe there are some cases where the compiler/linker mixes 32bit FP values/operations and 64 FP values/operations, generating some mess. I'm analyzing this.  
  
Moreover, we could/should also use 128bit FP registers/operations on AIX. But GCC on AIX is broken now about 128bit FP operations ; trying to fix this these days.  
Using 128bit FP operations on AIX will be required, because I think that many tests of Boost have been calibrated for 128bit FP precision (which is the default on Intel processors) and do not correctly handle 64bit precision. Probably I'll rebuild/retest all Boost on Fedora/Intel with 64bit operation in order to validate this hypothesis.  
  
Last but not the least, it seems that Power hardware is not using the same FP standard that is used by Intel HW, which may lead to inaccurate difference computations/comparisons in some cases.  
  
As a conclusion, building and testing Boost in a different environment than Linux/Intel may help the Boost project to find some deeply hidden rare (but maybe bad) issues in some cases. About Boost buildfarm, I think I remember that it is using only Intel processors. Adding a different HW could help to find hidden issues. Dealing with other OpenSource packages, I've often found that an issue which appears once out of 100 tries with Fedora does appear 99 times out of 100 tries on AIX, for multi-threaded code mainly.  
As another conclusion, building Boost on AIX is of great help for finding hidden bugs in GCC on AIX. Moreover, due to the many reasons Boost tests may fail, and due to the 10,000 tests in Boost, it's a hard job to port Boost to AIX.

---

## Comment 9

> Username: trex58  
> Created at: 2020-03-27 09:12:03 UTC  
> Url: https://github.com/boostorg/math/issues/327#issuecomment-604894856  

> The imprecision of floating-point is indeed a minefield (and sympathy to you in having to wander in it).  
> The number of variables in this saga is not helpful?  
> It seems to me unwise to be using old compiler versions.  Moving up to gcc 8 when 10 has been out for months seems unambitious. The theory is that the latest version has least bugs! Getting at least to the penultimate version could deal with some optimisations issue?.  I'd skip to the latest compiler, . and Boost for a start.  At least it would cut the number of variables.  
  
We have big Intel machines in our lab, but quite old (no AVX nor AVX2) in my team, so we've borrowed a PC Intel machine with AVX* (but with GCC v7) as a quick replacement for testing this. I've asked for being able to use modern Intel machines, with very recent GCC versions. Waiting...  
On Fedora/PPC64LE and Fedora/Intel, we use GCC 9.2.1 .  
On AIX, we use GCC 8.4 and GCC 9.3 . Testing our GCC fixes with GCC master right now. Waiting for GCC 10 for sure.

---

## Comment 10

> Username: pabristow  
> Created at: 2020-03-27 09:22:56 UTC  
> Url: https://github.com/boostorg/math/issues/327#issuecomment-604899487  

Testing on a variety of hardware is certainly most valuable to find real bugs.  Keep up this work!  
  
But downstream programs must just deal with floating-point imprecision  
  
(Aside: don't forget that the tolerances for tests are often chosen quite arbitrarily based on our limited platforms.  We often just increase tolerances slightly to 'solve' failures.  If you think that it is just that one tolerance is too tight, please tell us.  ).

---

## Comment 11

> Username: trex58  
> Created at: 2020-03-27 09:57:42 UTC  
> Updated at: 2020-03-27 09:59:23 UTC  
> Url: https://github.com/boostorg/math/issues/327#issuecomment-604913737  

> Testing on a variety of hardware is certainly most valuable to find real bugs.  Keep up this work!  
  
:-)  
  
> But downstream programs must just deal with floating-point imprecision.  
  
Yes. I have to remind my lessons about Fortran when I was a student in Computer Science, 40 years ago. ;-)  
  
> (Aside: don't forget that the tolerances for tests are often chosen quite arbitrarily based on our limited platforms.  We often just increase tolerances slightly to 'solve' failures.  If you think that it is just that one tolerance is too tight, please tell us.  ).  
  
Sure. I've already found several cases where result on AIX is very very very close to what is expected, due to some different optimizations or FP standard. Maybe due to fmadd too: this is why I suggest Boost community to use -march=native on a Intel + AVX + AVX2 hardware, in order to find and fix this kind of issues before I spend days, weeks (or months...) on this, due to my lack of expertise about Boost internals.  
  
Moreover, looking at Boost buildfarm, I see that the large table provides information about the OS being used, about the kind of C/C++ compiler (GCC / CLang), and sometimes about its version, but none about the HW used, except that some machines include ARM in their name.  
I would suggest to add a line (or 2) with HW details, with C/C++ compiler kind and version, with important GCC options (native code), and with FP version (64 or 128bit FP), in order to check that you cover more cases.  
Probably also some basic tests should be added to the "math" library in order to check some basic things (impact of -O2, impact of fmadd, etc) and warn Boost developers and users. Just ideas...

---

## Comment 12

> Username: NAThompson  
> Created at: 2020-03-27 10:16:04 UTC  
> Url: https://github.com/boostorg/math/issues/327#issuecomment-604921017  

> Maybe due to fmadd too: this is why I suggest Boost community to use -march=native on a Intel + AVX + AVX2 hardware, in order to find and fix this kind of issues before I spend days, weeks (or months...) on this, due to my lack of expertise about Boost internals.  
  
There is no issue though. You're problem is not lack of expertise in Boost internals, it's understanding the weak algebra of floating point arithmetic. (Nothing to be ashamed about; it's a difficult concept!) As to your suggestion of using `-march=native`, well, we use whatever hardware Travis CI/Appveyor gives us. If we used `-march=native`, we'd get slightly different results (but essentially meaningless) differences on every run, and then spend countless hours debugging a non-problem.  
  
It seems that our expectations about what the library should guarantee are different: You seem to think that it should produce deterministic results across platforms. We believe that the results should accurately approximate the infinite precision result, but don't care about determinism.  
  
There are people who are concerned about floating point determinism and reproducibility, but they have encountered extreme difficulties. See [here](https://randomascii.wordpress.com/2013/07/16/floating-point-determinism/).

---

## Comment 13

> Username: trex58  
> Created at: 2020-03-27 11:17:13 UTC  
> Url: https://github.com/boostorg/math/issues/327#issuecomment-604945586  

> There is no issue though.  
  
I used the wrong word. Or I didn't explain that it is only an issue when building/testing Boost in a different environment than the reference one you are mainly using: Intel HW.  
  
As a computer science engineer, with 38 years of professional experience with many complex projects, I do understand that floating point arithmetic is weak algebra, with propagation of errors maybe leading at the end to a big issue if not controlled/managed, with results generated within different environments being a few different, though I do not master FP arithmetic.  
  
The issue deals with the difficulty to know that an error warned by Boost asserts on a new environment used for building Boost is a real issue (GCC bug, …) or only an expected consequence of weak computation. And, when a test program expects to do acos(x) with x being exactly -1 and will fail with x = -1- epsilon, that leads to future issues for sure. And debugging C++ code is not easy.  
  
> As to your suggestion of using -march=native, well, we use whatever hardware Travis CI/Appveyor gives us. If we used -march=native, we'd get slightly different results (but essentially meaningless) differences on every run, and then spend countless hours debugging a non-problem.  
  
Debugging a "non-problem" would take much less time when done by a Boost expert than by a Boost newbye like me who is also fighting with C++ gdb limitations (mainly - but not only - with -O2).  
Meaning that such a "non-problem" is not a problem for computations made by Boost but is a problem for asserting that Boost on AIX (or any other environment) provides correct results or not. When I see a Boost test saying: "failed", I have to analyze it and to find if it really is an issue, or just an expected weak floating point computation. And this analysis takes hours and sometimes days.  
**Boost requires and deserves perfect quality on all platforms.**  
As an example, I've seen many failed tests with Boost on Fedora/PPC64LE. And it is not easy to say if they are a real bugs (with an impact to Boost applications) or if it is due to some weak computation.  
Looking at the port of lapack, atlas, and openblas to AIX and Linux/PPC64LE, I think that we have seen less issues than with Boost. Old codes, mainly Fortran code, have seen so many different environments (HW, OS, Compiler) that they are close to perfect.  
  
> It seems that our expectations about what the library should guarantee are different: You seem to think that it should produce deterministic results across platforms. We believe that the results should accurately approximate the infinite precision result, but don't care about determinism.  
  
I do agree that FP computation is not deterministic.

---

## Comment 14

> Username: NAThompson  
> Created at: 2020-03-27 11:43:37 UTC  
> Url: https://github.com/boostorg/math/issues/327#issuecomment-604956023  

Ok, it looks like your issue is that the tolerances for the Lambert W test are too stringent.  
  
I've opened another [issue](https://github.com/boostorg/math/issues/328) for that.

---

## Comment 15

> Username: jzmaddock  
> Created at: 2020-03-27 11:55:34 UTC  
> Url: https://github.com/boostorg/math/issues/327#issuecomment-604960040  

@trex58 : I feel your pain, and you are of course quite right that testing on multiple architectures  is a "good thing" that shows up very many issues.  Years ago, back when Boost.Math was first started, I had access to HP UX machines with 128-bit FP hardware and would sometimes (only sometimes because everything was manual and remote terminal access to is a right pain to handle) run the tests on there whenever there was a significant change.  It certainly showed up bugs.  Since then, not only has that program ended, but the only hardware any of us have regular access to is Intel x86/64.  More than that, much of it is in virtual machines, so -march=native probably picks up i686 at best!  
  
So... I'm not sure what the solution is, you could set a machine up to run our tests and report back to the test matrix, but that's still likely to leave you as chief debugger on AIX I'm afraid.

---

## Comment 16

> Username: NAThompson  
> Created at: 2020-03-27 12:08:52 UTC  
> Url: https://github.com/boostorg/math/issues/327#issuecomment-604965157  

We can also be a bit more careful about relativizing tolerance to condition number of function evaluation. The problem is that you've got to have confidence in your derivatives before you can test your function, so you have to write all your tests, implement a derivative, then do a pass back through and relativize. Kinda painful . . .

---

## Comment 17

> Username: ckormanyos  
> Created at: 2020-03-27 12:22:22 UTC  
> Updated at: 2020-03-27 12:23:05 UTC  
> Url: https://github.com/boostorg/math/issues/327#issuecomment-604970423  

trex wrote  
>>> The issue deals with the difficulty to know that an error warned by Boost asserts on a new environment used for building Boost is a real issue (GCC bug, …) or only an expected consequence of weak computation.  
  
John wrote  
>> So... I'm not sure what the solution is, you could set a machine up to run our tests and report back to the test matrix, but that's still likely to leave you as chief debugger on AIX I'm afraid.  
  
Nick wrote  
> We can also be a bit more careful about relativizing tolerance to condition number of function evaluation.  
  
This issue has been quite complex and detailed. Initially I had thought it was a data-type mixup. Now it seems more likely that there is a tolerance issue that gets exposed via a new (well, not new, but not well-tested) platform.  
  
It is quite common that diverse platforms reveal subtle differences in floating-point software. This seems to be the unfortunate nature of the art form, even if we are all supposedly adhering to IEEE754 and the like.  
  
Perhaps the best way to deal with this particular issue is to increase tolerance of the relevant test cases.  
  
Kind regards, Chris  
  
P.S. I have left this issue closed and think this will be handled in #328

---

## Comment 18

> Username: trex58  
> Created at: 2020-03-27 13:51:53 UTC  
> Url: https://github.com/boostorg/math/issues/327#issuecomment-605012152  

> Ok, it looks like your issue is that the tolerances for the Lambert W test are too stringent.  
> I've opened another issue for that.  
  
Thanks a lot! :-)

---

## Comment 19

> Username: trex58  
> Created at: 2020-03-27 13:59:16 UTC  
> Url: https://github.com/boostorg/math/issues/327#issuecomment-605015754  

> @trex58 : I feel your pain, and you are of course quite right that testing on multiple architectures  is a "good thing" that shows up very many issues.  Years ago, back when Boost.Math was first started, I had access to HP UX machines with 128-bit FP hardware and would sometimes (only sometimes because everything was manual and remote terminal access to is a right pain to handle) run the tests on there whenever there was a significant change.  It certainly showed up bugs.  Since then, not only has that program ended, but the only hardware any of us have regular access to is Intel x86/64.  More than that, much of it is in virtual machines, so -march=native probably picks up i686 at best!  
> So... I'm not sure what the solution is, you could set a machine up to run our tests and report back to the test matrix, but that's still likely to leave you as chief debugger on AIX I'm afraid.  
  
I'm now asking for a Intel AVX/AVX2 machine, if any is available in other colleagues' lab. Waiting.  
  
About AIX, we are already used, at ATOS BullFreeware, to be "chief debugger on AIX" ;-) !  
As an example, we have a machine for the Golang buildfarm. Now, with COVID-19, I guess that there are more important subjects...  
Anyway, I can test on different AIX HW. Power8 is easy. More complicated, but possible, for Power9, which has different ABI and FP possibilities.  
About providing Boost's project with my results, that may be complicated. To be studied later. Before, once we have understood/fixed the 1/2 dozen of GCC bugs that are making my life like hell, I'll be able to find Boost issues (if any) or tests to be improved, and propose patches to Boost community.

---

## Comment 20

> Username: trex58  
> Created at: 2020-03-27 14:04:25 UTC  
> Url: https://github.com/boostorg/math/issues/327#issuecomment-605018334  

> We can also be a bit more careful about relativizing tolerance to condition number of function evaluation.   
  
On the small temporary Intel+AVX+AVX2 machine I'm using now, which does not do FP 128bit computation and has not quadmath (thus adding 80 compile/link failures), I've been able to build a summary of differences between:  
   -march=native  
and:  
   -march=native -ffp-contract=off  
It appears that there is only 1 test of the "math" library having an issue with fmadd :  
    test_lambert_w .  
  
I have to wait for a Intel machine with quadmath in order to say that this test is the only one in that case for "math" library.  
And I'll have to check this for all Boost libraries...

---

## Comment 21

> Username: trex58  
> Created at: 2020-03-27 14:11:59 UTC  
> Url: https://github.com/boostorg/math/issues/327#issuecomment-605022125  

> It is quite common that diverse platforms reveal subtle differences in floating-point software. This seems to be the unfortunate nature of the art form, even if we are all supposedly adhering to IEEE754 and the like.  
  
Yes. Probably that many, if not nearly all, of the test issues I'll find deal only with some very very small differences between the expected result and what Boost on AIX computes (in addition to the tests failing due to GCC bugs). So that it should require few changes in Boost test code.  
  
However, before, I have to manage to understand ALL the reasons why about 16,000 asserts in "math" library do fail on AIX. I've found that already MANY of them are due to bugs in GCC C++ for AIX that we are now fixing. I have to wait for these bugs to be cleaned before having a clearer view of the remaining issues.  
  
> Perhaps the best way to deal with this particular issue is to increase tolerance of the relevant test cases.  
  
Yes. You are the Boost experts and I can only show you my results and ask you what you think about.

---

## Comment 22

> Username: trex58  
> Created at: 2020-03-27 14:32:09 UTC  
> Updated at: 2020-03-27 14:39:58 UTC  
> Url: https://github.com/boostorg/math/issues/327#issuecomment-605032860  

New information about the impact of -O2 and fmadd on AIX.  
Using -ffp-contract=off , that fixes 4 Boost "math" failed tests:  
  
```  
< ../bin.v2/libs/math/test/test_heuman_lambda.test/gcc-9.1.0/debug/address-model-64/link-static/visibility-hidden/test_heuman_lambda  
< ../bin.v2/libs/math/test/test_lambert_w.test/gcc-9.1.0/debug/address-model-64/link-static/visibility-hidden/test_lambert_w  
< ../bin.v2/libs/math/test/legendre_stieltjes_test.test/gcc-9.1.0/debug/address-model-64/link-static/visibility-hidden/legendre_stieltjes_test  
< ../bin.v2/libs/math/test/test_inverse_gamma_distribution.test/gcc-9.1.0/debug/address-model-64/link-static/visibility-hidden/test_inverse_gamma_distribution  
```  
  
Thus, in addition to the already known test_lambert_w , there are 3 more tests having issues with fmadd:  
```  
 - test_heuman_lambda  
 - legendre_stieltjes_test  
 - test_inverse_gamma_distribution  
```  
Studying them from now.

---

## Comment 23

> Username: jzmaddock  
> Created at: 2020-03-27 14:51:39 UTC  
> Url: https://github.com/boostorg/math/issues/327#issuecomment-605042761  

I can confirm that legrendre_stietjes_test fails here too on Mingw in release mode and -march=native:  
```  
Running 1 test case...  
legendre_stieltjes_test.cpp(95): error: in "LegendreStieltjesZeros": absolute value of ls2(zeros[0]){-2.7755575615628914e-16} exceeds 2.2204460492503131e-16  
```  
  
BTW when the special function tests are run on a new platform/compiler, the chances are that pretty much all of them will fail since we have no default "expected tolerance".... it's a case of looking at the error rates and comparing to other platforms and seeing if they are acceptable.

---

## Comment 24

> Username: NAThompson  
> Created at: 2020-03-27 15:04:57 UTC  
> Url: https://github.com/boostorg/math/issues/327#issuecomment-605050052  

Just worked out that for a root x of f, the numerical evaluation can only be expected to satisfy |f(x)| <= µ|xf'(x)|, where µ is the unit roundoff. I remember that the condition number of rootfinding is given in Corless's book; need to look this up.

---

## Comment 25

> Username: jzmaddock  
> Created at: 2020-03-27 19:04:21 UTC  
> Url: https://github.com/boostorg/math/issues/327#issuecomment-605216168  

>Old codes, mainly Fortran code, have seen so many different environments (HW, OS, Compiler) that they are close to perfect.  
  
Don't be so sure: as a trivial example if you run the venerable bratio fortran routine through our test data you'll find hundreds (if not thousands) of test cases where bratio explodes and generates a NaN.  This is not to denigrate this old code: without the associated paper we would never have implemented our routine, and for it's time it's state of the art.  But there's no way those guys could could crack out high precision test values using multiprecision arithmetic (or indeed Mathematica) as we can now.  As a result many of those old routines have fairly cursory tests *by todays standards*.
