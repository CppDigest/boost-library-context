# #272 - boost/math/constants/constants.hpp:297:3: error: unable to find numeric literal operator ‘operator""Q’ [Closed]

> Username: zengletian1491  
> Created at: 2019-11-20 10:18:06 UTC  
> Updated at: 2025-11-10 20:44:36 UTC  
> Closed at: 2020-06-13 02:09:59 UTC  
> Url: https://github.com/boostorg/math/issues/272  

Errors occurred when I compile other projects, and the errors were listed in the following:  
  
  
**/usr/local/include/boost/math/constants/constants.hpp:296:3: note: use -std=gnu++11 or -fext-numeric-literals to enable more built-in suffixes  
/usr/local/include/boost/math/constants/constants.hpp: In static member function ‘static constexpr T boost::math::constants::detail::constant_root_half_pi<T>::get(const mpl_::int_<5>&)’:  
/usr/local/include/boost/math/constants/constants.hpp:297:3: error: unable to find numeric literal operator ‘operator""Q’  
   BOOST_DEFINE_MATH_CONSTANT(root_half_pi, 1.253314137315500251207882642405522626e+00, "1.2533141373155002512078826424055226265034933703049691583149617881711468273039209874732  
   ^**  
  
I did not know how to solve this problem. Please give me some suggestions if you have. Thanks very much!

---

## Comment 1

> Username: NAThompson  
> Created at: 2019-11-21 15:59:26 UTC  
> Url: https://github.com/boostorg/math/issues/272#issuecomment-557150275  

@zengletian1491 : What compiler and what OS are you using?

---

## Comment 2

> Username: pabristow  
> Created at: 2019-11-21 17:13:50 UTC  
> Url: https://github.com/boostorg/math/issues/272#issuecomment-557183802  

If you are using GCC, -std=gnu++11 or -fext-numeric-literals are compiler options that you must add.  
  
Otherwise we need details on your platform chip type and compiler version. and what includes you have to help further.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2019-11-21 18:00:34 UTC  
> Url: https://github.com/boostorg/math/issues/272#issuecomment-557202639  

There is a mis-configuration in Boost.Config somewhere that it believes `__float128` is supported when apparently it is not.  
  
My guess is that you're using a compiler that is pretending to be GCC, but isn't.  More details please!  
  
In any case defining BOOST_MATH_DISABLE_FLOAT128 will fix the issue.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2019-11-22 08:35:19 UTC  
> Url: https://github.com/boostorg/math/issues/272#issuecomment-557442039  

>compiler: cmake  
>OS: Ubuntu 16.04.6 LTS  
  
Ummm, cmake is not a compiler, we need the actual compiler being invoked by cmake (looks like it might be gcc but we can't be sure), PLUS the command line it is invoked with, otherwise there's no way to reproduce.

---

## Comment 5

> Username: zengletian1491  
> Created at: 2019-11-22 10:09:38 UTC  
> Updated at: 2019-11-22 10:11:01 UTC  
> Url: https://github.com/boostorg/math/issues/272#issuecomment-557472873  

System-Product-Name:~/SoftwareExtractPackages$ lsb_release -a  
No LSB modules are available.  
Distributor ID:	Ubuntu  
Description:	Ubuntu 16.04.6 LTS  
Release:	16.04  
Codename:	xenial  
  
  
  
System-Product-Name:~/SoftwareExtractPackages$ uname -a  
System-Product-Name 4.15.0-70-generic #79~16.04.1-Ubuntu SMP Tue Nov 12 14:01:10 UTC 2019 x86_64 x86_64 x86_64 GNU/Linux  
  
  
  
**System-Product-Name:~/Work/rtabmap/build$ cmake ..  
-- The C compiler identification is GNU 5.4.0  
-- The CXX compiler identification is GNU 5.4.0  
-- Check for working C compiler: /usr/bin/cc  
-- Check for working C compiler: /usr/bin/cc -- works  
-- Detecting C compiler ABI info  
-- Detecting C compiler ABI info - done  
-- Detecting C compile features  
-- Detecting C compile features - done  
-- Check for working CXX compiler: /usr/bin/c++  
-- Check for working CXX compiler: /usr/bin/c++ -- works  
-- Detecting CXX compiler ABI info  
-- Detecting CXX compiler ABI info - done  
-- Detecting CXX compile features  
-- Detecting CXX compile features - done**  
  
  
  
Thanks very much!

---

## Comment 6

> Username: jzmaddock  
> Created at: 2019-11-22 12:52:08 UTC  
> Url: https://github.com/boostorg/math/issues/272#issuecomment-557520345  

Nope, still missing something.... I updated the Boost.Config CI tests to check gcc-5 in gnu mode and all the tests are still passing (and verify that __float128 support is turned on): https://travis-ci.org/boostorg/config/jobs/615513039  
  
So I think we need the full g++ command line.

---

## Comment 7

> Username: zengletian1491  
> Created at: 2019-11-25 23:57:30 UTC  
> Url: https://github.com/boostorg/math/issues/272#issuecomment-558395718  

I'm really sorry for my late reply. Well, after "cmake .." is typed, the results are shown in the attached file "afer cmake.docx";  
after "make" is typed, the results are shown in the attached file named "after make.docx". Thanks very much for your help!  
[after cmake.docx](https://github.com/boostorg/math/files/3889327/after.cmake.docx)  
[after make.docx](https://github.com/boostorg/math/files/3889328/after.make.docx)

---

## Comment 8

> Username: jzmaddock  
> Created at: 2019-11-26 13:20:05 UTC  
> Url: https://github.com/boostorg/math/issues/272#issuecomment-558624664  

Still not seeing a compiler command line in there, can you post the generated Makefile as well?

---

## Comment 9

> Username: zengletian1491  
> Created at: 2019-11-28 03:20:04 UTC  
> Url: https://github.com/boostorg/math/issues/272#issuecomment-559325522  

The Makefile is in the attached file!Thank you!  
[Makefile.txt](https://github.com/boostorg/math/files/3899682/Makefile.txt)

---

## Comment 10

> Username: jzmaddock  
> Created at: 2019-11-28 09:56:53 UTC  
> Url: https://github.com/boostorg/math/issues/272#issuecomment-559424526  

Ah... that's just the top level Makefile and the actual commands are apparently split between multiple sub-files :(  
  
After a bit of googling, it looks like running:  
  
make VERBOSE=1  
  
should hopefully spit out the actual commands being used.

---

## Comment 11

> Username: zengletian1491  
> Created at: 2019-11-29 01:32:33 UTC  
> Url: https://github.com/boostorg/math/issues/272#issuecomment-559631167  

This is the “build”file obtained with "cmake.." and "make VERBOSE=1"  
[build.zip](https://github.com/boostorg/math/files/3903377/build.zip)

---

## Comment 12

> Username: jzmaddock  
> Created at: 2019-11-29 09:56:53 UTC  
> Url: https://github.com/boostorg/math/issues/272#issuecomment-559731905  

Reduced test case (with gcc-5.x only) is:  
  
```  
g++ -I../../.. -std=c++11 -std=gnu++11 compile_test/constants_incl_test.cpp  
```  
  
Everything is fine with either one or the other -std options, it's mixing the 2 that confuses things.

---

## Comment 13

> Username: jzmaddock  
> Created at: 2019-11-29 18:17:31 UTC  
> Url: https://github.com/boostorg/math/issues/272#issuecomment-559859011  

OK, more investigation reveals that this is an issue for gcc-5 and 6, but was fixed for gcc-7 and later.  
  
So the workarounds are:  
  
1) Don't mix -std=c++11 and -std=gnu++11 on the same command line.  This might require fixing CMake though ;(  Or,  
2) As the error message suggests, add -fext-numeric-literals to the compiler flags.  Or  
3) Add -DBOOST_MATH_DISABLE_FLOAT128 to the compiler flags.  Or  
4) Add `#define BOOST_MATH_DISABLE_FLOAT128` to boost/math/tools/user.hpp`.  
  
At present I simply don't see any way to fix this at our end because the list of predefined macros gcc sets is identical between `-std=c++11 -std=gnu++11` and just `-std=gnu++11`.  Which of course they should be since the second -std= option supersedes the first.  Unfortunately mixing the 2 options like this seems to cause gcc to get into an inconsistent internal state, with the `-std=gnu++11` not being fully honoured.  
  
I'm inclined to close this as "not our problem", but perhaps I'll leave open for future Googlers to find... or in hopes of a bright idea from someone.  In any case, I doubt there will be any more patches/releases of gcc-5 at this stage.

---

## Comment 14

> Username: zengletian1491  
> Created at: 2019-11-30 02:47:55 UTC  
> Url: https://github.com/boostorg/math/issues/272#issuecomment-559906912  

Thanks very much for your help！@jzmaddock

---

## Comment 15

> Username: biergaizi  
> Created at: 2025-11-10 20:44:20 UTC  
> Updated at: 2025-11-10 20:44:36 UTC  
> Url: https://github.com/boostorg/math/issues/272#issuecomment-3513825646  

I've just encountered the same arcane compile failure in my project. The project previously built with a global `CXXFLAGS` with `-std=c++11`  without any problems. But it started to fail after adding `set(CMAKE_CXX_STANDARD 11)` in `CMakeLists.txt` with this error message.  
  
According to this report, basically, CMake was adding `-std=gnu++11` and the global `CXXFLAGS` was adding `-std=c++11`, putting the compiler in an inconsistent state, so Boost believed `__float128` support is turned on when it's actually not. The solution is to remove `-std=c++11` from `CXXFLAGS`.  
  
Thanks for all the troubleshooting here, it allowed me to make a trivial fix and document this legacy behavior in the documentation effortlessly.
