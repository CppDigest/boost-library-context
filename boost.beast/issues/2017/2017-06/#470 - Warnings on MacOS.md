# #470 - Warnings on MacOS [Closed]

> Username: rberlich  
> Created at: 2017-06-11 22:04:46 UTC  
> Updated at: 2019-02-25 21:35:57 UTC  
> Closed at: 2017-11-18 21:04:26 UTC  
> Url: https://github.com/boostorg/beast/issues/470  

Here are a few warnings I encounter when compiling Beast v53 (with a fix for read.ipp, as discussed in #468 ) on MacOS (Sierra, 10.12.5) with the latest Clang from Xcode (see below for version information).  
  
I am posting the warnings solely in the hope that they may be useful at some point, and this is NOT meant as a "call to action".   
  
From my point of view, the possible mix of two Boost versions seems to be the only "serious" problem.  
  
### Warnings:  
  
Many warnings of the type:  
```  
/opt/local/include/boost/asio/impl/connect.hpp:410:50: warning: unused typedef  
      'type_check' [-Wunused-local-typedef]  
      ComposedConnectHandler, handler, Iterator) type_check;  
  
/opt/local/include/boost/asio/impl/read_until.hpp:1129:55: warning: unused  
      typedef 'type_check' [-Wunused-local-typedef]  
  BOOST_ASIO_READ_HANDLER_CHECK(ReadHandler, handler) type_check;  
  
/opt/local/include/boost/asio/strand.hpp:185:69: warning: unused typedef  
      'type_check' [-Wunused-local-typedef]  
    BOOST_ASIO_COMPLETION_HANDLER_CHECK(CompletionHandler, handler) type_check;  
  
/opt/local/include/boost/asio/impl/write_at.hpp:806:57: warning: unused typedef  
      'type_check' [-Wunused-local-typedef]  
  BOOST_ASIO_WRITE_HANDLER_CHECK(WriteHandler, handler) type_check;  
```  
  
NOTE the "/opt/local/include/boost".  I do not quite understand, where this path comes from. It is not contained in the FindBoost.cmake which comes with my cmake, so it appears as if this path is somehow "known" to clang, i.e. is a "system path". However, you (Vinnie) explicitly set `set (Boost_NO_SYSTEM_PATHS ON)` in the main CMakeLists.txt file, and I am passing `-DBOOST_ROOT=/opt/boost` to cmake / FindBoost.cmake, as I use custom versions of Boost (1.62 at the moment). The next warning below seems to indicate, that it is found and the `-DBOOST_ROOT=/opt/boost` was successful.   
  
So could it be that the compilation procedure somehow mixes headers from two Boost-versions (one from a "system" path /opt/local, and the other from my custom location /opt/boost) ? This would be quite dangerous.  
  
 ```  
In file included from /opt/boost/include/boost/coroutine/standard_stack_allocator.hpp:21:  
/opt/boost/include/boost/coroutine/detail/config.hpp:17:4: warning:   
      "Boost.Coroutine is now deprecated. Please switch to Boost.Coroutine2. To  
      disable this warning message, define  
      BOOST_COROUTINE_NO_DEPRECATION_WARNING." [-W#warnings]  
#  warning                  "Boost.Coroutine is now deprecated. Please s...  
```  
  
You explicitly set `-DBOOST_COROUTINES_NO_DEPRECATION_WARNING=1` in the CMakeLists.txt, but, from the text in the warning should set `BOOST_COROUTINE_NO_DEPRECATION_WARNING` (without "s") instead. Note the `/opt/boost/`, which seems to indicate that my `-DBOOST_ROOT=/opt/boost` seems to have been successful.  
  
Here is another one, possibly affecting the tests:  
  
```  
Beast/test/zlib/zlib-1.2.8/inflate.c:1507:61: warning:   
      shifting a negative signed value is undefined [-Wshift-negative-value]  
    if (strm == Z_NULL || strm->state == Z_NULL) return -1L << 16;  
```  
  
Finally, I see the following warning, which I have seen being discussed in some ASIO list. This seems to be new with the current MacOS Sierra.  
```  
In file included from /opt/local/include/boost/asio/detail/fenced_block.hpp:24:  
/opt/local/include/boost/asio/detail/macos_fenced_block.hpp:51:5: warning:   
      'OSMemoryBarrier' is deprecated: first deprecated in macOS 10.12 - Use  
      std::atomic_thread_fence() from <atomic> instead  
      [-Wdeprecated-declarations]  
    OSMemoryBarrier();  
    ^  
/usr/include/libkern/OSAtomicDeprecated.h:749:9: note: 'OSMemoryBarrier' has  
      been explicitly marked deprecated here  
void    OSMemoryBarrier( void );  
```  
  
### Beast Version:   
53 (from version.hpp), with fixes to read.ipp / Download on June 11  
### Boost Version:  
1.62  
### Platform:   
```  
clang -v  
Apple LLVM version 8.1.0 (clang-802.0.42)  
Target: x86_64-apple-darwin16.6.0  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-11 22:18:14 UTC  
> Url: https://github.com/boostorg/beast/issues/470#issuecomment-307660698  

* Nothing I can do about the warnings, Boost.Asio needs to be updated. And why aren't they using Coroutines2? Hmm...  
  
There is a way to mark include paths on the compiler options in a way suggesting they are "system" include directories and to prevent them from creating warnings. In CMake this is using the **SYSTEM** keyword:  
```  
include_directories (SYSTEM ${Boost_INCLUDE_DIRS})  
```  
  
For gcc and clang its a variation of the `-I` switch (can't remember which).  
  
Yep, that mix-up with the macro was fixed in a more recent Boost:  
https://github.com/boostorg/coroutine/commit/c33f5fcc42cb28882ea6b98a9dc0572f3dc97468  
https://github.com/boostorg/coroutine/commits/master  
  
As for the multiple libraries, I don't know. I doubt it, but you never know.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-06-23 02:09:51 UTC  
> Url: https://github.com/boostorg/beast/issues/470#issuecomment-310551208  

>Beast/test/zlib/zlib-1.2.8/inflate.c:1507:61: warning: shifting a negative signed value is undefined [-Wshift-negative-value]  
  
This is just zlib being zib, its an ancient C library. I believe the warning is harmless.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-07-04 03:00:20 UTC  
> Url: https://github.com/boostorg/beast/issues/470#issuecomment-312769097  

Is there anything actionable here?

---

## Comment 4

> Username: octopus-prime  
> Created at: 2017-07-04 10:19:10 UTC  
> Url: https://github.com/boostorg/beast/issues/470#issuecomment-312841963  

```  
Beast/test/zlib/zlib-1.2.8/inflate.c:1507:61: warning:   
      shifting a negative signed value is undefined [-Wshift-negative-value]  
    if (strm == Z_NULL || strm->state == Z_NULL) return -1L << 16;  
```  
  
What about this:  
`  
    if (strm == Z_NULL || strm->state == Z_NULL) return -(1L << 16);  
`

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-07-04 13:53:07 UTC  
> Url: https://github.com/boostorg/beast/issues/470#issuecomment-312883835  

I rather not modify the zlib sources if I can help it, otherwise it will be harder to merge changes from the upstream repository. Users won't see this warning because its not part of `<beast/>` header files.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-07-12 21:35:50 UTC  
> Url: https://github.com/boostorg/beast/issues/470#issuecomment-314904247  

I am trying zlib-1.2.11 to see if the warning goes away

---

## Comment 7

> Username: vinniefalco  
> Created at: 2017-09-01 03:21:20 UTC  
> Url: https://github.com/boostorg/beast/issues/470#issuecomment-326477462  

I don't see these warnings anymore, has this been resolved?

---

## Comment 8

> Username: vinniefalco  
> Created at: 2017-11-18 21:04:26 UTC  
> Url: https://github.com/boostorg/beast/issues/470#issuecomment-345471079  

I don't see the warnings anymore so I will go ahead and close this. If the warnings come back please feel free to open a new issue, thanks!

---

## Comment 9

> Username: aidv  
> Created at: 2019-02-25 02:13:39 UTC  
> Updated at: 2019-02-25 02:14:45 UTC  
> Url: https://github.com/boostorg/beast/issues/470#issuecomment-466848024  

No warnings, but tons of errors.  
This is what I'm getting (sample error output):  
  
```  
Users/usr1/Desktop/Repos/some/thing/libs/beast/test/extern/zlib-1.2.11/contrib/masmx64/gvmat64.asm:1:25: error: unexpected token in memory operand  
;uInt longest_match_x64(  
                        ^  
/Users/usr1/Desktop/Repos/some/thing/libs/beast/test/extern/zlib-1.2.11/contrib/masmx64/gvmat64.asm:2:20: error: unknown token in expression  
; deflate_state *s,  
                   ^  
/Users/usr1/Desktop/Repos/some/thing/libs/beast/test/extern/zlib-1.2.11/contrib/masmx64/gvmat64.asm:3:17: error: unexpected token in argument list  
; IPos cur_match);  
                ^  
/Users/usr1/Desktop/Repos/some/thing/libs/beast/test/extern/zlib-1.2.11/contrib/masmx64/gvmat64.asm:5:22: error: unexpected token in argument list  
; gvmat64.asm -- Asm portion of the optimized longest_match for 32 bits x86_64  
                     ^  
/Users/usr1/Desktop/Repos/some/thing/libs/beast/test/extern/zlib-1.2.11/contrib/masmx64/gvmat64.asm:6:3: error: unexpected token at start of statement  
; (AMD64 on Athlon 64, Opteron, Phenom  
  ^  
/Users/usr1/Desktop/Repos/some/thing/libs/beast/test/extern/zlib-1.2.11/contrib/masmx64/gvmat64.asm:7:13: error: unexpected token in argument list  
; and Intel EM64T on Pentium 4 with EM64T, Pentium D, Core 2 Duo, Core I5/I7)  
            ^  
/Users/usr1/Desktop/Repos/some/thing/libs/beast/test/extern/zlib-1.2.11/contrib/masmx64/gvmat64.asm:8:17: error: unexpected token in argument list  
; Copyright (C) 1995-2010 Jean-loup Gailly, Brian Raiter and Gilles Vollant.  
                ^  
/Users/usr1/Desktop/Repos/some/thing/libs/beast/test/extern/zlib-1.2.11/contrib/masmx64/gvmat64.asm:10:16: error: unexpected token in argument list  
; File written by Gilles Vollant, by converting to assembly the longest_match  
               ^  
/Users/usr1/Desktop/Repos/some/thing/libs/beast/test/extern/zlib-1.2.11/contrib/masmx64/gvmat64.asm:11:18: error: unexpected token in argument list  
; from Jean-loup Gailly in deflate.c of zLib and infoZip zip.  
                 ^  
/Users/usr1/Desktop/Repos/some/thing/libs/beast/test/extern/zlib-1.2.11/contrib/masmx64/gvmat64.asm:13:10: error: unexpected token in argument list  
; and by taking inspiration on asm686 with masm, optimised assembly code  
         ^  
/Users/usr1/Desktop/Repos/some/thing/libs/beast/test/extern/zlib-1.2.11/contrib/masmx64/gvmat64.asm:14:14: error: unexpected token in argument list  
; from Brian Raiter, written 1998  
             ^  
/Users/usr1/Desktop/Repos/some/thing/libs/beast/test/extern/zlib-1.2.11/contrib/masmx64/gvmat64.asm:16:17: error: unexpected token in argument list  
; This software is provided 'as-is', without any express or implied  
                ^  
/Users/usr1/Desktop/Repos/some/thing/libs/beast/test/extern/zlib-1.2.11/contrib/masmx64/gvmat64.asm:17:16: error: unexpected token in argument list  
; warranty. In no event will the authors be held liable for any damages  
               ^  
/Users/usr1/Desktop/Repos/some/thing/libs/beast/test/extern/zlib-1.2.11/contrib/masmx64/gvmat64.asm:18:16: error: unexpected token in argument list  
; arising from the use of this software.  
               ^  
/Users/usr1/Desktop/Repos/some/thing/libs/beast/test/extern/zlib-1.2.11/contrib/masmx64/gvmat64.asm:20:17: error: unexpected token in argument list  
; Permission is granted to anyone to use this software for any purpose,  
                ^  
/Users/usr1/Desktop/Repos/some/thing/libs/beast/test/extern/zlib-1.2.11/contrib/masmx64/gvmat64.asm:21:24: error: unexpected token in argument list  
; including commercial applications, and to alter it and redistribute it  
                       ^  
/Users/usr1/Desktop/Repos/some/thing/libs/beast/test/extern/zlib-1.2.11/contrib/masmx64/gvmat64.asm:22:9: error: unknown token in expression  
; freely, subject to the following restrictions:  
        ^  
/Users/usr1/Desktop/Repos/some/thing/libs/beast/test/extern/zlib-1.2.11/contrib/masmx64/gvmat64.asm:24:3: error: unexpected token at start of statement  
; 1. The origin of this software must not be misrepresented; you must not  
  ^  
/Users/usr1/Desktop/Repos/some/thing/libs/beast/test/extern/zlib-1.2.11/contrib/masmx64/gvmat64.asm:24:71: error: unexpected token in argument list  
; 1. The origin of this software must not be misrepresented; you must not  
                                                                      ^  
/Users/usr1/Desktop/Repos/some/thing/libs/beast/test/extern/zlib-1.2.11/contrib/masmx64/gvmat64.asm:25:14: error: unexpected token in argument list  
; claim that you wrote the original software. If you use this software  
             ^  
/Users/usr1/Desktop/Repos/some/thing/libs/beast/test/extern/zlib-1.2.11/contrib/masmx64/gvmat64.asm:26:8: error: unexpected token in argument list  
; in a product, an acknowledgment in the product documentation would be  
       ^  
/Users/usr1/Desktop/Repos/some/thing/libs/beast/test/extern/zlib-1.2.11/contrib/masmx64/gvmat64.asm:27:19: error: unexpected token in argument list  
; appreciated but is not required.  
                  ^  
/Users/usr1/Desktop/Repos/some/thing/libs/beast/test/extern/zlib-1.2.11/contrib/masmx64/gvmat64.asm:28:3: error: unexpected token at start of statement  
; 2. Altered source versions must be plainly marked as such, and must not be  
  ^  
/Users/usr1/Desktop/Repos/some/thing/libs/beast/test/extern/zlib-1.2.11/contrib/masmx64/gvmat64.asm:29:21: error: unexpected token in argument list  
; misrepresented as being the original software  
                    ^  
/Users/usr1/Desktop/Repos/some/thing/libs/beast/test/extern/zlib-1.2.11/contrib/masmx64/gvmat64.asm:30:3: error: unexpected token at start of statement  
; 3. This notice may not be removed or altered from any source distribution.  
  ^  
/Users/usr1/Desktop/Repos/some/thing/libs/beast/test/extern/zlib-1.2.11/contrib/masmx64/gvmat64.asm:35:3: error: invalid symbol redefinition  
; http:  
  ^  
/Users/usr1/Desktop/Repos/some/thing/libs/beast/test/extern/zlib-1.2.11/contrib/masmx64/gvmat64.asm:36:3: error: invalid symbol redefinition  
; http:  
```  
  
What is this? I've been trying to solve this problem for 3 hours and counting.  
  
I'm working on XCode 10.1 (10B61), macOS Mojave 10.14.3 (18D109)

---

## Comment 10

> Username: vinniefalco  
> Created at: 2019-02-25 02:27:06 UTC  
> Url: https://github.com/boostorg/beast/issues/470#issuecomment-466850227  

Which branch of beast are you using? What are you trying to do? Are you trying to build the tests? Is this happening in your program? I need more information.

---

## Comment 11

> Username: aidv  
> Created at: 2019-02-25 02:33:55 UTC  
> Updated at: 2019-02-25 02:37:08 UTC  
> Url: https://github.com/boostorg/beast/issues/470#issuecomment-466851358  

I hope I can answer your questions properly.  
  
- I cloned the main branch, I'd assume. Just straight up `git clone https://github.com/boostorg/beast.git`  
- I am trying to start a WebSocket server  
- That makes me wonder; Should I build the tests?  
- Yes, during build  
  
**EDIT:** I can send my code to you. I am at the very start of my project so there is not code what so ever that I can't show.

---

## Comment 12

> Username: vinniefalco  
> Created at: 2019-02-25 02:47:18 UTC  
> Updated at: 2019-02-25 02:45:48 UTC  
> Url: https://github.com/boostorg/beast/issues/470#issuecomment-466853002  

You have to clone the boost superproject, as indicated in the https://github.com/boostorg/beast/blob/develop/README.md#building  
  
You can certainly build the tests, if you follow the steps.

---

## Comment 13

> Username: aidv  
> Created at: 2019-02-25 18:26:20 UTC  
> Url: https://github.com/boostorg/beast/issues/470#issuecomment-467122883  

Ok, so now I've followed your instructions exactly as described, and now I get an error on one of the targets om my project. It says Argument list too long:  
  
`recursive header expansion failed at /Users/usr1/Desktop/Repos/some/thing/libs/boost/libs/filesystem/test/issues/3332`  
  
which is weird because Boost isn't imported in the target in question at all. Boost used by a completely different target.

---

## Comment 14

> Username: vinniefalco  
> Created at: 2019-02-25 19:12:17 UTC  
> Url: https://github.com/boostorg/beast/issues/470#issuecomment-467141518  

Which targets on which projects? What command line are you using? I need more information.

---

## Comment 15

> Username: aidv  
> Created at: 2019-02-25 21:35:57 UTC  
> Url: https://github.com/boostorg/beast/issues/470#issuecomment-467192910  

I figured it out somehow, I can however not say how I did it because I tried so many things, but if one follows your instructions it should work without a problem.  
  
Also I had to call `sudo xcode-select --reset` from the terminal, and I think that it actually fixed the issues I had.  
  
However now that I've added some of your WebSocket code to my project and try to run it, my GUI seems to freeze and in fact freeze the whole application.  
  
But that's a different discussion.
