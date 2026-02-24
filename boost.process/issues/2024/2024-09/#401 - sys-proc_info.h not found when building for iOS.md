# #401 - sys/proc_info.h not found when building for iOS [Closed]

> Username: alexsharoff  
> Created at: 2024-09-12 09:15:34 UTC  
> Updated at: 2024-10-25 01:32:46 UTC  
> Closed at: 2024-10-25 01:32:46 UTC  
> Url: https://github.com/boostorg/process/issues/401  

When building Boost 1.86.0 for iOS we get the following error:  
```  
"/Applications/Xcode.12.2.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/clang++"   -std=c++11 -fvisibility-inlines-hidden -arch armv7 -arch armv7s -mios-version-min=9 -isysroot /Applications/Xcode.12.2.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/SDKs/iPhoneOS14.2.sdk -w -std=gnu++14 -mios-version-min=9 -std=gnu++14 -fPIC    -DBOOST_CHRONO_THREAD_DISABLED -O3 -Wall -fvisibility=hidden -Wno-inline -m32  -DBOOST_ALL_NO_LIB=1 -DBOOST_ASIO_NO_DEPRECATED -DBOOST_ATOMIC_STATIC_LINK=1 -DBOOST_FILESYSTEM_STATIC_LINK=1 -DBOOST_PROCESS_SOURCE=1 -DNDEBUG   -I"."  -c -o ".../boost/b/boost/bin.v2/libs/process/build/clang-darwin-12/release/arm_32/cxxstd-11-iso/link-static/target-os-iphone/threading-multi/visibility-hidden/ext/proc_info.o" "libs/process/src/ext/proc_info.cpp"  
libs/process/src/ext/proc_info.cpp:23:10: fatal error: 'sys/proc_info.h' file not found  
#include <sys/proc_info.h>  
```  
Maybe `sys/proc_info.h` is available only on macOS?

---

## Comment 1

> Username: ghost  
> Created at: 2024-09-26 23:28:46 UTC  
> Updated at: 2024-09-26 23:51:46 UTC  
> Url: https://github.com/boostorg/process/issues/401#issuecomment-2378114962  

Correct, that header is only available on macOS. However this is just one of many problems you will run into trying to build boost process on iOS. As far as I'm aware iOS is not even a platform we are looking to support just yet. It doesn't even support any real or actual shell interpreter. Many features will be missing due to how locked-down of a platform it is. I wouldn't count on this ever changing.  
  
Edit:  
  
Perhaps we could at least do platform macros which exclude stuff from iOS builds that are meant for macOS in specific, that way boost itself will still build even if that means boost process being very incomplete in of itself.  
  
Edit2:   
  
Neither me or the lead boost process dev know whether boost officially supports iOS at any capacity. That is something worth looking in to.

---

## Comment 2

> Username: alexsharoff  
> Created at: 2024-09-27 05:45:33 UTC  
> Url: https://github.com/boostorg/process/issues/401#issuecomment-2378444837  

Thank you for clarification. If Boost.Process is not supported for iOS, it would be nice to disable it when building the whole Boost library collection for iOS by default, if possible.

---

## Comment 3

> Username: ghost  
> Created at: 2024-09-28 20:44:48 UTC  
> Url: https://github.com/boostorg/process/issues/401#issuecomment-2380890480  

I don't have any experience with this, so don't ask me how, but according to the lead boost process dev:  
  
> They can just exclude build process manually

---

## Comment 4

> Username: alexsharoff  
> Created at: 2024-09-28 20:54:35 UTC  
> Url: https://github.com/boostorg/process/issues/401#issuecomment-2380892469  

For context: we're migrating from version 1.78 to 1.86. Running b2 with the same arguments as before now produces the above error. Of course we did exclude the library already via `--without-process`, but it seems like a workaround rather than a solution. If possible, it would be nice to have a working build out of the box.
