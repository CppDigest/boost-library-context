# #201 - [Build] Boost::Hana on Windows with Clang 3.7.0 using MinGW64 and Visual Studio 2015 [Closed]

> Username: m-j-w  
> Created at: 2015-11-09 20:18:23 UTC  
> Updated at: 2015-11-17 06:37:49 UTC  
> Closed at: 2015-11-15 16:25:59 UTC  
> Url: https://github.com/boostorg/hana/issues/201  

More a success story than issue: I've been building Hana with Clang 3.7.0 (libstdc++ of gcc 5.2.0), both provided by MinGW64, and it compiled and ran all tests and examples successfully (917 of 917), with and without exceptions enabled, respectively.  
  
Secondly, I've tried LLVM/Clang on Windows with the prebuilt windows binaries from http://llvm.org/releases/download.html . After generating the solution with cmake, changing manually the platform toolset to `LLVM-vs2014`, I chose manually some of the tests and examples, and compiled and ran them successfully. This includes the known difficult tests with type traits and the 9 parts of tuple tests.  
The build is a bit verbose due to some warnings about unrequired compiler flags, and features being GNU extensions, such like string literals. To my surprise, even the VS debugger works with the Clang-generated binaries... Unfortunately I'm not too familiar with the Visual Studio toolchain, so I can't provide a patch for the cmake scripts or to automate the testing framework. At least not in the near future.  
  
Anyway, I'd say there are now at least two possible ways to use Boost::Hana on Windows. And Cygwin should also work fine once GCC is upgraded from 4.9.3 to 5.2.0 or higher.

---

## Comment 1

> Username: badair  
> Created at: 2015-11-10 12:39:23 UTC  
> Url: https://github.com/boostorg/hana/issues/201#issuecomment-155409784  

This is great news. I'm going to try this tomorrow. Thanks!

---

## Comment 2

> Username: ldionne  
> Created at: 2015-11-11 16:19:43 UTC  
> Url: https://github.com/boostorg/hana/issues/201#issuecomment-155832955  

@m-j-w  Thanks a bunch! I'll try to see if I can setup continuous integration on AppVeyor.

---

## Comment 3

> Username: m-j-w  
> Created at: 2015-11-11 21:20:11 UTC  
> Url: https://github.com/boostorg/hana/issues/201#issuecomment-155913665  

Well, it appears to be rather trivial after all: in `cmd.exe`  
  
```  
cd hana  
mkdir build  
cd build  
cmake .. -T LLVM-vs2014 -DCMAKE_CXX_COMPILER="C:\Program Files\LLVM\msbuild-bin\cl.exe" -G "Visual Studio 14 2015 Win64" -DCMAKE_CXX_FLAGS="-Wno-gnu-string-literal-operator-template"`  
```  
  
Then simply opening the created `Boost.Hana.sln` with Visual Studio 2015 and run from the menu `Tests > Run > All` and wait.. and wait... and wait... and **918 successful, 4 failed**.  
  
The failing tests are:  
- test.ext.std.array.part3 (hana\include\boost/hana/detail/algorithm.hpp(83,14): error : call to 'lexicographical_compare' is ambiguous)  
- test.tuple.empty_member (hana\test\tuple\empty_member.cpp(17,9): error : static_assert failed)  
- example.misc.printable (hana\example\misc\printable.cpp(9,10): fatal error : 'cxxabi.h' file not found)  
- example.type.comparable (hana\example\type\basic_type.cpp(12,10): fatal error : 'cxxabi.h' file not found)  
  
Well, no doubt about the reason for the missing `cxxabi.h`, but no idea about the other two. With only two of the real tests failing that should for sure count as _Windows, Linux, and iOS supported_.  
  
@ldionne Checking for Clang-CL/MSVC in `<hana>/include/boost/hana/config.hpp` would be possible with an  
  
```  
#if defined(__clang__) && defined(_MSC_VER)  
#    if _MSC_VER>=1900  
            // supported  
#    else  
            // unsupported  
#    endif  
#endif  
```  
  
A last tip: I initially modified the `<hana>/include/boost/hana/config.h`, where I changed all `#warning` to `#pragma message`, because the MS compiler doesn't accept `#warning`, but clang-cl does. So if a fatal error occurs there, you definetly have the wrong compiler or platform toolset selected !

---

## Comment 4

> Username: m-j-w  
> Created at: 2015-11-11 21:40:08 UTC  
> Url: https://github.com/boostorg/hana/issues/201#issuecomment-155918233  

Hmm, actually reading the error helps as well. With respect to test.tuple.empty_member (hana\test\tuple\empty_member.cpp(17,9): error : static_assert failed):  
  
```  
int main() {  
    {  
        using T = hana::tuple<int, A>;  
        std::cout << sizeof(T) << std::endl;    // gives 8  
        std::cout << sizeof(A) << std::endl;    // gives 1  
        std::cout << sizeof(int) << std::endl;    // gives 4  
    }  
}  
```  
  
So not really a failure in Hana.

---

## Comment 5

> Username: ldionne  
> Created at: 2015-11-11 21:41:50 UTC  
> Url: https://github.com/boostorg/hana/issues/201#issuecomment-155918611  

I'm looking at it right now. Seems like the compiler isn't doing the empty base optimization (EBO)?  
  
Regarding the error in `boost/hana/detail/algorithm.hpp`, it's most likely due to an unqualified call to `lexicographical_compare`. I'm testing the fix locally right now.

---

## Comment 6

> Username: m-j-w  
> Created at: 2015-11-11 21:44:21 UTC  
> Url: https://github.com/boostorg/hana/issues/201#issuecomment-155919200  

And with respect to `lexicographical_compare`, these are the candidates found by clang-cl:  
  
```  
410>  W:\Libraries\hana\include\boost/hana/detail/algorithm.hpp(67,20) :  note: candidate function [with InputIter1 = std::_Array_const_iterator<int, 1>, InputIter2 = std::_Array_const_iterator<int, 0>, BinaryPred = boost::hana::placeholder_detail::less]  
410>      constexpr bool lexicographical_compare(InputIter1 first1, InputIter1 last1,  
410>                     ^  
410>  C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\include\xutility(3059,7) :  note: candidate function [with _InIt1 = std::_Array_const_iterator<int, 1>, _InIt2 = std::_Array_const_iterator<int, 0>, _Pr = boost::hana::placeholder_detail::less]  
410>          bool lexicographical_compare(_InIt1 _First1, _InIt1 _Last1,  
410>               ^  
```  
  
I'd say the second is apparently wrong...

---

## Comment 7

> Username: ldionne  
> Created at: 2015-11-11 21:47:46 UTC  
> Updated at: 2015-11-11 21:50:13 UTC  
> Url: https://github.com/boostorg/hana/issues/201#issuecomment-155920022  

@m-j-w Could you please tell me the output of the following program?  
  
``` c++  
#include <boost/hana.hpp>  
#include <iostream>  
#include <tuple>  
namespace hana = boost::hana;  
  
  
struct Empty { };  
  
template <typename T>  
struct Holder { T value; };  
struct EBO : Empty, Holder<int> { };  
  
  
int main() {  
    std::cout << sizeof(hana::tuple<Empty, int>) << std::endl;  
    std::cout << sizeof(int) << std::endl;  
    std::cout << sizeof(Empty) << std::endl;  
    std::cout << sizeof(std::tuple<Empty, int>) << std::endl;  
    std::cout << sizeof(EBO) << std::endl;  
}  
```  
  
On my machine, the output is  
  
```  
4  
4  
1  
4  
4  
```  
  
Regarding `lexicographical_compare`, what happens is that ADL kicks-in because `std::array::iterator` is some standard library type (defined in namespace `std`) instead of a simple pointer. Since my call to `lexicographical_compare` is not qualified, the one in namespace `std` is found by ADL, but the one in namespace `boost::hana::detail` is also found, hence the ambiguity. I'm about to push a commit that fixes this.  
  
[Edit: This should be fixed in cb390d2c82c, which I just pushed to develop.]

---

## Comment 8

> Username: m-j-w  
> Created at: 2015-11-11 21:58:11 UTC  
> Url: https://github.com/boostorg/hana/issues/201#issuecomment-155922458  

First, it happens both in Debug and Release, and I don't find a switch for changing EBO, but I'd also guess that to be the reason. And the output to your test case is  
  
```  
8  
4  
1  
8  
4  
```

---

## Comment 9

> Username: m-j-w  
> Created at: 2015-11-11 22:08:33 UTC  
> Url: https://github.com/boostorg/hana/issues/201#issuecomment-155924957  

Regarding EBO: Seems to be an emulated special feature of MSVC, aka bug, where only a first base class is optimized. See http://stackoverflow.com/a/12714226

---

## Comment 10

> Username: ldionne  
> Created at: 2015-11-11 22:10:44 UTC  
> Url: https://github.com/boostorg/hana/issues/201#issuecomment-155925750  

The issues with `<cxxabi.h>` should be fixed in c8850d8.

---

## Comment 11

> Username: m-j-w  
> Created at: 2015-11-11 22:24:46 UTC  
> Url: https://github.com/boostorg/hana/issues/201#issuecomment-155928758  

Yepp, and `example.type.comparable` and `test.ext.std.array.part3` is also working. Haven't installed the full boost library currently for MSVC, so can't test `example.misc.printable` right now, but I'd assume its working...

---

## Comment 12

> Username: ldionne  
> Created at: 2015-11-11 22:29:10 UTC  
> Url: https://github.com/boostorg/hana/issues/201#issuecomment-155929709  

> Regarding EBO: Seems to be an emulated special feature of MSVC, aka bug, where only a first base class is optimized. See http://stackoverflow.com/a/12714226  
  
Thanks a lot for the research. This is seriously fucked up. I could tweak the test to workaround EBO not being available. However, is there no switch to make clang-cl compliant and not emulate MSVC bugs?

---

## Comment 13

> Username: m-j-w  
> Created at: 2015-11-11 22:35:23 UTC  
> Updated at: 2015-11-11 22:37:23 UTC  
> Url: https://github.com/boostorg/hana/issues/201#issuecomment-155931021  

I've only read in some other mailing list that the clang guys had a lot of trouble in actually emulating all those special behaviours to make clang-cl a drop-in replacement for the MS compiler. Same for the types char16_t and char32_t which have supposedly been more or less silently introduced. So I'd guess no. The neat cite reads something like: clang tries to parse, when failing due to MS special behaviour tries to recover, and finally tries to compile. Says pretty much everything...  
  
And those specialties need to be handled to be "compatible" with the MSVC standard C++ library.

---

## Comment 14

> Username: ldionne  
> Created at: 2015-11-11 22:37:59 UTC  
> Url: https://github.com/boostorg/hana/issues/201#issuecomment-155931506  

Fixed in 76a0ac16a4967341b01257c2813b1504084f4e89. I truly hate working around such standard noncompliance, but in this case the fix is fairly self-contained. Please let me know if it works.

---

## Comment 15

> Username: m-j-w  
> Created at: 2015-11-11 22:46:31 UTC  
> Url: https://github.com/boostorg/hana/issues/201#issuecomment-155933377  

Sorry, no, it doesn't.  
  
```  
2>W:\Libraries\hana\test\tuple\empty_member.cpp(30,5): error : static_assert failed ""  
2>      static_assert((sizeof(hana::tuple<int, EmptyA>) == sizeof(int)), "");  
2>      ^             ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
2>  W:\Libraries\hana\test\tuple\empty_member.cpp(42,17) :  note: in instantiation of template class 'test<>' requested here  
2>  template struct test<>;  
2>                  ^  
2>  1 warning and 1 error generated.  
```  
  
The warning is issued by `config.hpp`.

---

## Comment 16

> Username: m-j-w  
> Created at: 2015-11-11 22:48:27 UTC  
> Url: https://github.com/boostorg/hana/issues/201#issuecomment-155933759  

Of course, static_asserts are parsed during template instantiation. So that deactivation doesn't work since candidate selection happens afterwards.

---

## Comment 17

> Username: ldionne  
> Created at: 2015-11-11 22:52:07 UTC  
> Url: https://github.com/boostorg/hana/issues/201#issuecomment-155934629  

Nope, it really should work, since `EmptyA` and `EmptyB` are dependent. The `static_assert`s can't possibly trigger before `test<...>` is instantiated, but it is only instantiated with `false`, in which case the empty specialization is picked. I just tested with a `static_assert` that should fail and changed the condition to `!EBO_is_supported`, and it indeed succeeds (as expected). I suspect this is another non-standard behaviour of Clang-cl. I'll think of a workaround, but in all cases this isn't too bad.

---

## Comment 18

> Username: m-j-w  
> Created at: 2015-11-11 22:56:16 UTC  
> Url: https://github.com/boostorg/hana/issues/201#issuecomment-155935759  

Well, you could compare the tuples against equivalently complicated simple non-templated structs, just like in the other test above. And either way, it's just a test, but a nice outlook on what cross-platform support might lead to...

---

## Comment 19

> Username: ldionne  
> Created at: 2015-11-11 23:04:02 UTC  
> Url: https://github.com/boostorg/hana/issues/201#issuecomment-155937424  

> Well, you could compare the tuples against equivalently complicated simple non-templated structs  
  
You're right, thanks. db487b2 should work, I hope.  
  
I'd like to get Appveyor set up so I can get Windows feedback without relying on external folks, but I'm terribly clueless with Windows. See for example https://ci.appveyor.com/project/ldionne/hana/build/1.0.13. If you have any idea on how I can download your Clang-cl in powershell or cmd.exe, please let me know.  
  
I have to go for now, but thanks a lot for the feedback! I'll check this again tomorrow or in a couple of days (I have some school stuff to do these days).

---

## Comment 20

> Username: m-j-w  
> Created at: 2015-11-11 23:32:27 UTC  
> Updated at: 2015-11-14 19:16:28 UTC  
> Url: https://github.com/boostorg/hana/issues/201#issuecomment-155943571  

Sorry to disappoint you, doesn't. Here's a modified version with outputs:  
  
``` C++  
/*  
@copyright Louis Dionne 2015  
Distributed under the Boost Software License, Version 1.0.  
(See accompanying file LICENSE.md or copy at http://boost.org/LICENSE_1_0.txt)  
 */  
  
#include <boost/hana/tuple.hpp>  
#include <iostream>  
namespace hana = boost::hana;  
  
//  
// This test makes sure that `hana::tuple` has a simple enough representation.  
// In particular, we make sure that the empty base-class optimization (EBO)  
// is able to fire, when supported.  
//  
  
template <typename T>  
struct holder { T value; };  
  
template <typename ...T>  
struct inherit : T... { };  
  
struct A { };  
struct B { };  
  
//static_assert((sizeof(hana::tuple<int, A>) == sizeof(inherit<holder<int>, A>)), "");  
//static_assert((sizeof(hana::tuple<A, int>) == sizeof(inherit<A, holder<int>>)), "");  
//static_assert((sizeof(hana::tuple<A, int, B>) == sizeof(inherit<A, holder<int>, B>)), "");  
//static_assert((sizeof(hana::tuple<A, B, int>) == sizeof(inherit<A, B, holder<int>>)), "");  
//static_assert((sizeof(hana::tuple<int, A, B>) == sizeof(inherit<holder<int>, A, B>)), "");  
  
int main() {   
    std::cout << "A: " << sizeof(A) << std::endl;  
    std::cout << "B: " << sizeof(B) << std::endl;  
    std::cout << std::endl;  
    std::cout << "hana::tuple<int, A>:        " << sizeof(hana::tuple<int, A>) << std::endl;  
    std::cout << "hana::tuple<A, int>:        " << sizeof(hana::tuple<A, int>) << std::endl;  
    std::cout << "hana::tuple<A, int, B>:     " << sizeof(hana::tuple<A, int, B>) << std::endl;  
    std::cout << "hana::tuple<A, B, int>:     " << sizeof(hana::tuple<A, B, int>) << std::endl;  
    std::cout << "hana::tuple<int, A, B>:     " << sizeof(hana::tuple<int, A, B>) << std::endl;  
    std::cout << std::endl;  
    std::cout << "inherit<holder<int>, A>:    " << sizeof(inherit<holder<int>, A>) << std::endl;  
    std::cout << "inherit<A, holder<int>>:    " << sizeof(inherit<A, holder<int>>) << std::endl;  
    std::cout << "inherit<A, holder<int>, B>: " << sizeof(inherit<A, holder<int>, B>) << std::endl;  
    std::cout << "inherit<A, B, holder<int>>: " << sizeof(inherit<A, B, holder<int>>) << std::endl;  
    std::cout << "inherit<holder<int>, A, B>: " << sizeof(inherit<holder<int>, A, B>) << std::endl;  
}  
```  
  
MinGW64 clang gives  
  
```  
A: 1  
B: 1  
  
hana::tuple<int, A>:        4  
hana::tuple<A, int>:        4  
hana::tuple<A, int, B>:     4  
hana::tuple<A, B, int>:     4  
hana::tuple<int, A, B>:     4  
  
inherit<holder<int>, A>:    4  
inherit<A, holder<int>>:    4  
inherit<A, holder<int>, B>: 4  
inherit<A, B, holder<int>>: 4  
inherit<holder<int>, A, B>: 4  
```  
  
and MSVC/Clang-cl gives  
  
```  
A: 1  
B: 1  
  
hana::tuple<int, A>:        8  
hana::tuple<A, int>:        8  
hana::tuple<A, int, B>:     8  
hana::tuple<A, B, int>:     12  
hana::tuple<int, A, B>:     12  
  
inherit<holder<int>, A>:    4  
inherit<A, holder<int>>:    4  
inherit<A, holder<int>, B>: 4  
inherit<A, B, holder<int>>: 8  
inherit<holder<int>, A, B>: 8  
```

---

## Comment 21

> Username: m-j-w  
> Created at: 2015-11-12 00:05:54 UTC  
> Url: https://github.com/boostorg/hana/issues/201#issuecomment-155951898  

Hmm, not familiar with Appveyor, yet. But the following might help:  
1. You need the following clang version: http://llvm.org/releases/3.7.0/LLVM-3.7.0-win64.exe  
It'll install to the path specified below.  
2. You need the integration of clang-cl into MSVC: http://llvm.org/builds, specifically http://llvm.org/pre-releases/win-snapshots/ClangFormat-r252455.vsix . This will create the platform toolkit definition.  
Perhaps you can install that on the command line using `start ClangFormat-r252455.vsix`. But I'd guess you need a least some additional arguments.   
3. Then take the cmake line from above: `cmake .. -T LLVM-vs2014 -DCMAKE_CXX_COMPILER="C:\Program Files\LLVM\msbuild-bin\cl.exe" -G "Visual Studio 14 2015 Win64" -DCMAKE_CXX_FLAGS="-Wno-gnu-string-literal-operator-template"`  
  
The -T defines the platform toolset, meaning the toolchain aka compiler executable and linker executable and all the default flags. The chosen generator -G also defines some defaults and creates the correct solution for VS 2015 (despite the toolset actually reading LLVM-vs2014). However, you then have the Visual Studio solution which you need to open and run. Problably there's also some command line thing, but I don't know that yet...  
Also, Microsoft just recently published an executable of MSVC without that GUI stuff, explicitely for the high demand in build farms. Haven't tried that either.  
  
When I tried to run clang-cl from the command line I had quite a lot problems with the libraries for 32 and 64bit. Adding `-m32` to `CMAKE_CXX_FLAGS` solved that. I read somewhere that this does not necessarily mean that it will be a 32bit executable, or was it vice versa ? It's a bit opaque what's actually happening inside that triangle cmake > msvc > clang-cl ...  
  
Someone else reading this who might be able to help ?

---

## Comment 22

> Username: m-j-w  
> Created at: 2015-11-12 00:12:36 UTC  
> Url: https://github.com/boostorg/hana/issues/201#issuecomment-155954252  

Forgot the actual question: Downloading with powershell. No idea, that's why I prefer to use Cygwin and MinGW64 when I have to work on Windows... but that might help: http://www.powershellatoms.com/basic/download-file-website-powershell/

---

## Comment 23

> Username: ldionne  
> Created at: 2015-11-12 05:40:10 UTC  
> Url: https://github.com/boostorg/hana/issues/201#issuecomment-156007552  

@m-j-w My current problem is actually quite stupid. I manage to download http://llvm.org/releases/3.7.0/LLVM-3.7.0-win64.exe, but then I can't extract the archive! I tried many things like `7z` and simply calling `.\LLVM-3.7.0-win64.exe` but nothing will do it. How do _you_ extract the archive? Do you need a GUI for that?

---

## Comment 24

> Username: m-j-w  
> Created at: 2015-11-12 06:49:02 UTC  
> Url: https://github.com/boostorg/hana/issues/201#issuecomment-156016954  

Ah, I see. Usually that requires the GUI. I checked the .exe for command line args, but it didn't give any, and didn't respond to any. If it actually were a "Windows installer" (extension .msi) then an unattended install from the command line would be possible, see the "package manager" at https://technet.microsoft.com/en-us/library/cc759262%28v=ws.10%29.aspx  
Probably that's something for the clang bugzilla, asking for a .msi that permits the unattended install on build farms, including the Visual Studio integration. One might still run into the problem of requiring admin rights, see e.g. https://llvm.org/bugs/show_bug.cgi?id=17118, not sure whether AppVeyor is granting these.  
  
However, building a visual studio solution (.sln) is possible from the command line with `msbuild "mysolution.sln"`, see e.g. the code in the image on the start web page of AppVeyor. (solution == make target)  
  
Looks like #202 could also be a reason for the above EBO difficulty. I'll check when you commit.

---

## Comment 25

> Username: m-j-w  
> Created at: 2015-11-12 07:07:13 UTC  
> Updated at: 2015-11-12 14:25:25 UTC  
> Url: https://github.com/boostorg/hana/issues/201#issuecomment-156019777  

On second thought, asking AppVeyor whether they might be willing to add clang-cl and the integration to their portfolio might be less troublesome. They appear to have a large set of software already installed, including MinGW64 and Cygwin.

---

## Comment 26

> Username: m-j-w  
> Created at: 2015-11-13 20:01:55 UTC  
> Url: https://github.com/boostorg/hana/issues/201#issuecomment-156542561  

FYI, the last commit 537e4fe645fdc2bd0f1b026a7e3ee62a221aa013 for #202 had no effect on this EBO problem. The output given above remains the same. Let's blame the compiler...

---

## Comment 27

> Username: ldionne  
> Created at: 2015-11-13 20:02:45 UTC  
> Url: https://github.com/boostorg/hana/issues/201#issuecomment-156542714  

Yes, this is what I thought. It's definitely the compiler emulating MSVC's bug.

---

## Comment 28

> Username: m-j-w  
> Created at: 2015-11-14 19:15:42 UTC  
> Updated at: 2015-11-14 19:16:10 UTC  
> Url: https://github.com/boostorg/hana/issues/201#issuecomment-156736465  

And also just for reference, which I could already have done earlier and saved us quite some time, the actual MSVC compiler also results in  
  
```  
A: 1  
B: 1  
  
inherit<holder<int>, A>:    4  
inherit<A, holder<int>>:    4  
inherit<A, holder<int>, B>: 4  
inherit<A, B, holder<int>>: 8  
inherit<holder<int>, A, B>: 8  
```  
  
for the above test case. Apparently, I couldn't add the `hana::tuple` parts there. It is BTW also unaffected by Debug or Release builds.

---

## Comment 29

> Username: ldionne  
> Created at: 2015-11-14 19:22:46 UTC  
> Url: https://github.com/boostorg/hana/issues/201#issuecomment-156737956  

Thanks. This is indeed not a bug in tuple, just a particularity of Clang-cl that we'll have to workaround. That could be done by simply disabling the tests that check for EBO on that compiler from the CMake files. We already do similar stuff when Boost is missing, or when libc++ is too old because of a bug in `std::tuple`. However, I'd very much like to have some Windows CI set up before introducing workarounds for Windows. Otherwise, I have absolutely no way of testing those changes.

---

## Comment 30

> Username: m-j-w  
> Created at: 2015-11-14 19:25:57 UTC  
> Url: https://github.com/boostorg/hana/issues/201#issuecomment-156738331  

I updated the pull-request (and the PR message).

---

## Comment 31

> Username: m-j-w  
> Created at: 2015-11-14 21:54:48 UTC  
> Updated at: 2015-11-14 22:03:28 UTC  
> Url: https://github.com/boostorg/hana/issues/201#issuecomment-156748633  

This time, I also ran all the possible cases and got the correct warning messages for the compiler detection scripts and the `config.hpp`.  
  
Also, finally, I figured how to build and run the tests from the command line (cmd.exe). For future reference, I'll document that here.  
  
For a 32bit Debug build:  
  
```  
cd hana  
mkdir build_Win32  
cmake .. -TLLVM-vs2014 -G"Visual Studio 14 2015"  
"C:\Program Files (x86)\MSBuild\14.0\Bin\MSBuild.exe" Boost.Hana.sln /p:Configuration=Debug /p:Platform=Win32  
"C:\Program Files (x86)\MSBuild\14.0\Bin\MSBuild.exe" RUN_TESTS.vcxproj /p:Configuration=Debug /p:Platform=Win32  
```  
  
For a 64bit Release build using a less verbose output comparable to the amount of cmake/make (`/v:m /nologo`) and all available processors for multiple build threads (`/m`), and omitted warnings about compiler arguments not being used by clang-cl (`-Qunused-arguments`):  
  
```  
cd hana  
mkdir build_Win64  
cmake .. -TLLVM-vs2014 -G"Visual Studio 14 2015 Win64"  -DCMAKE_CXX_FLAGS="-Qunused-arguments"  
"C:\Program Files (x86)\MSBuild\14.0\Bin\MSBuild.exe" Boost.Hana.sln /p:Configuration=Release /p:Platform=x64 /v:m /m /nologo  
"C:\Program Files (x86)\MSBuild\14.0\Bin\MSBuild.exe" RUN_TESTS.vcxproj /p:Configuration=Release /p:Platform=x64  
```  
  
Note the difference for the invokation of cmake and MSBuild in the platform tag, and, yes, the `LLVM-vs2014` is correct although being for VS 2015. Also note, I didn't test with the other boost libraries.  
  
For a Hana build and test run, I highly recommend the use of the command line. Due to the close to 1000 test cases (and probably growing), MSVC (the IDE) is rather slow with all those background indexing processes.  
Unlike mentioned in quite a few other howtos, it is not necessary to run the above command from a command line invoked from within Visual Studio. The regular cmd.exe is absolutely sufficient.  
  
There also seems to be a chance in generating a build for ARM (replace Win64 with ARM in the above example), since being available as cmake generator. But I can't test that.   
  
With these lines you should be able to describe the build matrix in the appveyor JSON definition, once the clang-cl issue is solved.  
  
A closing remark: If you would prefer adding such platform specific notes somewhere more permanent, the github wiki might be a good place. I'd expect the compiler landscape as a whole to improve at a faster pace than major releases of boost are released. There could then also be notes for platforms where you don't have an automatic build and test, and which would thus only have limited support.

---

## Comment 32

> Username: badair  
> Created at: 2015-11-14 23:17:01 UTC  
> Url: https://github.com/boostorg/hana/issues/201#issuecomment-156756824  

> Due to the close to 1000 test cases (and probably growing), MSVC (the IDE) is rather slow with all those background indexing processes.  
  
While I agree that the Visual Studio IDE is overkill for building the test projects, I found that disabling Intellisense (and then restarting VS) increased UI responsiveness to a usable level, at least once the projects have loaded.

---

## Comment 33

> Username: m-j-w  
> Created at: 2015-11-15 00:13:10 UTC  
> Url: https://github.com/boostorg/hana/issues/201#issuecomment-156763230  

Well, that wasn't meant to be a general recommendation for application development. But after having run quite a lot of test runs, I find it rather impractical for the very purpose given above.  
  
@badair would you mind checking the correctness of the PR by running the tests after @ldionne accepts it ? Most likely he will feel relieved not having to rely solely on one confirmation of one single user.

---

## Comment 34

> Username: ldionne  
> Created at: 2015-11-15 16:12:42 UTC  
> Url: https://github.com/boostorg/hana/issues/201#issuecomment-156825099  

> A closing remark: If you would prefer adding such platform specific notes somewhere more permanent, the github wiki might be a good place.  
  
Thanks for the notes, I just added them to [the wiki](https://github.com/boostorg/hana/wiki/Building-on-Windows).   
  
> @badair would you mind checking the correctness of the PR by running the tests after @ldionne accepts it ? Most likely he will feel relieved not having to rely solely on one confirmation of one single user.  
  
Actually, could you do that _before_ I merge? It can be done by doing  
  
``` shell  
git remote add mjw https://github.com/m-j-w/hana.git  
git fetch mjw windows_build  
git checkout windows_build  
```  
  
and then checking that it works properly.

---

## Comment 35

> Username: ldionne  
> Created at: 2015-11-15 16:25:59 UTC  
> Url: https://github.com/boostorg/hana/issues/201#issuecomment-156826253  

I'm going to close this in favour or #207, which is more targeted.

---

## Comment 36

> Username: badair  
> Created at: 2015-11-16 03:17:42 UTC  
> Updated at: 2015-11-16 03:26:17 UTC  
> Url: https://github.com/boostorg/hana/issues/201#issuecomment-156902299  

I checked out mjw's branch, then built and ran tests for 64 bit.  
  
Test results:  
  
```  
99% tests passed, 3 tests failed out of 538  
  
Total Test time (real) =  16.83 sec  
  
The following tests FAILED:  
      253 - example.tap (OTHER_FAULT)  
      320 - test.bugs.github_202 (Not Run)  
      521 - test.tuple.empty_member (Not Run)  
```  
  
CTest errors: http://pastebin.com/Q5u2VwyC  
  
(re)build results: http://pastebin.com/hiWxKs1w (ctrl + f "error")  
  
Need me to do this for 32bit as well?

---

## Comment 37

> Username: badair  
> Created at: 2015-11-16 03:23:05 UTC  
> Url: https://github.com/boostorg/hana/issues/201#issuecomment-156903677  

> Thanks for the notes, I just added them to the wiki.   
  
Small nitpick - technically there should be a `cd` command after the `mkdir` command in the build steps

---

## Comment 38

> Username: ldionne  
> Created at: 2015-11-16 12:39:37 UTC  
> Url: https://github.com/boostorg/hana/issues/201#issuecomment-157015569  

> Small nitpick - technically there should be a cd command after the mkdir command in the build steps  
  
Fixed. Note that anyone can edit the wiki, so if you see something to fix you can also do it yourself.  
  
> Need me to do this for 32bit as well?  
  
Thanks a lot for checking. No need to do it for 32 bit; I've merge the pull request now. I set up a Windows VM and I'm also trying to run the tests myself.

---

## Comment 39

> Username: badair  
> Created at: 2015-11-17 01:02:32 UTC  
> Url: https://github.com/boostorg/hana/issues/201#issuecomment-157225546  

I just set up the environment on a fresh Windows install the other day. It's pretty straightforward, but I'm happy to help if you get stuck.

---

## Comment 40

> Username: ldionne  
> Created at: 2015-11-17 01:23:33 UTC  
> Url: https://github.com/boostorg/hana/issues/201#issuecomment-157229604  

Thanks, actually it was super easy and I'm now proudly running the tests on Windows. Huge thanks to @m-j-w for all the grunt work of making this possible without too much pain. Now, I'll exclude the unit tests that fail on Windows from the build when we detect Clang-Cl (if possible), so we get everything passing.

---

## Comment 41

> Username: m-j-w  
> Created at: 2015-11-17 06:37:49 UTC  
> Url: https://github.com/boostorg/hana/issues/201#issuecomment-157286996  

Glad to help increase the user experience, and, hopefully, the number of users. And even better that you now have Windows covered semi-automatically on your side, too ;-)
