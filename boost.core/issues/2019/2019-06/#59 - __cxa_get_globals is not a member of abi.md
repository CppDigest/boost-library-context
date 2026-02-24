# #59 - __cxa_get_globals is not a member of abi [Closed]

> Username: Johnnyxy  
> Created at: 2019-06-12 14:00:50 UTC  
> Updated at: 2019-06-18 11:34:01 UTC  
> Closed at: 2019-06-13 12:42:40 UTC  
> Url: https://github.com/boostorg/core/issues/59  

Hi there,  
when building Boost for QNX SDP 7.0 (QCC 5.4.0) I get an error in the ```uncaught_exceptions.hpp``` file.  
  
  
error  
---------------  
>In file included from ./boost/log/detail/format.hpp:26:0,  
                 from libs\log\src\format_parser.cpp:25:  
./boost/core/uncaught_exceptions.hpp: In function 'unsigned int boost::core::uncaught_exceptions()':  
./boost/core/uncaught_exceptions.hpp:109:66: error: '__cxa_get_globals' is not a member of 'abi'  
     std::memcpy(&count, reinterpret_cast< const unsigned char* >(::abi::__cxa_get_globals()) + sizeof(void*), sizeof(count)); // __cxa_eh_globals::uncaughtExceptions, x32 offset - 0x4, x64 - 0x8  
  
  
background  
----------------  
QNX 7.0 actually has a ```__cxa_get_globals``` function but does not expose it through ```cxxabi.h``` like some systems, according to the code docs:  
https://github.com/boostorg/core/blob/4f141646b5dbf523c368eb9a9a1d1a6a3a6bf45b/include/boost/core/uncaught_exceptions.hpp#L53  
  
In the QNX compiler ```cxxabi.h``` source it is defined at line 605 (see attachment).  
[cxxabi.h.txt](https://github.com/boostorg/core/files/3277597/cxxabi.h.txt)  
  
compile fix  
----------------  
I fixed compilation by patching the ```uncaught_exceptions.hpp``` at line 62, adding the QNX Neutrino macro ```__QNXNTO__``` (this is defined for QNX versions > 4).  
from:  
```c  
#if !defined(__FreeBSD__) && \  
    ( \  
        (defined(__GNUC__) && (__GNUC__ * 100 + __GNUC_MINOR__) < 407) || \  
        defined(__OpenBSD__) || \  
        defined(_LIBCPPABI_VERSION) \  
    )  
```  
  
to:  
```c  
#if !defined(__FreeBSD__) && \  
    ( \  
        (defined(__GNUC__) && (__GNUC__ * 100 + __GNUC_MINOR__) < 407) || \  
        defined(__OpenBSD__) || \  
        defined(__QNXNTO__) || \  
        defined(_LIBCPPABI_VERSION) \  
    )  
```  
  
I will test this a little bit more but until now this does the trick.

---

## Comment 1

> Username: Lastique  
> Created at: 2019-06-12 15:42:50 UTC  
> Url: https://github.com/boostorg/core/issues/59#issuecomment-501332630  

I'm not sure I understand. In the file you attached, `__cxa_get_globals` is defined, so it should be accessible. The preprocessor workaround in `uncaught_exceptions.hpp` is there for systems that *don't* declare `__cxa_get_globals` in `cxxabi.h`. Are you sure you're including the right `cxxabi.h` in your project?

---

## Comment 2

> Username: Johnnyxy  
> Created at: 2019-06-12 15:48:53 UTC  
> Url: https://github.com/boostorg/core/issues/59#issuecomment-501335331  

The problem is that even building Boost ```log``` does not work. My code does not even get compiled yet.  
  
It was odd for me too that ```__cxa_get_globals``` it is declared in the ```cxxabi.h``` but while building Boost ```log``` it still does get the mentioned error.

---

## Comment 3

> Username: Lastique  
> Created at: 2019-06-12 15:52:06 UTC  
> Url: https://github.com/boostorg/core/issues/59#issuecomment-501336682  

Try looking at the preprocessed output and track what headers are included. It is also possible that there is some sort of macro collision that causes `__cxa_get_globals` to disappear.  
  
You can see full command lines used to build Boost if you add `-d+2` to the `b2` command line. Use that command line for the failing file and modify it to produce preprocessed output.

---

## Comment 4

> Username: Johnnyxy  
> Created at: 2019-06-13 11:49:32 UTC  
> Url: https://github.com/boostorg/core/issues/59#issuecomment-501669474  

Hi,  
I did modify the compiler execution to only preprocess and output the preprocessed code to a file ```gcc-compiler-variant ... -E -P ... > preprocessed.cpp```.  
  
What I found was that ```cxxabi.h``` was actually included. This would mean that the code had to be included but it was not. Now after a little investigation I found out that there are two (for QNX SDP 7.0) in the future maybe more ```cxxabi.h``` variants (not only of this file, serval others too). These variants depend on the libcxx one links against.   
  
For QNX SDP 7.0 there are by default two C++ libraries available ([see docs](http://www.qnx.com/developers/docs/7.0.0/#com.qnx.doc.neutrino.utilities/topic/q/qcc.html)) "GNU C++ library" (QNX abbr. name: gpp) and "LLVM C++ library" (QNX abbr. name: cxx) which is the default.  
The GNU C++ library comes with a ```cxxabi.h``` that defines the ```__cxa_get_globals``` as one can [see here](https://github.com/boostorg/core/files/3277597/cxxabi.h.txt) while the LLVM C++ library does not (see attachment).  
[QNX SDP 7.0 cxx cxxabi.h.txt](https://github.com/boostorg/core/files/3285768/QNX.SDP.7.0.cxx.cxxabi.h.txt)  
  
I did a text search for ```__cxa_get_globals``` and it is not provided in any other file except for the GNU C++ library variant.  
  
conclusion  
-----------  
This means only for the LLVM library boost needs to provide its own definition of ```__cxa_get_globals```, maybe additionally with others that are needed.  
At the moment I do not know if it is possible to detect the library by some macro as the include path is already set by the QNX "compiler" which is a GCC wrapper with target specific parameters.

---

## Comment 5

> Username: Lastique  
> Created at: 2019-06-13 12:07:54 UTC  
> Url: https://github.com/boostorg/core/issues/59#issuecomment-501674860  

Right, and they commented `_LIBCPPABI_VERSION` define just to make life more fun to the developers. Oh, joy. You should really report this bug to QNX developers.

---

## Comment 6

> Username: Johnnyxy  
> Created at: 2019-06-13 12:34:16 UTC  
> Url: https://github.com/boostorg/core/issues/59#issuecomment-501683304  

For the LLVM variant there is a ```__config``` header file. This header file only exists in the LLVM variant.  
Also there is a macro ```#define _LIBCPP_VERSION 3700``` as well as ```#define _LIBCPP_ABI_VERSION 1``` (see attachment).  
[__config.txt](https://github.com/boostorg/core/files/3286002/__config.txt)  
  
I will try to do a workaround to detect if the ```__cxa_get_globals``` has to be included by Boost.

---

## Comment 7

> Username: Lastique  
> Created at: 2019-06-13 12:43:59 UTC  
> Url: https://github.com/boostorg/core/issues/59#issuecomment-501686607  

Thank you for your investigation, I've committed 420dff80b5f67287dda43de6b2291bf6cde067e7 as a tentative fix. Could you verify if it works?

---

## Comment 8

> Username: Lastique  
> Created at: 2019-06-13 12:45:54 UTC  
> Url: https://github.com/boostorg/core/issues/59#issuecomment-501687262  

Regarding `__config`, I don't see it being included by LLVM's `cxxabi.h`. Can you confirm it is being included indirectly?

---

## Comment 9

> Username: Lastique  
> Created at: 2019-06-13 12:49:35 UTC  
> Url: https://github.com/boostorg/core/issues/59#issuecomment-501688493  

Although, nevermind. `__config` is part of libc++, not libc++abi, which is considered a separate library. libc++ can be used with glibcxx, so it can't be used to detect libc++abi.

---

## Comment 10

> Username: Johnnyxy  
> Created at: 2019-06-13 12:56:12 UTC  
> Updated at: 2019-06-13 12:57:57 UTC  
> Url: https://github.com/boostorg/core/issues/59#issuecomment-501690856  

The output of the preprocessed code shows that the ```__config``` has been included serval times.  
So to answer your question, yes the ```__config``` gets included directly.  
```c++  
# 1 "./boost/config/compiler/gcc.hpp" 1  
# 165 "./boost/config/compiler/gcc.hpp"  
# 1 "c:\\qnx700\\target\\qnx7\\usr\\include\\c++\\v1\\cstddef" 1 3 4  
# 36 "c:\\qnx700\\target\\qnx7\\usr\\include\\c++\\v1\\cstddef" 3 4  
# 1 "c:\\qnx700\\target\\qnx7\\usr\\include\\c++\\v1\\__config" 1 3 4  
# 15 "c:\\qnx700\\target\\qnx7\\usr\\include\\c++\\v1\\__config" 3 4  
         
# 16 "c:\\qnx700\\target\\qnx7\\usr\\include\\c++\\v1\\__config" 3  
# 25 "c:\\qnx700\\target\\qnx7\\usr\\include\\c++\\v1\\__config" 3  
# 1 "c:\\qnx700\\target\\qnx7\\usr\\include\\unistd.h" 1 3 4  
# 27 "c:\\qnx700\\target\\qnx7\\usr\\include\\unistd.h" 3 4  
# 1 "c:\\qnx700\\target\\qnx7\\usr\\include\\confname.h" 1 3 4  
# 28 "c:\\qnx700\\target\\qnx7\\usr\\include\\unistd.h" 2 3 4  
```  
  
additionally  
------------  
I just did a quick search and it gets included by all ```std header files``` as first include command ```#include <__config>```.  
- array  
- atomic  
- bitset  
- cassert  
- cctype  
- cerrno  
- cfenv  
- cfloat  
- chrono  
- cinttypes  
- ...  
  
  
  
EDIT:  
I will try the fix tomorrow.

---

## Comment 11

> Username: Johnnyxy  
> Created at: 2019-06-18 07:55:35 UTC  
> Url: https://github.com/boostorg/core/issues/59#issuecomment-502991582  

@Lastique   
I used your proposal an added some additional checks to it. With this change it works for QNX SDP 6.5 (GNU C++), QNX SDP 7.0 (GNU C++; LLVM C++).  
  
The background for those checks is that only with the LLVM backend it is necessary to declare ```__cxa_get_globals```. As I know the LLVM backend is integrated since QNX SDP 7.0 that wraps the execution of GCC 5.4. The next older version of QNX SDP is 6.6 and that shipped with [GCC 4.7](http://www.qnx.com/developers/articles/rel_5849_7.html#new_os_sdk) and had no LLVM included. So the check has to ensure that the Boost declaration of ```__cxa_get_globals``` gets only used when using QNX SDP 7.0 or higher.   
As 7.0 it is the current version at the moment and the version (4.4, 4.7, 5.4.) steps of the used GCC shipped with each QNX SDP version (6.5, 6.6, 7.0) can be larger, in the next QNX SDP release this "bug" could be resolved already. Thus my proposed solution is to check the QNX version if it is 7 and if ```_LIBCPP_VERSION``` is ```3700``` (as currently declared in the header files) or lower. If the next QNX SDP still needs this declaration we can increase the version numbers.  
  
Have a look at the patch file in the attachment:  
[boost_core_qnx70_cxx_provide___cxa_get_globals.patch.txt](https://github.com/boostorg/core/files/3300380/boost_core_qnx70_cxx_provide___cxa_get_globals.patch.txt)

---

## Comment 12

> Username: Lastique  
> Created at: 2019-06-18 09:31:14 UTC  
> Url: https://github.com/boostorg/core/issues/59#issuecomment-503026199  

Thanks.  
  
I don't think checking for libc++ version is correct. As I said earlier, it is not tied to libc++abi, so the latter may get updated without the former and vice versa.  
  
I will add an open check for `_NTO_VERSION` though. If a future QNX version is known to fix libc++abi so that it defines `_LIBCPPABI_VERSION` as it normally should, that check can be updated and converted to a closed one. But it won't hurt if we don't. To my knowledge, no version of libc++abi declares `__cxa_get_globals`, I don't think this is going to change.

---

## Comment 13

> Username: Lastique  
> Created at: 2019-06-18 09:46:30 UTC  
> Url: https://github.com/boostorg/core/issues/59#issuecomment-503031934  

On the second thought, do we have to check for `_NTO_VERSION`? If older QNX versions don't ship libc++abi, the existing checks for `__GLIBCXX__` and `__GLIBCPP__` should be working. Can you verify that my original checks in commit https://github.com/boostorg/core/commit/420dff80b5f67287dda43de6b2291bf6cde067e7 do work in all cases?

---

## Comment 14

> Username: Johnnyxy  
> Created at: 2019-06-18 11:28:08 UTC  
> Url: https://github.com/boostorg/core/issues/59#issuecomment-503063601  

I tested the tentative fix with QNX SDP 6.5 (GNU C++, Dinkum C++), QNX SDP 7.0 (GNU C++, LLVM C++). All variants ```compiled successfully```.  
  
All in all it worked.  
  
Additionally  
------------  
If I may: The comment you put into the fix is not 100% correct.  
>On QNX SDP 7.0 (QCC 5.4.0), there are multiple cxxabi.h, one from glibcxx from gcc and another from libc++abi from LLVM. The latter is included first by qcc.  
  
As the different variants of the header files are not included in sequence or in any order at all.  
The QNX SDP compiler wrapper invokes a GCC variant for a specific target depending on command line arguments. In this call the GCC frontend gets passed the library to use (GNU C++ or LLVM C++). Thus only one set of files are included specific for the target and library.  
  
```q++ -Vgcc_ntoaarch64le_cxx ...``` (compile for arch aarch64le with LLVM C++ library)  
will execute something like that  
```ntoaarch64-gcc.exe -Ic:/.../LLVM_INCLUDE_PATH```  
  
```q++ -Vgcc_ntoaarch64le_gpp ...``` (compile for arch aarch64le with GNU C++ library)  
will execute something like that  
```ntoaarch64-gcc.exe -Ic:/.../GNU_INCLUDE_PATH```  
  
I would change the comment you made in the fix to the following to clarify this:  
>// On QNX SDP 7.0 (QCC 5.4.0), there are multiple cxxabi.h, one from glibcxx from gcc and another from libc++abi from LLVM. Which one is included will be determined by the qcc  
// command line arguments (-V and/or -Y; http://www.qnx.com/developers/docs/7.0.0/#com.qnx.doc.neutrino.utilities/topic/q/qcc.html). The LLVM libc++abi is missing the declaration  
// of __cxa_get_globals but it is also patched by QNX developers to not define _LIBCPPABI_VERSION. https://github.com/boostorg/core/issues/59  
  
The tested patch code I have been using is (including your fix and my modified comments):  
[boost_core_qnx70_cxx_provide___cxa_get_globals.patch.txt](https://github.com/boostorg/core/files/3301212/boost_core_qnx70_cxx_provide___cxa_get_globals.patch.txt)

---

## Comment 15

> Username: Lastique  
> Created at: 2019-06-18 11:34:01 UTC  
> Url: https://github.com/boostorg/core/issues/59#issuecomment-503065471  

Thanks, I already updated the comment in 7e1d02895803e11500730764c8601a7d8bdb5b5d.
