# #61 - compile problems on Android ndk r16 beta 1 [Closed]

> Username: berkus  
> Created at: 2017-10-25 13:00:07 UTC  
> Updated at: 2018-01-27 22:28:38 UTC  
> Closed at: 2018-01-27 22:28:37 UTC  
> Url: https://github.com/boostorg/container/issues/61  

Hello, in attempt to build boost for android with more or less reasonable strict settings (-Werror) I uncovered some bugs.  
  
```  
clang-darwin.compile.c android-build/boost/bin.v2/   
libs/container/build/clang-darwin-5.0~x86/debug/address-   
model-32/link-static/target-os-android/threading-multi/alloc_lib.o   
In file included from libs/container/src/alloc_lib.c:19:   
libs/container/src/dlmalloc_ext_2_8_6.c:1097:8: error: variable 'ret' is   
used uninitialized whenever 'if' condition is false [-Werror,-Wsometimes-   
uninitialized]   
   if (!PREACTION(m)) {   
       ^~~~~~~~~~~~~   
libs/container/src/dlmalloc_ext_2_8_6.c:1125:11: note: uninitialized use   
occurs here   
   return ret;   
          ^~~   
libs/container/src/dlmalloc_ext_2_8_6.c:1097:4: note: remove the 'if' if   
its condition is always true   
   if (!PREACTION(m)) {   
   ^~~~~~~~~~~~~~~~~~~   
libs/container/src/dlmalloc_ext_2_8_6.c:1095:4: note: variable 'ret' is   
declared here   
   boost_cont_malloc_stats_t ret;   
   ^   
1 error generated.   
    "/usr/local/opt/android-ndk/android-ndk-r16-beta1//   
toolchains/llvm/prebuilt/darwin-x86_64/bin/clang++"   
"-DBOOST_AC_USE_PTHREADS" "-DBOOST_SP_USE_PTHREADS" "-fvisibility=hidden"   
"-fvisibility-inlines-hidden" "-Wno-unused-local-typedef" -x c -O0 -g -O0   
-fno-inline -Wall -g --target=i686-none-linux-android   
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
-march=i686 -DBOOST_ALL_NO_LIB=1 -DBOOST_CONTAINER_STATIC_LINK=1   
-D_LITTLE_ENDIAN -I"." -c -o "android-build/boost/bin.v2/   
libs/container/build/clang-darwin-5.0~x86/debug/address-   
model-32/link-static/target-os-android/threading-multi/alloc_lib.o"   
"libs/container/src/alloc_lib.c"  
```  
  
This is using `clang` toolchain on `Android NDK r16 beta1`.  
  
[​Reference to user-config.jam for the build](https://github.com/twilio/twilio-boost-build/blob/android-ndk-16/boost.sh#L526)

---

## Comment 1

> Username: berkus  
> Created at: 2017-10-25 13:06:01 UTC  
> Url: https://github.com/boostorg/container/issues/61#issuecomment-339323224  

Xref https://svn.boost.org/trac10/ticket/13271

---

## Comment 2

> Username: igaztanaga  
> Created at: 2018-01-27 22:28:37 UTC  
> Url: https://github.com/boostorg/container/issues/61#issuecomment-361020564  

Thanks for the report.  Referenced commit should fix the uninitialized variable issue.
