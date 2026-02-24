# #68 Don't use readdir_r on Linux and Android [Merged]

> Username: Lastique  
> Created at: 2018-03-01 23:38:34 UTC  
> Updated at: 2018-09-06 14:55:30 UTC  
> Merged at: 2018-09-06 13:46:00 UTC  
> Closed at: 2018-09-06 13:46:00 UTC  
> Url: https://github.com/boostorg/filesystem/pull/68  

`readdir_r` has been deprecated and has problems of its own[1]. glibc 2.24 marked  
`readdir_r` as deprecated and may eventually remove it. At the same time, plain  
`readdir` is thread-safe if different threads call it for different directory  
streams, which is fine in our case.  
  
[1]: http://man7.org/linux/man-pages/man3/readdir_r.3.html

---

## Comment 1

> Username: alexeikh  
> Created_at: 2018-04-10 16:03:00 UTC  
> Updated_at: 2018-04-10 16:05:41 UTC  
> Url: https://github.com/boostorg/filesystem/pull/68#issuecomment-380155622  

Apart from avoiding to use the deprecated functionality, this patch fixes issue #72. I.e. the patch is useful not only for GNU/Linux, but also for Android/Linux.  
  
Android NDK developers recommend to use `readdir()` instead of `readdir_r()`. Here is a nice article from one of them, with the reasoning: http://elliotth.blogspot.com/2012/10/how-not-to-use-readdirr3.html  
  
About thread-safety of `readdir()`, Elliott writes the following:  
> In practice, you don't have a problem with readdir(3) because Android's bionic, Linux's glibc, and OS X and iOS' libc all allocate per-DIR* buffers, and return pointers into those

---

## Comment 2

> Username: alexeikh  
> Created_at: 2018-05-30 11:12:11 UTC  
> Url: https://github.com/boostorg/filesystem/pull/68#issuecomment-393122833  

Non-maintainer review of the patch. Hopefully will help the maintainers to spend less time on their official review.  
  
The patch is tiny, straightforward, very understandable.  
  
The patch adds conditions checking for `Linux` and `Android`. One may argue that `readdir()` and `readdir_r()` are properties of libc, not of the OS, and it would be more correct to check for `glibc` and `bionic` instead of checking for `Linux` and `Android`. And this way Boost.Filesystem will not change its behavior on other Linux libcs, like `uclibc` or `dietlibc`. A counter-argument may be that other checks in the surrounding code check for OS, namely `HP-UX` and `AIX`. I don't know the maintainers' position on this question. For me, either way is OK.  
  
The patch can benefit from comment(s), explaining why a condition was added.  
  
Even if the patch can be improved, I think it is already good and can be merged in.

---

## Comment 3

> Username: jeking3  
> Created_at: 2018-07-17 01:48:11 UTC  
> Url: https://github.com/boostorg/filesystem/pull/68#issuecomment-405433281  

I just ran into this while building with "warnings as errors" in iostreams:  
```  
gcc.compile.c++ ../../bin.v2/libs/filesystem/build/gcc-7.3/debug/cxxstd-03-iso/link-static/operations.o  
../../libs/filesystem/src/operations.cpp: In function 'int {anonymous}::readdir_r_simulator(DIR*, dirent*, dirent**)':  
../../libs/filesystem/src/operations.cpp:2126:18: error: 'int readdir_r(DIR*, dirent*, dirent**)' is deprecated [-Werror=deprecated-declarations]  
       { return ::readdir_r(dirp, entry, result); }  
                  ^~~~~~~~~  
In file included from /usr/include/features.h:424:0,  
                 from /usr/include/x86_64-linux-gnu/c++/7/bits/os_defines.h:39,  
                 from /usr/include/x86_64-linux-gnu/c++/7/bits/c++config.h:533,  
                 from /usr/include/c++/7/cstddef:49,  
                 from ../../boost/config/compiler/gcc.hpp:165,  
                 from ../../boost/config.hpp:39,  
                 from ../../boost/filesystem/operations.hpp:18,  
                 from ../../libs/filesystem/src/operations.cpp:68:  
/usr/include/dirent.h:189:12: note: declared here  
 extern int __REDIRECT (readdir_r,  
            ^  
../../libs/filesystem/src/operations.cpp:2126:47: error: 'int readdir_r(DIR*, dirent*, dirent**)' is deprecated [-Werror=deprecated-declarations]  
       { return ::readdir_r(dirp, entry, result); }  
                                               ^  
In file included from /usr/include/features.h:424:0,  
                 from /usr/include/x86_64-linux-gnu/c++/7/bits/os_defines.h:39,  
                 from /usr/include/x86_64-linux-gnu/c++/7/bits/c++config.h:533,  
                 from /usr/include/c++/7/cstddef:49,  
                 from ../../boost/config/compiler/gcc.hpp:165,  
                 from ../../boost/config.hpp:39,  
                 from ../../boost/filesystem/operations.hpp:18,  
                 from ../../libs/filesystem/src/operations.cpp:68:  
/usr/include/dirent.h:189:12: note: declared here  
 extern int __REDIRECT (readdir_r,  
            ^  
cc1plus: all warnings being treated as errors  
  
    "/usr/bin/g++-7"   -std=c++03 -m64 -O0 -fno-inline -Wall -g -Werror -DBOOST_ALL_NO_LIB=1 -DBOOST_FILESYSTEM_STATIC_LINK=1 -DBOOST_SYSTEM_STATIC_LINK=1  -I"../.." -c -o "../../bin.v2/libs/filesystem/build/gcc-7.3/debug/cxxstd-03-iso/link-static/operations.o" "../../libs/filesystem/src/operations.cpp"  
```  
  
Any reason why this hasn't been merged?

---
