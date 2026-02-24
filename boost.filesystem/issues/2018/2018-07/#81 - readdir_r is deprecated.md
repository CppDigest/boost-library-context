# #81 - readdir_r is deprecated [Closed]

> Username: kivadiu  
> Created at: 2018-07-30 08:30:27 UTC  
> Updated at: 2018-11-24 12:29:20 UTC  
> Closed at: 2018-11-24 12:29:20 UTC  
> Url: https://github.com/boostorg/filesystem/issues/81  

While building 1.68.0 beta 1 on x86_64 linux with g++ 8.2.0, I get the following warnings:  
  
```  
gcc.compile.c++ bin.v2/libs/filesystem/build/gcc-8.2.0/release/link-static/threading-multi/operations.o  
  
    "g++-8.2.0"   -I/softs/lin64-gcc-8.2.0/release/iconv/include -I/softs/lin64-gcc-8.2.0/release/gettext/include -I/softs/lin64-gcc-8.2.0/release/bzip2/include -I/softs/lin64-gcc-8.2.0/release/zlib/include -I/softs/lin64-gcc-8.2.0/release/jpeg-turbo/include -I/softs/lin64-gcc-8.2.0/release/xz/include -I/softs/lin64-gcc-8.2.0/release/tiff/include -I/softs/lin64-gcc-8.2.0/release/png/include -march=native -std=c++14 -O2 -DNDEBUG -m64 -pthread -O3 -finline-functions -Wno-inline -Wall  -DBOOST_ALL_NO_LIB=1 -DBOOST_ASIO_NO_DEPRECATED -DBOOST_FILESYSTEM_NO_DEPRECATED -DBOOST_FILESYSTEM_STATIC_LINK=1 -DBOOST_SYSTEM_STATIC_LINK=1 -DNDEBUG  -I"." -c -o "bin.v2/libs/filesystem/build/gcc-8.2.0/release/link-static/threading-multi/operations.o" "libs/filesystem/src/operations.cpp"  
  
libs/filesystem/src/operations.cpp: Dans la fonction « int {anonyme}::readdir_r_simulator(DIR*, dirent*, dirent**) »:  
libs/filesystem/src/operations.cpp:2126:18: warning: « int readdir_r(DIR*, dirent*, dirent**) » est obsolète [-Wdeprecated-declarations]  
       { return ::readdir_r(dirp, entry, result); }  
                  ^~~~~~~~~  
In file included from /usr/include/features.h:423,  
                 from /softs/gcc-8.2.0/include/c++/8.2.0/x86_64-pc-linux-gnu/bits/os_defines.h:39,  
                 from /softs/gcc-8.2.0/include/c++/8.2.0/x86_64-pc-linux-gnu/bits/c++config.h:508,  
                 from /softs/gcc-8.2.0/include/c++/8.2.0/cstddef:49,  
                 from ./boost/config/compiler/gcc.hpp:165,  
                 from ./boost/config.hpp:39,  
                 from ./boost/filesystem/operations.hpp:18,  
                 from libs/filesystem/src/operations.cpp:68:  
/usr/include/dirent.h:189:12: note: déclaré ici  
 extern int __REDIRECT (readdir_r,  
            ^~~~~~~~~~  
libs/filesystem/src/operations.cpp:2126:47: warning: « int readdir_r(DIR*, dirent*, dirent**) » est obsolète [-Wdeprecated-declarations]  
       { return ::readdir_r(dirp, entry, result); }  
                                               ^  
In file included from /usr/include/features.h:423,  
                 from /softs/gcc-8.2.0/include/c++/8.2.0/x86_64-pc-linux-gnu/bits/os_defines.h:39,  
                 from /softs/gcc-8.2.0/include/c++/8.2.0/x86_64-pc-linux-gnu/bits/c++config.h:508,  
                 from /softs/gcc-8.2.0/include/c++/8.2.0/cstddef:49,  
                 from ./boost/config/compiler/gcc.hpp:165,  
                 from ./boost/config.hpp:39,  
                 from ./boost/filesystem/operations.hpp:18,  
                 from libs/filesystem/src/operations.cpp:68:  
/usr/include/dirent.h:189:12: note: déclaré ici  
 extern int __REDIRECT (readdir_r,  
            ^~~~~~~~~~  
libs/filesystem/src/operations.cpp:2126:47: warning: « int readdir_r(DIR*, dirent*, dirent**) » est obsolète [-Wdeprecated-declarations]  
       { return ::readdir_r(dirp, entry, result); }  
                                               ^  
In file included from /usr/include/features.h:423,  
                 from /softs/gcc-8.2.0/include/c++/8.2.0/x86_64-pc-linux-gnu/bits/os_defines.h:39,  
                 from /softs/gcc-8.2.0/include/c++/8.2.0/x86_64-pc-linux-gnu/bits/c++config.h:508,  
                 from /softs/gcc-8.2.0/include/c++/8.2.0/cstddef:49,  
                 from ./boost/config/compiler/gcc.hpp:165,  
                 from ./boost/config.hpp:39,  
                 from ./boost/filesystem/operations.hpp:18,  
                 from libs/filesystem/src/operations.cpp:68:  
/usr/include/dirent.h:189:12: note: déclaré ici  
 extern int __REDIRECT (readdir_r,  
            ^~~~~~~~~~  
```

---

## Comment 1

> Username: weary  
> Created at: 2018-08-18 16:18:19 UTC  
> Url: https://github.com/boostorg/filesystem/issues/81#issuecomment-414069317  

I got the same after upgrading libc6-dev (which ships dirent.h) from 2.23-0ubuntu10 to 2.27-3ubuntu1. Apparently readdir_r got marked depricated in favor of readdir

---

## Comment 2

> Username: alexeikh  
> Created at: 2018-09-06 09:15:44 UTC  
> Url: https://github.com/boostorg/filesystem/issues/81#issuecomment-419024092  

Pull request with the fix already exists: #68. Waiting for the maintainers to merge it...

---

## Comment 3

> Username: Lastique  
> Created at: 2018-11-24 12:29:20 UTC  
> Url: https://github.com/boostorg/filesystem/issues/81#issuecomment-441364615  

Fixed in https://github.com/boostorg/filesystem/commit/0f6e3f06861748606af2c3f36b457a05f72d56e2.
