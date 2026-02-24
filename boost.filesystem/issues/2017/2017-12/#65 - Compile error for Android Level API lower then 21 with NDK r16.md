# #65 - Compile error for Android Level API lower then 21 with NDK r16 [Closed]

> Username: Bjoe  
> Created at: 2017-12-07 11:20:37 UTC  
> Updated at: 2018-06-10 00:53:57 UTC  
> Closed at: 2018-06-10 00:53:57 UTC  
> Url: https://github.com/boostorg/filesystem/issues/65  

Boost 1.65.1  
Compiler: clang from android NDK  
clang_version__ "5.0.300080 "  
NDK version: 16.0.4442984  
Build system: cmake (3.6.4111459)  
Std librariy: libc++_static  
Build machine: Ubuntu 16.04.3 LTS  
  
Since Google [introduced unified headers](https://android.googlesource.com/platform/ndk/+/master/docs/UnifiedHeaders.md) the following compile error occurs to build boost::filesystem for example Android Level 19:  
```  
clang-linux.compile.c++.without-pth bin.v2/libs/filesystem/build/clang-linux-android/release/link-static/target-os-android/threading-multi/operations.o  
libs/filesystem/src/operations.cpp:1705:12: error: no member named 'truncate' in the global namespace  
    error(!BOOST_RESIZE_FILE(p.c_str(), size) ? BOOST_ERRNO : 0, p, ec,  
           ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
libs/filesystem/src/operations.cpp:224:38: note: expanded from macro 'BOOST_RESIZE_FILE'  
#   define BOOST_RESIZE_FILE(P,SZ)(::truncate(P, SZ)== 0)  
                                   ~~^  
1 error generated.  
  
  "/android-sdk/ndk-bundle/toolchains/llvm/prebuilt/linux-x86_64/bin/clang++" -c -x c++ -Qunused-arguments -std=c++11 -frtti -fexceptions -Oz -DNDEBUG  -isysroot /android-sdk/ndk-bundle/sysroot --target=armv7-none-linux-androideabi --gcc-toolchain=/android-sdk/ndk-bundle/toolchains/arm-linux-androideabi-4.9/prebuilt/linux-x86_64 -isystem /android-sdk/ndk-bundle/sysroot/usr/include -I/android-sdk/ndk-bundle/sources/cxx-stl/llvm-libc++/include -I/android-sdk/ndk-bundle/sources/android/support/include -I/android-sdk/ndk-bundle/sources/cxx-stl/llvm-libc++abi/include -O3 -Wno-inline -Wall -DBOOST_LOG_WITHOUT_SYSLOG -isystem /android-sdk/ndk-bundle/sysroot/usr/include/arm-linux-androideabi -D__ANDROID_API__=19 -g -DANDROID -ffunction-sections -funwind-tables -fstack-protector-strong -no-canonical-prefixes -march=armv7-a -mfloat-abi=softfp -mfpu=vfpv3-d16 -fno-integrated-as -mthumb -Wa,--noexecstack -Wformat -Werror=format-security  -Qunused-arguments -DHAVE_PTHREADS -DBOOST_ALL_NO_LIB=1 -DBOOST_FILESYSTEM_STATIC_LINK=1 -DBOOST_SYSTEM_STATIC_LINK=1 -DNDEBUG -I"." -o "bin.v2/libs/filesystem/build/clang-linux-android/release/link-static/target-os-android/threading-multi/operations.o" "libs/filesystem/src/operations.cpp"  
  
...failed clang-linux.compile.c++.without-pth bin.v2/libs/filesystem/build/clang-linux-android/release/link-static/target-os-android/threading-multi/operations.o...  
```  
  
Before they introduced the unified headers the header file unistd.h didn't care about the defines _FILE_OFFSET_BITS or __USE_FILE_OFFSET64. Here a snippit from unistd.h:  
```C++  
...  
extern int lchown(const char *, uid_t, gid_t);  
extern int truncate(const char *, off_t);  
extern char *getcwd(char *, size_t);  
  
extern int sync(void);  
  
extern int close(int);  
extern off_t lseek(int, off_t, int);  
extern off64_t lseek64(int, off64_t, int);  
  
extern ssize_t read(int, void *, size_t);  
extern ssize_t write(int, const void *, size_t);  
extern ssize_t pread(int, void *, size_t, off_t);  
extern ssize_t pread64(int, void *, size_t, off64_t);  
extern ssize_t pwrite(int, const void *, size_t, off_t);  
extern ssize_t pwrite64(int, const void *, size_t, off64_t);  
  
extern int dup(int);  
extern int dup2(int, int);  
extern int fcntl(int, int, ...);  
extern int ioctl(int, int, ...);  
extern int flock(int, int);  
extern int fsync(int);  
extern int fdatasync(int);  
extern int ftruncate(int, off_t);  
extern int ftruncate64(int, off64_t);  
...  
```  
Since they introduced unified headers, unistd.h snippet:  
```C++  
...  
#if defined(__USE_FILE_OFFSET64)  
  
#if __ANDROID_API__ >= 21  
int truncate(const char* __path, off_t __length) __RENAME(truncate64) __INTRODUCED_IN(21);  
#endif /* __ANDROID_API__ >= 21 */  
  
  
#if __ANDROID_API__ >= 12  
ssize_t pread(int __fd, void* __buf, size_t __count, off_t __offset)  
  __overloadable __RENAME(pread64) __INTRODUCED_IN(12);  
ssize_t pwrite(int __fd, const void* __buf, size_t __count, off_t __offset)  
  __overloadable __RENAME(pwrite64) __INTRODUCED_IN(12);  
int ftruncate(int __fd, off_t __length) __RENAME(ftruncate64) __INTRODUCED_IN(12);  
#endif /* __ANDROID_API__ >= 12 */  
  
#else  
int truncate(const char* __path, off_t __length);  
ssize_t pread(int __fd, void* __buf, size_t __count, off_t __offset)  
    __overloadable __RENAME_CLANG(pread);  
ssize_t pwrite(int __fd, const void* __buf, size_t __count, off_t __offset)  
    __overloadable __RENAME_CLANG(pwrite);  
int ftruncate(int __fd, off_t __length);  
#endif  
  
  
#if __ANDROID_API__ >= 21  
int truncate64(const char* __path, off64_t __length) __INTRODUCED_IN(21);  
#endif /* __ANDROID_API__ >= 21 */  
  
  
#if __ANDROID_API__ >= 12  
ssize_t pread64(int __fd, void* __buf, size_t __count, off64_t __offset)  
    __INTRODUCED_IN(12) __overloadable __RENAME_CLANG(pread64);  
ssize_t pwrite64(int __fd, const void* __buf, size_t __count, off64_t __offset)  
    __INTRODUCED_IN(12) __overloadable __RENAME_CLANG(pwrite64);  
int ftruncate64(int __fd, off64_t __length) __INTRODUCED_IN(12);  
#endif /* __ANDROID_API__ >= 12 */  
...  
```  
Now they care about the define __USE_FILE_OFFSET64 and truncate for 64 Bit is not implemented before Android API level 21.  
  
We compile and use boost::filesystem for Android API level 19 long time ago and we didn't have any problems. So I decided to "if def" the define of  __USE_FILE_OFFSET64 around (see snippet):  
```C++  
...  
#if !(defined(__ANDROID__) && __ANDROID_API__ < 21)  
//  define 64-bit offset macros BEFORE including boost/config.hpp (see ticket #5355)  
#if !(defined(__HP_aCC) && defined(_ILP32) && !defined(_STATVFS_ACPP_PROBLEMS_FIXED))  
#define _FILE_OFFSET_BITS 64 // at worst, these defines may have no effect,  
#endif  
#if !defined(__PGI)  
#define __USE_FILE_OFFSET64 // but that is harmless on Windows and on POSIX  
      // 64-bit systems or on 32-bit systems which don't have files larger   
      // than can be represented by a traditional POSIX/UNIX off_t type.   
      // OTOH, defining them should kick in 64-bit off_t's (and thus   
      // st_size)on 32-bit systems that provide the Large File  
      // Support (LFS)interface, such as Linux, Solaris, and IRIX.  
      // The defines are given before any headers are included to  
      // ensure that they are available to all included headers.  
      // That is required at least on Solaris, and possibly on other  
      // systems as well.  
#else  
#define _FILE_OFFSET_BITS 64  
#endif  
#endif  
...  
```  
to skip this so that we have the same behavior as before, I think.  
  
I create a patch file but I will not create a pull request because I think this is not the right solution and you should decided if this is a proper fix or not. Then I can create a pull request, if needed.  
  
I added the patch file and a preprocessor output of the compile error in operations.cpp  
  
[operations.ii.txt](https://github.com/boostorg/filesystem/files/1538803/operations.ii.txt)  
  
[android-boost-1_65_1-patches.patch.txt](https://github.com/boostorg/filesystem/files/1538797/android-boost-1_65_1-patches.patch.txt)  
  
You need some help or additional information, let me know.

---

## Comment 1

> Username: Bjoe  
> Created at: 2017-12-07 12:39:09 UTC  
> Updated at: 2017-12-07 13:45:35 UTC  
> Url: https://github.com/boostorg/filesystem/issues/65#issuecomment-349955454  

I have seen there is a ticket in track https://svn.boost.org/trac10/ticket/13230 . It is most likely the same issue

---

## Comment 2

> Username: rcdailey  
> Created at: 2018-01-08 15:29:02 UTC  
> Url: https://github.com/boostorg/filesystem/issues/65#issuecomment-355998056  

@Bjoe Any idea what patch they're talking about in the issue you linked? They didn't seem to include a link to a github commit or pull request that I can see.

---

## Comment 3

> Username: Bjoe  
> Created at: 2018-01-12 12:12:19 UTC  
> Updated at: 2018-01-12 12:30:09 UTC  
> Url: https://github.com/boostorg/filesystem/issues/65#issuecomment-357223233  

@rcdailey Hi. I think my patch .... see above: [android-boost-1_65_1-patches.patch.txt](https://github.com/boostorg/filesystem/files/1538797/android-boost-1_65_1-patches.patch.txt)  .... I didn't create a pull request yet because my patch fixes the build issue (!) not the problem ! I'm still waiting for any contributors from the boost filesystem project to give me a answer, if I should create a pull request ... maybe in the next time I will created one...

---

## Comment 4

> Username: Bjoe  
> Created at: 2018-01-12 12:26:10 UTC  
> Updated at: 2018-01-12 12:31:40 UTC  
> Url: https://github.com/boostorg/filesystem/issues/65#issuecomment-357225761  

Btw ... #56 and [boost.filesystem compile problem on Android](https://svn.boost.org/trac10/ticket/13274) is the same issue

---

## Comment 5

> Username: rcdailey  
> Created at: 2018-02-23 01:04:01 UTC  
> Url: https://github.com/boostorg/filesystem/issues/65#issuecomment-367875904  

Update: I confirmed via [response from an NDK developer](https://github.com/android-ndk/ndk/issues/501#issuecomment-367849150) that this issue is indeed a boost.filesystem problem, not an NDK bug.  
  
When will this issue get some attention? It's been months.

---

## Comment 6

> Username: Lastique  
> Created at: 2018-03-01 19:42:57 UTC  
> Url: https://github.com/boostorg/filesystem/issues/65#issuecomment-369707440  

Disclaimer: I'm not a Boost.Filesystem maintainer.  
  
From the preprocessed output, there is no declaration of `::truncate`, which I think is supposed to be there regardless of whether we define `_FILE_OFFSET_BITS` or `__USE_FILE_OFFSET64` or not. This is part of POSIX API, and, if I understand correctly, it was also provided by an older NDK for the target Android API version you use. So, the fact that it is missing seems to me a bug in NDK.  
  
Granted, we shouldn't define `__USE_FILE_OFFSET64` ourselves as this is an internal macro specific to libc. The public POSIX macro is `_FILE_OFFSET_BITS`, which is the one we should define. But this may be a workaround for some broken libc, I don't know. If this is `__USE_FILE_OFFSET64` that breaks NDK then we should remove it in this case.

---

## Comment 7

> Username: enh  
> Created at: 2018-03-01 20:42:41 UTC  
> Url: https://github.com/boostorg/filesystem/issues/65#issuecomment-369724220  

this is the mistake:  
  
> if I understand correctly, it was also provided by an older NDK for the target Android API version you use  
  
no, it wasn't. in older NDKs, `_FILE_OFFSET_BITS` and `__USE_FILE_OFFSET64` were ignored. so you just used to get a 32-bit off_t and 32-bit functions regardless. the current NDK supports `_FILE_OFFSET_BITS=64`, but at the cost of various degrees of support at different API levels.  
  
if you want the old behavior back 100%, just don't define `_FILE_OFFSET_BITS`/`__USE_FILE_OFFSET64` if `defined(__ANDROID__)`.  
  
alternatively, at the cost of ABI issues on your side, you could check `__ANDROID_API_LEVEL__` and enable `__FILE_OFFSET_BITS=64` if you're being built targeting a new enough API level to have 64-bit versions of all the functions you need. but if you have any `off_t`s in your ABI, that will be an ABI break.

---

## Comment 8

> Username: Lastique  
> Created at: 2018-03-01 22:10:47 UTC  
> Url: https://github.com/boostorg/filesystem/issues/65#issuecomment-369748678  

That NDK removes pieces of otherwise available APIs when `_FILE_OFFSET_BITS` is defined is a bug of NDK. It should either support this mode in full or not (in which case it should use 32-bit offsets throughout the API regardless of the macro).  
  
I guess, we could consider `_FILE_OFFSET_BITS` broken in the NDK and not define it. Although I wonder if other Android SDKs like CrystaX do not suffer from this problem. We might want to apply this workaround specifically for the Google NDK.

---

## Comment 9

> Username: enh  
> Created at: 2018-03-01 23:08:59 UTC  
> Url: https://github.com/boostorg/filesystem/issues/65#issuecomment-369763391  

> That NDK removes pieces of otherwise available APIs when _FILE_OFFSET_BITS is defined is a bug of NDK.  
  
you think so (and it's a not entirely invalid argument), but others argue the opposite: that if they ask for `_FILE_OFFSET_BITS=64` they should get as much as was available at their API level, because API 26 is a long time to wait for real-world app developers. (and this was, after all, how the Android platform worked during those years: with only a small subset of the functionality available. almost no-one needs it all.)  
  
an argument in favor of the behavior we chose for the NDK is that it's trivial for dissenters to get what they want (just stop defining `_FILE_OFFSET_BITS`) but non-trivial for the others to get partial support on old API levels (they'd have to touch a lot of different NDK header files).

---

## Comment 10

> Username: Lastique  
> Created at: 2018-03-01 23:09:51 UTC  
> Url: https://github.com/boostorg/filesystem/issues/65#issuecomment-369763569  

Could someone test if https://github.com/boostorg/filesystem/pull/67 fixes the problem?

---

## Comment 11

> Username: Lastique  
> Created at: 2018-03-02 14:05:16 UTC  
> Url: https://github.com/boostorg/filesystem/issues/65#issuecomment-369929117  

@enh I understand your reasoning but I disagree. When the user defines `_FILE_OFFSET_BITS=64` he wants to have the full support for 64-bit offsets. In older API versions this is not possible to provide, so the `_FILE_OFFSET_BITS=64` cannot and should not be supported. The interested parties that need particular 64-bit APIs that happen to be available in older API versions can already use `off64_t` and accordingly named functions like `lseek64`.

---

## Comment 12

> Username: rcdailey  
> Created at: 2018-03-02 15:22:14 UTC  
> Url: https://github.com/boostorg/filesystem/issues/65#issuecomment-369950483  

@Lastique I can confirm your changes fix the problem. I checked out your branch on your fork and compiled for the following platform using Android NDK:  
  
```  
Android NDK Version: r16b  
Architecture: armeabi-v7a  
API: 15  
Toolchain: Clang  
STL: libc++ (LLVM)  
```  
  
I applied your patch to boost 1.66.0 for my test. Patch file I used is available at the bottom of this post. Can we get this hotfixed into 1.66?  
  
```patch  
diff --git a/src/operations.cpp b/src/operations.cpp  
index 035612c..df9aa42 100644  
--- a/src/operations.cpp  
+++ b/src/operations.cpp  
@@ -10,7 +10,9 @@  
   
 //--------------------------------------------------------------------------------------//   
   
-//  define 64-bit offset macros BEFORE including boost/config.hpp (see ticket #5355)   
+//  define 64-bit offset macros BEFORE including boost/config.hpp (see ticket #5355)  
+//  Google Android NDK is broken though as it doesn't provide truncate() declaration when _FILE_OFFSET_BITS=64 is defined (https://github.com/boostorg/filesystem/issues/65)  
+#if !defined(__ANDROID__) || (__ANDROID_API__+0) >= 21 || defined(__CRYSTAX__)  
 #if !(defined(__HP_aCC) && defined(_ILP32) && !defined(_STATVFS_ACPP_PROBLEMS_FIXED))  
 #define _FILE_OFFSET_BITS 64 // at worst, these defines may have no effect,  
 #endif  
@@ -28,6 +30,7 @@  
 #else  
 #define _FILE_OFFSET_BITS 64  
 #endif  
+#endif // !defined(__ANDROID__) || (__ANDROID_API__+0) >= 21 || defined(__CRYSTAX__)  
   
 // define BOOST_FILESYSTEM_SOURCE so that <boost/filesystem/config.hpp> knows  
 // the library is being built (possibly exporting rather than importing code)  
@@ -44,7 +47,8 @@  
 #include <boost/filesystem/operations.hpp>  
 #include <boost/scoped_array.hpp>  
 #include <boost/detail/workaround.hpp>  
-#include <vector>   
+#include <limits>  
+#include <vector>  
 #include <cstdlib>     // for malloc, free  
 #include <cstring>  
 #include <cstdio>      // for remove, rename  
@@ -1615,6 +1619,12 @@ namespace detail  
   BOOST_FILESYSTEM_DECL  
   void resize_file(const path& p, uintmax_t size, system::error_code* ec)  
   {  
+#   if defined(BOOST_POSIX_API)  
+    if (BOOST_UNLIKELY(size > static_cast< uintmax_t >((std::numeric_limits< off_t >::max)()))) {  
+      error(system::errc::file_too_large, p, ec, "boost::filesystem::resize_file");  
+      return;  
+    }  
+#   endif  
     error(!BOOST_RESIZE_FILE(p.c_str(), size) ? BOOST_ERRNO : 0, p, ec,  
       "boost::filesystem::resize_file");  
   }  
```

---

## Comment 13

> Username: alexeikh  
> Created at: 2018-05-11 08:39:08 UTC  
> Url: https://github.com/boostorg/filesystem/issues/65#issuecomment-388300264  

Now when #67 has been merged, this issue can be closed, I think.

---

## Comment 14

> Username: Bjoe  
> Created at: 2018-06-10 00:53:57 UTC  
> Url: https://github.com/boostorg/filesystem/issues/65#issuecomment-396010233  

Yep. I close this. Thanks @alexeikh
