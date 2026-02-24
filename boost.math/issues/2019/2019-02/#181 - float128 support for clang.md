# #181 - float128 support for clang [Closed]

> Username: bgemmill  
> Created at: 2019-02-05 20:28:17 UTC  
> Updated at: 2021-04-01 16:02:32 UTC  
> Closed at: 2020-03-10 12:33:50 UTC  
> Url: https://github.com/boostorg/math/issues/181  

Hello all,  
  
I'm using clang 7 with boost 1.69.0, and noticed that there isn't float128 support in boost math, even though clang seems to support it.  
  
The relevant file is `/include/boost/math/tools/config.hpp`:  
```  
#if defined(_GLIBCXX_USE_FLOAT128) && defined(BOOST_GCC) && !defined(__STRICT_ANSI__) \  
   && !defined(BOOST_MATH_DISABLE_FLOAT128) || defined(BOOST_MATH_USE_FLOAT128)  
//  
// Only enable this when the compiler really is GCC as clang and probably   
// intel too don't support __float128 yet :-(  
//  
#ifndef BOOST_MATH_USE_FLOAT128  
#  define BOOST_MATH_USE_FLOAT128  
#endif  
```  
  
Which seemed to be a result of pre-float128 clang: https://svn.boost.org/trac10/ticket/8265  
  
It looks like clang started adding support around 2015/2016: https://reviews.llvm.org/D15120, and it appears that clang has this support now:  
```  
    __float128 thing = 1e+100Q;  
    thing += 45;  
    thing /= 2;  
    size_t sz = sizeof(thing);  
    std::cout << sz << std::endl;  
    std::cout << (double)thing << std::endl; // cast because I'm not hunting down gcc's quadmath.h in a small demo :-)  
```  
  
Would it be reasonable to change the line in /include/boost/math/tools/config.hpp to check for gcc or clang?

---

## Comment 1

> Username: pabristow  
> Created at: 2019-02-06 09:22:01 UTC  
> Url: https://github.com/boostorg/math/issues/181#issuecomment-460953576  

I have yet to read all the LLVM thread ;-) but enabling this and Boost.Math tests would be excellent workout for __float128.  But access to quadmath.h and quadmath.lib would be needed.  Perhaps a branch to try to get this working?

---

## Comment 2

> Username: NAThompson  
> Created at: 2019-02-06 14:12:14 UTC  
> Url: https://github.com/boostorg/math/issues/181#issuecomment-461036497  

Shouldn't this be a feature request against multiprecision?

---

## Comment 3

> Username: bgemmill  
> Created at: 2019-02-06 15:09:19 UTC  
> Url: https://github.com/boostorg/math/issues/181#issuecomment-461057364  

@pabristow on bionic, the package is libquadmath0, and it lives in `/usr/lib/gcc/x86_64-linux-gnu/${GCC_MAJOR_VERSION}/`  
In cmake, I'm getting it with something like this:  
```  
find_path(Quadmath_INCLUDE_DIR NAMES quadmath.h HINTS /usr/lib/gcc/x86_64-linux-gnu/7/include/)  
find_library(Quadmath_LIBRARY NAMES quadmath HINTS /usr/lib/gcc/x86_64-linux-gnu/7)  
```  
I'm not as familiar with b2; I'd imagine one way to make this go would be the same kind of library detection as zlib, bzip2, etc.  
  
  
@NAThompson probably both. This thread was for `boost::floatmax_t` from [here](https://www.boost.org/doc/libs/1_69_0/libs/math/doc/html/math_toolkit/exact_typdefs.html).  
  
I'm not using boost::multiprecision at the moment, so you'd likely know better what backend they have for their [float128](https://www.boost.org/doc/libs/1_69_0/libs/multiprecision/doc/html/boost_multiprecision/tut/floats/float128.html) on clang.

---

## Comment 4

> Username: pabristow  
> Created at: 2019-02-07 16:56:50 UTC  
> Url: https://github.com/boostorg/math/issues/181#issuecomment-461509591  

I'll look into this - but being a Microsoft user, I have yet to get Clang to work using b2.  
  
However, recently other people have got this working, so if I can crack this I can investigate if using Clang and __float128 is now working - I suspect it is.

---

## Comment 5

> Username: NAThompson  
> Created at: 2019-02-07 23:38:46 UTC  
> Updated at: 2019-02-07 23:38:56 UTC  
> Url: https://github.com/boostorg/math/issues/181#issuecomment-461636868  

@bgemmill : It looks like my Apple clang supports float128, but the compilation target isn't supported:  
  
```  
float128.cpp:5:5: fatal error: __float128 is not supported on this target  
    __float128 x = 1.1Q;  
    ^  
1 error generated.  
make: *** [float128.x] Error 1  
```  
  
Are we equipped to handle this situation? Generally we use compiler versions to find out whether a certain feature is supported, not the compiler + target.

---

## Comment 6

> Username: bgemmill  
> Created at: 2019-02-07 23:44:29 UTC  
> Url: https://github.com/boostorg/math/issues/181#issuecomment-461638162  

@NAThompson you may still be right; what version are you using? I have:  
```  
$ clang --version  
clang version 7.0.1-svn348686-1~exp1~20190113235231.54 (branches/release_70)  
Target: x86_64-pc-linux-gnu  
Thread model: posix  
InstalledDir: /usr/bin  
```

---

## Comment 7

> Username: NAThompson  
> Created at: 2019-02-08 00:12:08 UTC  
> Url: https://github.com/boostorg/math/issues/181#issuecomment-461644049  

```  
 clang++ --version  
Apple LLVM version 10.0.0 (clang-1000.11.45.5)  
Target: x86_64-apple-darwin18.2.0  
Thread model: posix  
InstalledDir: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin  
```

---

## Comment 8

> Username: bgemmill  
> Created at: 2019-02-08 01:06:22 UTC  
> Url: https://github.com/boostorg/math/issues/181#issuecomment-461654782  

@NAThompson looks like apple renumbers the clang version string for some reason, and enterprising users have figured out [which version of clang is actually running](https://en.wikipedia.org/wiki/Xcode#Latest_versions) by looking at apple's cmake source.  
  
If that chart is right, you're running 6.0.1, and that may be why we see different support.  
  
As a different idea, in cmake, we can do tricks like:  
```  
    CHECK_CXX_SOURCE_COMPILES("  
        int main(void){  
            __float128 foo = ::101.01Q;  
        }" FLOAT128_PRESENT)  
```  
Does b2 have anything like that?

---

## Comment 9

> Username: NAThompson  
> Created at: 2019-02-08 01:15:45 UTC  
> Url: https://github.com/boostorg/math/issues/181#issuecomment-461656542  

That would be interesting, but I don't think there is such a thing in b2. I think @jzmaddock just laboriously tests each compiler and adds a feature flag to boost.config.

---

## Comment 10

> Username: bgemmill  
> Created at: 2019-02-08 02:22:45 UTC  
> Url: https://github.com/boostorg/math/issues/181#issuecomment-461668543  

Detection of quadmath and/or @jzmaddock's config magic may be the way forward then. I'll be curious what @pabristow comes up with on the b2 detection front.  
  
Thanks all for giving this bug some love!

---

## Comment 11

> Username: jzmaddock  
> Created at: 2019-02-08 18:50:24 UTC  
> Url: https://github.com/boostorg/math/issues/181#issuecomment-461906047  

>That would be interesting, but I don't think there is such a thing in b2.   
  
`check-target-builds` will do that, the catch is it's only useful for things being built with b2, it's not a "configure" step that writes out config.hpp.  
  
>I think @jzmaddock just laboriously tests each compiler and adds a feature flag to boost.config.  
  
Pretty much.  There is a gotcha here in that at least on ubuntu:  
  
```  
#include <quadmath.h>  
```  
  
Fails for me with clang 4,5 and 6.  Haven't tried 7 yet.  
  
Strangely, linking to -lquadmath always works just fine.  
  
Since it's basically just Math&Multiprecision using __float128, we could provide our own set of declarations for quadmath.h - that may take a while and requires some careful testing.  
  
Otherwise I guess we could enable this only when __has_include(quadmath,h) is true?

---

## Comment 12

> Username: bgemmill  
> Created at: 2019-02-08 19:44:54 UTC  
> Url: https://github.com/boostorg/math/issues/181#issuecomment-461923512  

@jzmaddock clang doesn't appear to provide it's own quadmath header at least in their ubuntu packages. One workaround is pointing clang at gcc's header in `/usr/lib/gcc/x86_64-linux-gnu/7/include/` since it all goes to libquadmath0's shared library.  
  
Otherwise I'm not sure the `__has_include` directive you added would hit anything until the clang folks start including a copy too.

---

## Comment 13

> Username: NAThompson  
> Created at: 2019-02-08 19:51:39 UTC  
> Url: https://github.com/boostorg/math/issues/181#issuecomment-461925691  

Would it be sensible to submit a bug against clang? Presumably `quadmath.h` is licensed in a compatible way with `clang`, so `clang` would only need to vendorize it.

---

## Comment 14

> Username: jzmaddock  
> Created at: 2019-02-08 20:45:39 UTC  
> Url: https://github.com/boostorg/math/issues/181#issuecomment-461941031  

>@jzmaddock clang doesn't appear to provide it's own quadmath header at least in their ubuntu packages. One workaround is pointing clang at gcc's header in `/usr/lib/gcc/x86_64-linux-gnu/7/include/` since it all goes to libquadmath0's shared library.  
  
We have absolutely zero control over the command line - the libraries are "just a bunch of headers" and we don't control how they are used.  So if clang is invoked in a way which does not find quadmath.h then there's not much else we can do (except generate a hard error when a header the user may care less about is not found).

---

## Comment 15

> Username: jzmaddock  
> Created at: 2019-02-08 20:46:48 UTC  
> Url: https://github.com/boostorg/math/issues/181#issuecomment-461941343  

>Would it be sensible to submit a bug against clang? Presumably `quadmath.h` is licensed in a compatible way with `clang`, so `clang` would only need to vendorize it.  
  
quadmath.h ships with gcc, it's one of their system headers, clang should find it IMO (which is not to say it's going to be easy for them to do so).

---

## Comment 16

> Username: pabristow  
> Created at: 2019-02-19 17:42:35 UTC  
> Url: https://github.com/boostorg/math/issues/181#issuecomment-465235632  

From: jzmaddock [mailto:notifications@github.com]  
Sent: 08 February 2019 20:46  
To: boostorg/math  
Cc: Paul A. Bristow; Mention  
Subject: Re: [boostorg/math] float128 support for clang (#181)  
  
  
@jzmaddock<https://github.com/jzmaddock> clang doesn't appear to provide it's own quadmath header at least in their ubuntu packages. One workaround is pointing clang at gcc's header in /usr/lib/gcc/x86_64-linux-gnu/7/include/ since it all goes to libquadmath0's shared library.  
  
We have absolutely zero control over the command line - the libraries are "just a bunch of headers" and we don't control how they are used. So if clang is invoked in a way which does not find quadmath.h then there's not much else we can do (except generate a hard error when a header the user may care less about is not found).  
  
After much mucking about with user-config.jam, informed by this and previous long discussions  
  
  http://boost.2283326.n4.nabble.com/LLVM-bitcode-for-Boost-libraries-td4706369.html#a4706453  
  
I can now compile, link and run examples of boost code using Clang on Windows clang-win using clang-cl.  
  
(it is most disappointing that after over a decade of development, a space in a filename still causes so much trouble ☹ )  
  
Emboldened by https://reviews.llvm.org/D15120 “Add support for __float128 type to be used by targets that support it”  
  
This patch adds support for the __float128 keyword and literals for IEEE Quad Precision.  
  
I have tried to use float128.  
  
Since (as noted above), Clang does not package quadmath I have either included GCC’s quadmath.h,  
  
C:\Program Files\mingw-w64\x86_64-8.1.0-win32-seh-rt_v6-rev0\mingw64\lib\gcc\x86_64-w64-mingw32\8.1.0\include\quadmath.h  
  
or, in some desperation, naughtily copied into the Clang include folder, both the same (ill-)effect.  
  
“C:\LLVM\clang-800\LLVM\lib\clang\8.0.0\include\quadmath.h"  
  
Trying a hello_world-ish example of float128 I hit a roadblock at this error  
  
“  
  
In file included from hello_boost_float128.cpp:10:  
  
In file included from ..\..\..\boost/multiprecision/float128.hpp:9:  
  
In file included from ..\..\..\boost/config.hpp:44:  
  
In file included from ..\..\..\boost/config/detail/select_stdlib_config.hpp:18:  
  
In file included from C:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\VC\Tools\MSVC\14.16.27023\include\cstddef:7:  
  
C:\Program Files\mingw-w64\x86_64-8.1.0-win32-seh-rt_v6-rev0\mingw64\lib\gcc\x86_64-w64-mingw32\8.1.0\include\stddef.h(438):  error: typedef redefinition with different types  
  
('struct max_align_t' vs 'double')  
  
C:\LLVM\clang-800\LLVM\lib\clang\8.0.0\include/__stddef_max_align_t.h(30):  note: previous definition is here  
  
In file included from hello_boost_float128.cpp:10:  
  
“  
  
I will presumably hit a similar roadblock at the link stage?  
  
Does anyone understand what I am doing wrong, or if I am trying something impossible?  
  
Paul

---

## Comment 17

> Username: bgemmill  
> Created at: 2019-02-19 18:38:39 UTC  
> Url: https://github.com/boostorg/math/issues/181#issuecomment-465256799  

@pabristow caveat: I'm not terribly familiar with mingw, but I'll hazard a guess at what's going on. On ubuntu, I can point clang at gcc's quadmath.h without issue because they're both using gcc's libstdc++, so all of the other typedefs and includes are the same, and I don't run into the issue that you're seeing.   
  
On ubuntu, clang gets includes from:  
`/usr/include/c++/7.3.0/`  
gcc from:  
`/usr/lib/gcc/x86_64-linux-gnu/7.3.0/`  
  
Aside from additional library headers like quadmath.h, they look identical. Without being too familiar with mingw, I'd imagine that the equivalent two you have are different. I might run into the same sorts of oddities if I were using clang's libc++ instead of gcc's libstdc++, and tried to use quadmath.h.  
  
To make this go, you might want to try to get clang to use mingw's libstdc++ or edit a copy of quadmath.h to only include files from clang. Both approaches would force just one standard library and get rid of the double definitions.  
  
I'd be curious if either or both work for you since it could be a "quadmath.h only available with libstdc++" issue.

---

## Comment 18

> Username: ckormanyos  
> Created at: 2019-02-19 21:32:41 UTC  
> Url: https://github.com/boostorg/math/issues/181#issuecomment-465319076  

The presence of __float128 in GCC can sometimes depend on whether or not GCC was actually built with support of libquadmath. An investigation of "g++ -v" can give hints if the compiler was built with --enable-libquadmath or --disable-libquadmath. These build flags are, however, not always present because some GCC targets have libquadmath enabled by default while some other GCC targets have libquadmath disabled by default. You can perform a preliminary investigation of your MinGW GCC build by examining the result of "g++ -v".

---

## Comment 19

> Username: pabristow  
> Created at: 2019-02-22 12:43:00 UTC  
> Url: https://github.com/boostorg/math/issues/181#issuecomment-466384068  

Sorry for delay - rain got into my internet connection :-(  
  
"g++ -v" is useful to be reminded, and I have checked that B2 builds and runs GCC 7.2.0. and 8.1.0 for simple float128 programs so I am confident that the quadmath library is present.  
  
I remain confused by the multiple versions of some files  
  
But this file exists in /bin  
  
"C:\Program Files\mingw-w64\x86_64-8.1.0-win32-seh-rt_v6-rev0\mingw64\bin\libquadmath-0.dll" 327 KB  
  
which is a copy (using Code compare diff function) of  
  
"C:\Program Files\mingw-w64\x86_64-8.1.0-win32-seh-rt_v6-rev0\mingw64\x86_64-w64-mingw32\lib\libquadmath-0.dll" 327 kb  
  
and this header file quadmath.h  
  
"C:\Program Files\mingw-w64\x86_64-8.1.0-win32-seh-rt_v6-rev0\mingw64\lib\gcc\x86_64-w64-mingw32\8.1.0\include\quadmath.h"  
  
and also a 'weak' version whose function is obscure but probably not relevant to this problem???  
"C:\Program Files\mingw-w64\x86_64-8.1.0-win32-seh-rt_v6-rev0\mingw64\lib\gcc\x86_64-w64-mingw32\8.1.0\include\quadmath_weak.h"  
  
But perhaps I should just accept this as working on an 'emulation' of *nix?  
  
I will not look at Ben's promising suggestion on quadmath.h

---

## Comment 20

> Username: pabristow  
> Created at: 2019-02-22 15:13:02 UTC  
> Url: https://github.com/boostorg/math/issues/181#issuecomment-466430307  

I have now got some Boost math examples working with Clang-win, now using Clang 8.0.0  
  
quadmath is now 'supported' in that this will is present (so mingw need/must not be involved and the previous stdlib type clash is gone).  
  
"C:\LLVM\clang-800\LLVM\lib\clang\8.0.0\include\quadmath.h" is provided,  
  
 but sadly it would seem not 'supported' yet :-(  
  
Nor is there a libquadmath-o.dll or similar.  
  
In file included from hello_boost_float128.cpp:10:  
In file included from ..\..\..\boost/multiprecision/float128.hpp:47:  
C:\LLVM\clang-800\LLVM\lib\clang\8.0.0\include\quadmath.h(33):  error: unsupported machine mode 'TC'  
C:\LLVM\clang-800\LLVM\lib\clang\8.0.0\include\quadmath.h(47):  error: __float128 is not supported on this target  
  
I'll try version Clang 9, and investigate further.

---

## Comment 21

> Username: pabristow  
> Created at: 2019-02-26 17:44:40 UTC  
> Url: https://github.com/boostorg/math/issues/181#issuecomment-467539751  

Clang 9 does not provide quadmath.h either.  
  
Making quadmath.h from mingw visible via including  
  
<include>"C:/Program Files/mingw-w64/x86_64-8.1.0-win32-seh-rt_v6-rev0/mingw64/lib/gcc/x86_64-w64-mingw32/8.1.0/include"  
  
 or copying  quadmath.h into clang include tree  
  
doesn't work, so I conclude that some of the Clangers will have to sort it out.  
  
I'll try to raise a plea/bug for this on the LLVM site.  Disappointing as all the other math examples and test work OK.

---

## Comment 22

> Username: pabristow  
> Created at: 2019-02-27 12:14:47 UTC  
> Url: https://github.com/boostorg/math/issues/181#issuecomment-467839950  

From: Nick [mailto:notifications@github.com]  
Sent: 08 February 2019 19:52  
To: boostorg/math  
Cc: Paul A. Bristow; Mention  
Subject: Re: [boostorg/math] float128 support for clang (#181)  
  
Would it be sensible to submit a bug against clang? Presumably quadmath.h is licensed in a compatible way with clang, so clang would only need to vendorize it.  
  
I’ve patch so that the config now allows __float128 with clang >7, and that an include is provided to make quadmath.h visible) but hit this:  
In file included from hello_boost_float128.cpp:10:  
In file included from ..\..\..\boost/multiprecision/float128.hpp:47:  
C:\LLVM\clang-800\LLVM\lib\clang\8.0.0\include\quadmath.h(33):  error: unsupported machine mode 'TC'  
C:\LLVM\clang-800\LLVM\lib\clang\8.0.0\include\quadmath.h(47):  error: __float128 is not supported on this target  
  
After some further investigation I have raised a bug report  
  
__float_128 clang-cl on Windows 10 x64 using Mingw64 / GCC 8.1 to enable 128-bit float Boost.Multiprecision and Boost.Math fails to compile  
  
https://bugs.llvm.org/show_bug.cgi?id=40879  
  
I’ll tell you about any progress.  
  
Paul

---

## Comment 23

> Username: pabristow  
> Created at: 2020-03-10 12:33:50 UTC  
> Url: https://github.com/boostorg/math/issues/181#issuecomment-597061742  

Closed, confirming that float128 is NOT supported on Clang.  Will reopen if this enhancement  becomes available.

---

## Comment 24

> Username: msimberg  
> Created at: 2021-03-08 13:45:50 UTC  
> Url: https://github.com/boostorg/math/issues/181#issuecomment-792766476  

Unsure if I should open a separate issue (I may have misconfigured something), so please let me know if I should do that.  
  
I'm hitting one of the issues mentioned above with the new Cray compiler based on clang compiling for GPU, i.e. this:  
```  
<path-to-boost>/include/boost/config/detail/suffix.hpp:510:26: error: __float128 is not supported on this target  
   __extension__ typedef __float128 float128_type;  
                         ^  
1 error generated when compiling for sm_60.  
```  
This is with the system GCC headers (GCC 8.1.0) (and reproduces with Boost 1.70/73/75; have not tested others). The test program is simply  
```  
#include <boost/config.hpp>  
int main() {}  
```  
compiled using `CC -I<path-to-boost>/include "--cuda-gpu-arch=sm_60" main.cu`. A `cpp` file compiled for the host compiles ok.  
  
The compiler identifies itself as  
```  
Cray clang version 10.0.2 (df7eba9cfe923bfcce662b26f97beaf9f806187a)  
Target: x86_64-unknown-linux-gnu  
Thread model: posix  
InstalledDir: /opt/cray/pe/cce/10.0.2/cce-clang/x86_64/share/../bin  
```  
I'm unsure if this is a bug in the Cray compiler, or something else. Curiously it doesn't seem to set the `_CRAYC` macro. Setting it manually leads to other problems (since the version macros also need to be reported).  
  
I think a compilation test wouldn't even be enough in this case since there's a difference between compiling for host and device code.  
  
Easiest (on our end) would be to have a way to disable 128-bit types explicitly, but you may have better ideas on what's going on here.

---

## Comment 25

> Username: bgemmill  
> Created at: 2021-03-08 14:56:53 UTC  
> Url: https://github.com/boostorg/math/issues/181#issuecomment-792810561  

@msimberg: Looking at `/include/boost/math/tools/config.hpp`, does defining BOOST_MATH_DISABLE_FLOAT128 work for you?

---

## Comment 26

> Username: msimberg  
> Created at: 2021-03-08 15:07:48 UTC  
> Url: https://github.com/boostorg/math/issues/181#issuecomment-792817648  

> @msimberg: Looking at `/include/boost/math/tools/config.hpp`, does defining BOOST_MATH_DISABLE_FLOAT128 work for you?  
  
Good question. It does not, and with that question I realize my question might be misplaced here since the error is actually coming from `boost/config` and not from `boost/math`. I arrived here through https://github.com/boostorg/config/pull/261. Shall I move my question to `config` instead?

---

## Comment 27

> Username: bgemmill  
> Created at: 2021-03-08 15:52:42 UTC  
> Url: https://github.com/boostorg/math/issues/181#issuecomment-792848034  

boost/config may be the better place; it was mentioned here that @jzmaddock does exhaustive testing of compilers and versions. What you mentioned about `_CRAYC` sounds like something isn't being detected properly.  
  
If you're not using it, a workaround in the mean time may be removing libquadmath from that system so that the check `__has_include(<quadmath.h>)` fails.

---

## Comment 28

> Username: pabristow  
> Created at: 2021-03-08 16:58:01 UTC  
> Url: https://github.com/boostorg/math/issues/181#issuecomment-792900104  

This is entirely plausible on an eclectic combination like this.  
  
John Maddock will need to have the output from a configuration test for exact combination of compiler, platform, hardware, standard version ...   Only the user an provide this - @jzmaddock has an impressive but limited set of kit.  
  
But meanwhile using the macro  BOOST_MATH_DISABLE_FLOAT128 provided for just this eventually should be your first (and simplest) step.  And report back.

---

## Comment 29

> Username: ckormanyos  
> Created at: 2021-03-08 17:08:35 UTC  
> Updated at: 2021-03-08 17:09:44 UTC  
> Url: https://github.com/boostorg/math/issues/181#issuecomment-792908267  

> John Maddock will need to have the output from a configuration test for exact combination of compiler, platform, hardware, standard version ... Only the user an provide this  
  
Yes. It would be great to get some real data _from the wild_ because it seems like test experience on Cray is hard to obtain.  
  
Pondering... This post got me wondering if this system has native `double` or `long double` (or both) being equivalent to `float128_t`? And furthermore how best to deal with that if or when non-libquadmath 128-bit floar becomes more and more common. And then ho, or if, to differentiate it from multiprecision's float128_t?

---

## Comment 30

> Username: jzmaddock  
> Created at: 2021-03-08 17:15:59 UTC  
> Updated at: 2021-03-08 17:16:37 UTC  
> Url: https://github.com/boostorg/math/issues/181#issuecomment-792915154  

>But meanwhile using the macro BOOST_MATH_DISABLE_FLOAT128 provided for just this eventually should be your first (and simplest) step  
  
Sorry no, this is purely a Boost.Config issue, nothing to do with this library.  
  
I'm assuming that clang is being correctly detected as the compiler, and that BOOST_HAS_FLOAT128 is being set here: https://github.com/boostorg/config/blob/b0f2951791c5b51608c05c6aeaeccde2771127e4/include/boost/config/compiler/clang.hpp#L63  In which case I assume that `_CRAYC` is not set?  We would need to know what macro *does* identify this platform.  I wonder if `__CUDA__` is set?  
  
In the mean time, you may be able to fix your local configuration via the configure script: https://www.boost.org/doc/libs/1_75_0/libs/config/doc/html/index.html#boost_config.configuring_boost_for_your_platform.using_the_configure_script

---

## Comment 31

> Username: jzmaddock  
> Created at: 2021-03-08 17:19:52 UTC  
> Url: https://github.com/boostorg/math/issues/181#issuecomment-792918170  

>Pondering... This post got me wondering if this system has native double or long double (or both) being equivalent to float128_t? And furthermore how best to deal with that if or when non-libquadmath 128-bit floar becomes more and more common. And then ho, or if, to differentiate it from multiprecision's float128_t?  
  
We appear to be talking about CUDA here - so there are only 32 and 64-bit floats, and rather limited C++ support all round, the situation is probably no different from regular CUDA on x86, it's just that the host machine and compiler are out of the ordinary (for us anyway).

---

## Comment 32

> Username: msimberg  
> Created at: 2021-03-09 09:56:08 UTC  
> Url: https://github.com/boostorg/math/issues/181#issuecomment-793645528  

Thanks everyone for the replies!  
  
> If you're not using it, a workaround in the mean time may be removing libquadmath from that system so that the check `__has_include(<quadmath.h>)` fails.  
  
I unfortunately do not have control over that (i.e. no permissions to do that).  
  
> But meanwhile using the macro BOOST_MATH_DISABLE_FLOAT128 provided for just this eventually should be your first (and simplest) step. And report back.  
  
That does not work in this case, as it's boost config which detects that it should have float128 support, not boost math.  
  
> I'm assuming that clang is being correctly detected as the compiler, and that BOOST_HAS_FLOAT128 is being set here: https://github.com/boostorg/config/blob/b0f2951791c5b51608c05c6aeaeccde2771127e4/include/boost/config/compiler/clang.hpp#L63 In which case I assume that `_CRAYC` is not set? We would need to know what macro _does_ identify this platform. I wonder if `__CUDA__` is set?  
  
Indeed, that's exactly what it hits. I dumped a list of defines with `CC -dM -E` here (for both host and device mode, i.e. compiling a cpp vs cu file): https://gist.github.com/msimberg/478395ebaf3a124df89527268b816f26. `__CUDA__` is set. Interestingly in both host and device mode the following is set: `_GLIBCXX_USE_FLOAT128 1` and `__HAVE_FLOAT128 0`. `__cray__` is set in both cases as well. Let me know if I can provide any further information from the system.  
  
> In the mean time, you may be able to fix your local configuration via the configure script: https://www.boost.org/doc/libs/1_75_0/libs/config/doc/html/index.html#boost_config.configuring_boost_for_your_platform.using_the_configure_script  
  
Thanks, I didn't know that existed! I gave it a quick try without success. In this case I'd have to set `BOOST_NO_COMPILER_CONFIG` to not have `BOOST_HAS_FLOAT128` set automatically, since the user config is loaded first, right? It'd be handy to have a file that's loaded last in the configuration where I could just unset `BOOST_HAS_FLOAT128` but keep the rest. I'm sure I haven't quite set things up properly either so I'll have another look at this.

---

## Comment 33

> Username: msimberg  
> Created at: 2021-04-01 11:41:45 UTC  
> Url: https://github.com/boostorg/math/issues/181#issuecomment-811849499  

Just to follow up on this, I did get a successful build going in the end by concatenating the nvcc and clang compiler configurations, with `BOOST_HAS_FLOAT128` unconditionally disabled, and using that as the user configuration. That said, if there's anything I can do to help Boost detect this correctly in the first place please let me know.

---

## Comment 34

> Username: jzmaddock  
> Created at: 2021-04-01 16:02:32 UTC  
> Url: https://github.com/boostorg/math/issues/181#issuecomment-812008271  

Sorry, dropped the ball on this one, does this fix things: https://github.com/boostorg/config/pull/378
