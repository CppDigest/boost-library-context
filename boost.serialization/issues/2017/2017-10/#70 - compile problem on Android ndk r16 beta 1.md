# #70 - compile problem on Android ndk r16 beta 1 [Closed]

> Username: berkus  
> Created at: 2017-10-25 12:57:44 UTC  
> Updated at: 2017-11-22 12:08:58 UTC  
> Closed at: 2017-11-08 17:22:15 UTC  
> Url: https://github.com/boostorg/serialization/issues/70  

Hello, in attempt to build boost for android with more or less reasonable strict settings (-Werror) I uncovered some bugs.  
  
```  
clang-darwin.compile.c++ android-build/boost/bin.v2/   
libs/serialization/build/clang-darwin-5.0~x86/debug/   
address-model-32/link-static/target-os-android/threading-   
multi/polymorphic_iarchive.o   
In file included from libs/serialization/src/polymorphic_iarchive.cpp:20:   
In file included from ./boost/archive/polymorphic_iarchive.hpp:32:   
./boost/archive/detail/iserializer.hpp:69:7: error: macro expansion   
producing 'defined' has undefined behavior [-Werror,-Wexpansion-to-defined]   
#if ! DONT_USE_HAS_NEW_OPERATOR   
      ^   
./boost/archive/detail/iserializer.hpp:63:12: note: expanded from macro   
'DONT_USE_HAS_NEW_OPERATOR'   
        || defined(__SUNPRO_CC) && (__SUNPRO_CC < 0x590) \   
           ^   
./boost/archive/detail/iserializer.hpp:212:9: error: macro expansion   
producing 'defined' has undefined behavior [-Werror,-Wexpansion-to-defined]   
    #if DONT_USE_HAS_NEW_OPERATOR   
        ^   
./boost/archive/detail/iserializer.hpp:63:12: note: expanded from macro   
'DONT_USE_HAS_NEW_OPERATOR'   
        || defined(__SUNPRO_CC) && (__SUNPRO_CC < 0x590) \   
           ^   
2 errors generated.   
    "/usr/local/opt/android-ndk/android-ndk-r16-beta1//   
toolchains/llvm/prebuilt/darwin-x86_64/bin/clang++"   
"-DBOOST_AC_USE_PTHREADS" "-DBOOST_SP_USE_PTHREADS" "-fvisibility=hidden"   
"-fvisibility-inlines-hidden" "-Wno-unused-local-typedef" -x c++   
-ftemplate-depth-255 -fvisibility=hidden -fvisibility-inlines-hidden   
-std=c++14 -O0 -g -O0 -fno-inline -Wall -g --target=i686-none-linux-android   
--gcc-toolchain=/usr/local/opt/android-ndk/android-ndk-   
r16-beta1//toolchains/x86-4.9/prebuilt/darwin-x86_64   
--sysroot=/usr/local/opt/android-ndk/android-ndk-r16-beta1//sysroot   
-isystem /usr/local/opt/android-ndk/android-ndk-r16-beta1//   
sources/cxx-stl/llvm-libc++/include -isystem /usr/local/opt/android-ndk/   
android-ndk-r16-beta1//sources/cxx-stl/llvm-libc++abi/include -isystem   
/usr/local/opt/android-ndk/android-ndk-r16-beta1//sources/android/support/include   
-isystem /usr/local/opt/android-ndk/android-ndk-r16-beta1//sysroot/usr/include   
-isystem /usr/local/opt/android-ndk/android-ndk-r16-beta1//   
sysroot/usr/include/i686-linux-android -DANDROID -D__ANDROID_API__=21   
-ffunction-sections -funwind-tables -fstack-protector-strong   
-fno-limit-debug-info -fPIC -no-canonical-prefixes -mstackrealign   
-Wa,--noexecstack -Wformat -Werror=format-security -Wall -Werror -Wshadow   
-march=i686 -DBOOST_ALL_NO_LIB=1 -D_LITTLE_ENDIAN -I"." -c -o   
"android-build/boost/bin.v2/libs/serialization/build/   
clang-darwin-5.0~x86/debug/address-model-32/link-static/   
target-os-android/threading-multi/polymorphic_iarchive.o"   
"libs/serialization/src/polymorphic_iarchive.cpp"   
```  
  
This is using `clang` toolchain on `Android NDK r16 beta1`.  
  
[​Reference to user-config.jam for the build](https://github.com/twilio/twilio-boost-build/blob/android-ndk-16/boost.sh#L526)

---

## Comment 1

> Username: berkus  
> Created at: 2017-10-25 13:05:42 UTC  
> Url: https://github.com/boostorg/serialization/issues/70#issuecomment-339323164  

Xref https://svn.boost.org/trac10/ticket/13272

---

## Comment 2

> Username: masterleinad  
> Created at: 2017-10-29 23:55:57 UTC  
> Url: https://github.com/boostorg/serialization/issues/70#issuecomment-340313674  

#55 addresses this

---

## Comment 3

> Username: berkus  
> Created at: 2017-11-22 12:08:58 UTC  
> Url: https://github.com/boostorg/serialization/issues/70#issuecomment-346332136  

Cool, thanks guys!
