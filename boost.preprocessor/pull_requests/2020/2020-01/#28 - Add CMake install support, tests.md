# #28 Add CMake install support, tests [Merged]

> Username: pdimov  
> Created at: 2020-01-05 04:27:12 UTC  
> Updated at: 2020-01-06 01:20:30 UTC  
> Merged at: 2020-01-06 01:20:30 UTC  
> Closed at: 2020-01-06 01:20:30 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/28  

This updates CMakeLists.txt with install and testing support. It also updates .travis.yml and adds CMake tests to it.  
  
The -std=c++2a failures are not caused by this PR.

---

## Comment 1

> Username: eldiener  
> Created_at: 2020-01-05 07:30:48 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/28#issuecomment-570885141  

What is the reason for the quick.cpp test ? Is there a reason why you added your copyright for appveyor.yml but replaced the original copyright in travis.yml with your own ?   
  
I know about the failures in the tests, thank you, and that they have nothing to do with your PR. The failures in the tests are because of a C++20 __VA_OPT__ bug in gcc, of which they have been notified by me, and the same failure in clang prior to clang-9.0, where the earlier bug was fixed.

---

## Comment 2

> Username: pdimov  
> Created_at: 2020-01-05 12:11:38 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/28#issuecomment-570906070  

The `quick.cpp` test (apart from being used from cmake_subdir_test and cmake_install_test) is part of a convention where, in the superproject CI, we can run a small portion of the Boost test suite either via b2 (by invoking `b2 quick` in `status/`), or now, via CMake (because CTest has an option to filter by test name, so we can use f.ex. `ctest -R quick_cpp` to only execute the `quick.cpp` tests from all the libraries.)  
  
I just copied the entire `.travis.yml` from some other library, copyright and all.  
  
Would you want me to do something about the C++20 failures, for example remove these configurations from `.travis.yml`? Or do you need no further changes to this PR, and will deal with them later in whatever way you've decided?

---

## Comment 3

> Username: eldiener  
> Created_at: 2020-01-05 12:33:36 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/28#issuecomment-570908151  

>   
>   
> The `quick.cpp` test (apart from being used from cmake_subdir_test and cmake_install_test) is part of a convention where, in the superproject CI, we can run a small portion of the Boost test suite either via b2 (by invoking `b2 quick` in `status/`), or now, via CMake (because CTest has an option to filter by test name, so we can use f.ex. `ctest -R quick_cpp` to only execute the `quick.cpp` tests from all the libraries.)  
>   
> I just copied the entire `.travis.yml` from some other library, copyright and all.  
>   
> Would you want me to do something about the C++20 failures, for example remove these configurations from `.travis.yml`? Or do you need no further changes to this PR, and will deal with them later in whatever way you've decided?  
  
I do not know what to do about the C++20 failures in CI testing other than that they are genuine failures indicating compiler bugs and not bugs in the library itself. Am I supposed to remove these tests altogether in the test suite ?  
  
Regarding the `quick.cpp` test is it now going to be standard in Boost libraries to create a small, quick test for the purpose of the superproject CI ? I still do not understand the practical reason for running such a test at the superproject CI level. Does this have something to do with transitioning from b2 to CMake ?  
  
As far as the PR itself I have no problem merging this to 'develop' and eventually to 'master', as it appears to be basically for CMake, about which you know far more than I do.

---

## Comment 4

> Username: pdimov  
> Created_at: 2020-01-05 12:52:46 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/28#issuecomment-570909842  

> I do not know what to do about the C++20 failures in CI testing other than that they are genuine failures indicating compiler bugs and not bugs in the library itself.  
  
Well, I understand nothing about Preprocessor, but if `BOOST_PP_VA_OPT` doesn't work on g++ 8/9 and clang 6-8, shouldn't `BOOST_PP_VARIADIC_HAS_OPT()` be set to 0 for them?  
  
> I still do not understand the practical reason for running such a test at the superproject CI level.  
  
Originally I used it to quickly catch obvious breakages, such as an erroneous test Jamfile being checked in and breaking the entire regression matrix. Now I use it with CMake too for the same purpose; catching an erroneous CMakeLists.txt breaking the Boost CMake build/test.

---

## Comment 5

> Username: eldiener  
> Created_at: 2020-01-05 20:34:32 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/28#issuecomment-570945167  

>   
>   
> > I do not know what to do about the C++20 failures in CI testing other than that they are genuine failures indicating compiler bugs and not bugs in the library itself.  
>   
> Well, I understand nothing about Preprocessor, but if `BOOST_PP_VA_OPT` doesn't work on g++ 8/9 and clang 6-8, shouldn't `BOOST_PP_VARIADIC_HAS_OPT()` be set to 0 for them?  
>   
> > I still do not understand the practical reason for running such a test at the superproject CI level.  
>   
> Originally I used it to quickly catch obvious breakages, such as an erroneous test Jamfile being checked in and breaking the entire regression matrix. Now I use it with CMake too for the same purpose; catching an erroneous CMakeLists.txt breaking the Boost CMake build/test.  
  
Actually `BOOST_PP_VA_OPT` works fine but the equivalent use of just `__VA_OPT__ ` is where the error occurs for the compilers which have the `__VA_OPT__` bug. I could of course remove the equivalent tests for just using `__VA_OPT__ ` and then the VA_OPT testing would just work, but then users of those compilers/versions which have the `__VA_OPT__ ` bug in C++20 mode would never know about it, not that most end-users actually pay attention to testing results anyway. The `BOOST_PP_VARIADIC_HAS_OPT()` just tests whether or not the __VA_OPT__ is recognized as a valid construct in C++20 mode, not whether it is guaranteed to give the correct result in an given potential case.  
  
So the 'quick' test is just used to quickly make sure that a bad jam test file or CMake test has not been checked in ? It seems then that testing would then necessarily have to run the 'quick' test before it runs the normal tests for each library. In that case would it not be better that the 'quick' test in jamfile tems be 'explicit' so that it would not be run again in normal testing ?

---

## Comment 6

> Username: pdimov  
> Created_at: 2020-01-05 23:47:19 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/28#issuecomment-570960911  

You can mark it explicit if you like, but I wouldn't bother. It doesn't really hurt anything to have it executed.

---
