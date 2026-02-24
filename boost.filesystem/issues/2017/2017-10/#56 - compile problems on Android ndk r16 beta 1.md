# #56 - compile problems on Android ndk r16 beta 1 [Closed]

> Username: berkus  
> Created at: 2017-10-25 13:03:33 UTC  
> Updated at: 2018-03-22 09:00:26 UTC  
> Closed at: 2018-03-09 18:30:15 UTC  
> Url: https://github.com/boostorg/filesystem/issues/56  

Hello, in attempt to build boost for android with more or less reasonable strict settings (-Werror) I uncovered some bugs.  
  
```  
/usr/local/opt/android-ndk/android-ndk-r16-beta1//sources/android/support/include/stdio.h:36:25: error: invalid token at start of a preprocessor expression  
#if __USE_FILE_OFFSET64 && __ANDROID_API__ < __ANDROID_API_N__  
```  
  
This happens because filesystem defines a "harmless" macro:  
  
```  
libs/filesystem/src/operations.cpp:18:9: warning: '__USE_FILE_OFFSET64' macro redefined [-Wmacro-redefined]  
#define __USE_FILE_OFFSET64 // but that is harmless on Windows and on POSIX  
        ^  
```  
  
While this check was `#if defined(__USE_FILE_OFFSET64)` in previous android versions, it seems to have changed when they introduced unified headers. It's probably more "harmless" to define an integer value in boost.filesystem rather than persuade Google to fix their code.  
  
This is using `clang` toolchain on `Android NDK r16 beta1`.  
  
[​Reference to user-config.jam for the build](https://github.com/twilio/twilio-boost-build/blob/android-ndk-16/boost.sh#L526)

---

## Comment 1

> Username: berkus  
> Created at: 2017-10-25 13:04:37 UTC  
> Updated at: 2017-10-25 13:05:01 UTC  
> Url: https://github.com/boostorg/filesystem/issues/56#issuecomment-339322871  

Xref https://svn.boost.org/trac10/ticket/13274

---

## Comment 2

> Username: pdimov  
> Created at: 2017-10-25 14:09:33 UTC  
> Updated at: 2017-10-25 14:11:53 UTC  
> Url: https://github.com/boostorg/filesystem/issues/56#issuecomment-339342283  

See also https://github.com/android-ndk/ndk/issues/518 and https://github.com/android-ndk/ndk/issues/501  
  
> will be fixed in r16beta2.

---

## Comment 3

> Username: enh  
> Created at: 2018-03-01 20:45:29 UTC  
> Url: https://github.com/boostorg/filesystem/issues/56#issuecomment-369724980  

this bug can be closed: the released NDK added the missing `defined()` last year.

---

## Comment 4

> Username: alexeikh  
> Created at: 2018-03-12 14:28:00 UTC  
> Updated at: 2018-03-12 14:28:06 UTC  
> Url: https://github.com/boostorg/filesystem/issues/56#issuecomment-372328367  

It should be noted that:  
- This issue (#56) is about compilation failure of `#include <cstdio>` if `_FILE_OFFSET_BITS=64` is defined.  
- This issue (#56) should not be confused with issue #65, which is about compilation failure of `BOOST_RESIZE_FILE(...)` if `_FILE_OFFSET_BITS=64` is defined.  
- This issue (#56) is only fixed in Android NDK for libc++ (LLVM STL). If using libstdc++ (GNU STL), the issue remains.  
- Issue #65 is not fixed yet. Android NDK developers' opinion is that it must be fixed in the Boost.Filesystem code.  
- Pull request #69 aims to fix both issue #56 for GNU STL and issue #65.  
  
I am publishing this information here, so that #65 and #69 will not be mistakenly closed as duplicates for this issue (#56).

---

## Comment 5

> Username: berkus  
> Created at: 2018-03-22 09:00:26 UTC  
> Url: https://github.com/boostorg/filesystem/issues/56#issuecomment-375224271  

@alexeikh libstdc++ is no longer supported in latest NDKs and is slated for removal. I don't think any significant work will be done to remedy this in libstdc++.
