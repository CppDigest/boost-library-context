# #207 - Linking boost libs against libc++ standard lib [Closed]

> Username: bshastry  
> Created at: 2018-10-16 13:40:43 UTC  
> Updated at: 2020-06-10 13:26:43 UTC  
> Closed at: 2020-06-10 13:26:43 UTC  
> Url: https://github.com/boostorg/boost/issues/207  

Hello,  
  
I had a look at the boost continuous fuzzing test harnesses and build configuration over at [oss-fuzz](https://github.com/google/oss-fuzz/blob/master/projects/boost/) and was left with the impression that the current harnesses test only small portions of the boost library mostly because the portions that need to be tested need to be linked against the LLVM implementation of the C++ standard library (libc++).  
  
I was wondering if there is something I could do to get all boost code to build using the clang/llvm toolchain? I believe the pay-off from this effort could be that more targets can be added and continuously fuzzed. Moreover, other projects that depend on one or more boost libraries can benefit from this as well.  
  
Thank you.

---

## Comment 1

> Username: mclow  
> Created at: 2018-10-16 15:20:18 UTC  
> Url: https://github.com/boostorg/boost/issues/207#issuecomment-430279886  

If you look at the test runners, you'll see that Boost is built with clang/llvm all the time. In fact, I run some of those: https://www.boost.org/development/tests/master/developer/summary.html.

---

## Comment 2

> Username: bshastry  
> Created at: 2018-10-16 15:52:05 UTC  
> Updated at: 2018-10-16 15:53:19 UTC  
> Url: https://github.com/boostorg/boost/issues/207#issuecomment-430291912  

Thank you :-)  
  
Maybe I wasn't configuring the build environment properly. Here's what I do  
  
```  
./bootstrap.sh && ./b2 headers install  
```  
  
and this kicks off a build via system installed gcc and g++.  
  
Is there anything I am missing to get bootstrap to use the compiler/linker and compiler/linker flags during the build process? ATM all the customizations are defined as standard bash env variables i.e., CC,CXX,CFLAGS and so on.  
  
I suppose the goal is to get boost to use clang/clang++ and link against libc++. Thank you.

---

## Comment 3

> Username: bshastry  
> Created at: 2018-10-17 11:13:47 UTC  
> Url: https://github.com/boostorg/boost/issues/207#issuecomment-430587890  

> If you look at the test runners, you'll see that Boost is built with clang/llvm all the time. In fact, I run some of those: https://www.boost.org/development/tests/master/developer/summary.html.  
  
Thanks, I got boost to build with clang but not against libc++. The problem is that I am unable to influence the CXXFLAGS used during the build process.

---

## Comment 4

> Username: jeking3  
> Created at: 2018-11-27 00:24:37 UTC  
> Url: https://github.com/boostorg/boost/issues/207#issuecomment-441851110  

Have you tried using `toolset=clang cxxflags=-stdlib=libc++` ?
