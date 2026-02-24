# #216 - Problem with Embarcadero clang-based compilers [Closed]

> Username: eldiener  
> Created at: 2020-04-09 23:31:12 UTC  
> Updated at: 2020-12-26 19:16:12 UTC  
> Closed at: 2020-12-26 16:52:50 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/216  

I am seeing this. Source code is my own to illustrate the problem and I call it test_cpp_int.cpp:  
  
```  
#include <boost/multiprecision/cpp_int.hpp>  
#include <boost/core/lightweight_test.hpp>  
int main()  
{  
   auto i2 = static_cast<boost::multiprecision::cpp_int>(1);  
   auto i3 = static_cast<boost::multiprecision::cpp_int>(1);  
   BOOST_TEST_EQ( i2, i3 );  
   return boost::report_errors();  
}  
```  
When building the code with other compilers everything is fine. When building the code with the Embarcadero clang-based compiler bcc64 I am getting this link error:  
  
Error: Unresolved external '__udivti3' referenced from C:\PROGRAMMING\BUILD\MODULAR-BOOST\BOOST\BIN.V2\LIBS\TEST_LOCAL\TEST\TEST_CPP_INT.TEST\EMBARCADERO-7.40\DEBUG\THREADING-MULTI\VISIBILITY-HIDDEN\TEST_CPP_INT.O  
Error: Unresolved external '__umodti3' referenced from C:\PROGRAMMING\BUILD\MODULAR-BOOST\BOOST\BIN.V2\LIBS\TEST_LOCAL\TEST\TEST_CPP_INT.TEST\EMBARCADERO-7.40\DEBUG\THREADING-MULTI\VISIBILITY-HIDDEN\TEST_CPP_INT.O  
  
Do you have any idea where the _udivti3 and _umodti3 might be coming from ? Is there a backend for multiprecision that might be causing this error ? I can only find _udivti3 and _umodti3  as related to gcc. Embarcardero is using clang-5.0 as its "compiler" on Windows but no gcc macro is predefined, only __clang__ ( and boost_clang from config.hpp ), as well as __BORLANDC__, __CODEGEARC__, and BOOST_EMBTC from config.hpp.

---

## Comment 1

> Username: ckormanyos  
> Created at: 2020-04-10 06:08:16 UTC  
> Updated at: 2020-04-10 06:11:16 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/216#issuecomment-611894049  

> Do you have any idea where the _udivti3 and _umodti3 might be coming from ? Is there a backend for multiprecision that might be causing this error ? I can only find _udivti3 and _umodti3 as related to gcc.   
  
They are GCC related. `cpp_int` is designed to be fully compiled and linked as header-only.  
  
The functions you mention are compiler-internal assembly routines intended to perform integer primitive unsigned operations division and modulus operations with three arguments.  
  
We usually see this kind of error when the linker is not able to include the compiler-internal assembly routines because they are either missing in the C library or some other library needs to be included. They might be found in libgcc.a, libc.a, (or possibly liba.a, or even in libm.a). Evidently the core being used as target is missing compiler-specific assembly routines that the compiler is generating calls to itself.  
  
This is indeed unusual, as you are probably not controlling the linker libraries yourself manually (which can be done). My mind wanders... Is it a new compiler port? Is the use case using 128 bit (sort of new stuff) or something that could potentially be not yet well-tested or only partially deployed?

---

## Comment 2

> Username: ckormanyos  
> Created at: 2020-04-10 06:12:35 UTC  
> Updated at: 2020-04-10 06:13:42 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/216#issuecomment-611895184  

> They are GCC related.  
  
Oh, I forgot to metion, if memory serves, the clang front end is might be relying on GCC backend libs so, counterintuitive as it may seem, it makes sense to find GCC related stuff when searching for clang issues in linker areas. At least that's how clang was years ago when it got started. Don't use it as extensively now as I did then.

---

## Comment 3

> Username: eldiener  
> Created at: 2020-04-10 11:15:41 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/216#issuecomment-611988280  

My challenge is to create a self-enclosed example which I can report to Embarcadero as a compiler/linker bug. It would be hard, I believe, to include the entire code comprising cpp_int.hpp even though I could include the relevant lightweight test code. But maybe including cpp_int.hpp is viable. I hate to produce a huge listing as a bug report to Embarcadero and I also want to try to avoid telling them to get the latest Boost source on the 'develop' branch where I am testing this in order to see the bug.  
  
Embarcadero is Borland/CodeGear from the old days and their current clang-based compilers are meant as a replacement for the old Borland C++/bcc32 compiler which was never very C++ compliant and which at best implemented some small amount of C++11. The old Borland compiler has really not been able to compile Boost C++ libraries since around Boost 1.38, and even then had numerous weaknesses and workarounds in Boost code. It still exists, but I have no interest in it. I have been working to try compiling current Boost code with the current Embarcadero C++ clang-based compilers, which are based on clang-5.0, but which therefore supports C++ through C++17.. It does seem as if Embarcadero's tweaking of clang-5.0 left something out if the linker can not find the compiler-internal assembly routines anywhere. The linker is Embarcadero's own linker, but maybe there is some linker library path which is undocumented or just has been left out of the Embarcadero C++ Builder Community Edition which i am using.  Thanks for the information on where those routines come from !

---

## Comment 4

> Username: jzmaddock  
> Created at: 2020-04-10 11:25:09 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/216#issuecomment-611990716  

Since these appear to be 64-bit arithmetic routines, what happens with:  
  
```  
std::uint64 i(2), j(1), k;  
k = i / j;  
```  
  
If that's not it, try again with `__int128`.  
  
PS, if these really are 64-bit routines, how come clang is calling the external routines, and not generating 64-bit assembly?

---

## Comment 5

> Username: jzmaddock  
> Created at: 2020-04-10 11:26:30 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/216#issuecomment-611991084  

PPS, I was going to download the compiler to try, but I am *not* giving them my email and phone number and consenting to marketing calls.... just so you know ;)

---

## Comment 6

> Username: eldiener  
> Created at: 2020-04-10 11:51:20 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/216#issuecomment-611997502  

I do not remember "consenting to marketing calls", or even giving them my phone number. Make up a phone number, how can they know <g>. I have gotten alot of marketing e-mails from Embarcadero, which now automatically go into my spam folder under Thunderbird. I have never gotten any phone calls from them, and I highly doubt you would even if you gave them a real phone number. I have been in e-mail contact with a few Embarcadero C++ honchos about my efforts and they have been helpful. I do understand your not wanting to give them any information, but I already get so much e-mail spam that another organization trying to bother me hardly matters.

---

## Comment 7

> Username: eldiener  
> Created at: 2020-04-10 11:54:23 UTC  
> Updated at: 2020-04-10 11:54:37 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/216#issuecomment-611998310  

I have no idea why their clang-based compiler is calling external routines. Maybe it is a leftover from their tweaking of clang-5.0 which they missed. Or maybe there is some library which they forgot to distribute, or some library directory path I am not setting during the link, although I am pretty sure I am setting the correct paths.

---

## Comment 8

> Username: eldiener  
> Created at: 2020-04-10 12:14:19 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/216#issuecomment-612003607  

Bravo John ! Using std::uint64_t from cstdint worked without error but when I tried __int128 the compiler accepted it but the linker gave:  
  
Error: Unresolved external '__divti3' referenced from C:\PROGRAMMING\BUILD\MODULAR-BOOST\BOOST\BIN.V2\LIBS\TEST_LOCAL\TEST\TEST_CPP_INT.TEST\EMBARCADERO-7.40\DEBUG\THREADING-MULTI\VISIBILITY-HIDDEN\TEST_CPP_INT.O  
  
So there is my bug report to Embarcadero.

---

## Comment 9

> Username: jzmaddock  
> Created at: 2020-04-11 08:13:20 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/216#issuecomment-612363620  

Looks like we need to change Boost.Config and not set BOOST_HAS_INT128, currently we have:  
  
```  
//  
// We disable this if the compiler is really nvcc with C++03 as it  
// doesn't actually support __int128 as of CUDA_VERSION=7500  
// even though it defines __SIZEOF_INT128__.  
// See https://svn.boost.org/trac/boost/ticket/10418  
//     https://svn.boost.org/trac/boost/ticket/11852  
// Only re-enable this for nvcc if you're absolutely sure  
// of the circumstances under which it's supported.  
// Similarly __SIZEOF_INT128__ is defined when targetting msvc  
// compatibility even though the required support functions are absent.  
//  
  
#if defined(__SIZEOF_INT128__) && !defined(BOOST_NVCC_CXX03) && !defined(_MSC_VER)  
#  define BOOST_HAS_INT128  
#endif  
```  
  
in clang.hpp which looks like it needs to exclude Embarcadero as well?  
  
Oh and the test case needs updating I guess...

---

## Comment 10

> Username: ckormanyos  
> Created at: 2020-04-11 08:53:43 UTC  
> Updated at: 2020-04-11 08:54:29 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/216#issuecomment-612370455  

> Looks like we need to change Boost.Config and not set BOOST_HAS_INT128  
  
John, are you sure? Does this assume that the compiler does not intend to support int128?  
  
I thought the issue here is that the compiler does intend to support int128, but the presence of the right link libraries is broken (even though the compiler actually does intend to support int128).  
  
If it gets configured out, then the compiler (or its installation as multilib) gets repaired, then HAS128 needs to be reactivated?  
  
Best regards, Chris

---

## Comment 11

> Username: ckormanyos  
> Created at: 2020-04-11 08:59:25 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/216#issuecomment-612371395  

> So there is my bug report to Embarcadero.  
  
I do not know who is responsible for it. The compiler seems like it is generating the right code, so the compiler might be ok. But the installed GCC libs that have the internal assembly routines are either missing or not being linked properly.  
  
I do not know if this is in the compiler distro or on the GCC backend topology.  
  
Kind regards, Chris

---

## Comment 12

> Username: jzmaddock  
> Created at: 2020-04-11 11:28:05 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/216#issuecomment-612398609  

>John, are you sure? Does this assume that the compiler does not intend to support int128?  
  
If they haven't shipped those libraries then I would assume the intention is not to support __int128.  clang in msvc compatibility mode has the same issue.

---

## Comment 13

> Username: ckormanyos  
> Created at: 2020-04-11 11:57:09 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/216#issuecomment-612403901  

>> John, are you sure? Does this assume that the compiler does not intend to support int128?  
  
> If they haven't shipped those libraries then I would assume the intention is not to support __int128. clang in msvc compatibility mode has the same issue.  
  
Yeah. I kind of caught myself a few minutes after I wrote my post. I guess it does not really matter if 128 support is _intended_ or not. It's broken, for whatever reason. All your points are correct.  
  
Sorry about the noise...

---

## Comment 14

> Username: eldiener  
> Created at: 2020-04-11 14:40:38 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/216#issuecomment-612436260  

>   
>   
> Looks like we need to change Boost.Config and not set BOOST_HAS_INT128, currently we have:  
>   
> ```  
> //  
> // We disable this if the compiler is really nvcc with C++03 as it  
> // doesn't actually support __int128 as of CUDA_VERSION=7500  
> // even though it defines __SIZEOF_INT128__.  
> // See https://svn.boost.org/trac/boost/ticket/10418  
> //     https://svn.boost.org/trac/boost/ticket/11852  
> // Only re-enable this for nvcc if you're absolutely sure  
> // of the circumstances under which it's supported.  
> // Similarly __SIZEOF_INT128__ is defined when targetting msvc  
> // compatibility even though the required support functions are absent.  
> //  
>   
> #if defined(__SIZEOF_INT128__) && !defined(BOOST_NVCC_CXX03) && !defined(_MSC_VER)  
> #  define BOOST_HAS_INT128  
> #endif  
> ```  
>   
> in clang.hpp which looks like it needs to exclude Embarcadero as well?  
>   
> Oh and the test case needs updating I guess...  
  
I can just undefine BOOST_HAS_INT128 in codegear.hpp.

---

## Comment 15

> Username: eldiener  
> Created at: 2020-04-11 14:45:57 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/216#issuecomment-612437322  

>   
>   
> > So there is my bug report to Embarcadero.  
>   
> I do not know who is responsible for it. The compiler seems like it is generating the right code, so the compiler might be ok. But the installed GCC libs that have the internal assembly routines are either missing or not being linked properly.  
>   
> I do not know if this is in the compiler distro or on the GCC backend topology.  
>   
> Kind regards, Chris  
  
The Embarcadero clang-based C++ compilers do not use gcc in any way. The whole issue is a bug which I reported to Embarcadero at https://quality.embarcadero.com/browse/RSP-28043.

---

## Comment 16

> Username: pabristow  
> Created at: 2020-04-11 16:08:08 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/216#issuecomment-612454258  

Clang using mingw-64 also does not (yet) support float_128 (which I presume uses internal assemble int 128-bit?).  All the 128-bit FP handling code code is available as when using GCC, but something needed to be done to make it usable. I've ask for this but nobody has taken on the task.

---

## Comment 17

> Username: eldiener  
> Created at: 2020-04-12 16:44:40 UTC  
> Updated at: 2020-04-12 16:45:17 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/216#issuecomment-612643602  

>   
>   
> Clang using mingw-64 also does not (yet) support float_128 (which I presume uses internal assemble int 128-bit?). All the 128-bit FP handling code code is available as when using GCC, but something needed to be done to make it usable. I've ask for this but nobody has taken on the task.  
  
I am seeing that BOOST_HAS_FLOAT128 is not defined in Boost config for clang-9.0 targeting mingw-64/gcc. Is not that what you want of Boost since it is not usable ? Making it usable is a task for clang not Boost. From my past interactions with clang developers regarding their Windows implementation you are not likely to get them to do very much for you as clang on Windows has always been neglected compared to their Linux implementation. But what has all this to do with the Embarcadero C++ clang-based compiler on Windows other than that both clang implementations are on Windows ? The Embarcadero C++ employees probably have very little to do with the people who put out clang for Windows.

---

## Comment 18

> Username: pabristow  
> Created at: 2020-04-13 11:08:14 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/216#issuecomment-612856364  

As you imply - not a lot.  
  
I had imagined that the quadmath code might include int128 handling code (as well as the fp128 assembler code) but a brief search suggests that this was a delusion.  
  
Clang (whatever the host) does not implement use of the quadmath 128-bit floating point software, unlike GCC.  This looks feasible but needs some work by Clang developers and so far they haven't considered it a priority.  It would need a bit of adjustment to make it for for Boost too, of course.  
  
Meanwhile to get 128-bit FP on Clang, one can use the slower Boost.Multiprecision cpp_bin_float_quad.

---

## Comment 19

> Username: jzmaddock  
> Created at: 2020-12-26 16:52:50 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/216#issuecomment-751374033  

Closing as this is a config issue - did we fix it there BTW?

---

## Comment 20

> Username: eldiener  
> Created at: 2020-12-26 18:38:32 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/216#issuecomment-751384219  

Yes, I fixed this for Embarcadero and reported it to them some time ago. Since telling their people that Boost 1.75 was fully setup for all Boost libraries for the Embarcadero C++ clang-based compilers, and encouraging them to support Boost in various ways, all has been silence from them. Furthermore they no longer offer a Community Edition for their latest releases of C++ Builder, 10.4 and 10.4.1, so the setup in config is strctly for the previous 10.3 release. I was happy to do what I did for them in Boost, but I am not going to pursue the compiler further if they can no longer offer free community editions for their latest releases to developers trying to help them out.

---

## Comment 21

> Username: jzmaddock  
> Created at: 2020-12-26 18:44:07 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/216#issuecomment-751384667  

Oh well :(

---

## Comment 22

> Username: eldiener  
> Created at: 2020-12-26 18:55:38 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/216#issuecomment-751385701  

What can I do ? When I contacted them they were enthusiastic about the changes I would make, since before that they just distributed their own copies of Boost based on certain Boost releases. They were also enthusiastic about bug reports I could make, based on Boost tests, about their compiler, and about the C++ standard compliance of their clang-based offerings. Perhaps when they realized the various problems their compiler had implementing the C++17 standard they became less enthusiastic <g>. After all their niche market still remains their IDE and visual programming environment. I still wish them good luck, having used C++ Builder some decade and a half ago in actual projects on which I worked, but their priorities are what they are and I have done the best that I could on my own time for them.

---

## Comment 23

> Username: jzmaddock  
> Created at: 2020-12-26 19:16:12 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/216#issuecomment-751387240  

Sigh.... I learnt to program with Turbo C++, though that was a while ago ;)  I never really used C++ builder much, though it had it's moments I guess.  Given that they're using clang, fixing up their bugs ought to be so much simpler than with their own compiler... but the lack of a community edition and widespread testing sort of kills it.  We'll just have to see if any bug reports come in.
