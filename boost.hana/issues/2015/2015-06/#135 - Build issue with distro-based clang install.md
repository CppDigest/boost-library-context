# #135 - Build issue with distro-based clang install. [Closed]

> Username: camio  
> Created at: 2015-06-17 18:48:10 UTC  
> Updated at: 2015-06-25 00:09:53 UTC  
> Closed at: 2015-06-25 00:09:53 UTC  
> Url: https://github.com/boostorg/hana/issues/135  

```  
export CXX=clang++  
export CC=clang  
cmake ~/code/hana -G Ninja  
```  
  
When I start building I get many errors like the following:  
  
```  
[26/886] Building CXX object example/CMakeFiles/example.comparable.dir/comparable.cpp.o  
FAILED: /usr/bin/clang++   -DBOOST_HANA_CONFIG_ENABLE_STRING_UDL -I/home/david/code/hana/include    -ftemplate-backtrace-limit=0 -pedantic -std=c++1y -W -Wall -Wextra -Wno-unused-local-typedefs -Wwrite-strings -stdlib=libc++ -Wno-gnu-string-literal-operator-template -Wno-unused-parameter -MMD -MT example/CMakeFiles/example.comparable.dir/comparable.cpp.o -MF example/CMakeFiles/example.comparable.dir/comparable.cpp.o.d -o example/CMakeFiles/example.comparable.dir/comparable.cpp.o -c /home/david/code/hana/example/comparable.cpp  
In file included from /home/david/code/hana/example/comparable.cpp:7:  
In file included from /home/david/code/hana/include/boost/hana/assert.hpp:13:  
In file included from /home/david/code/hana/include/boost/hana/bool.hpp:16:  
In file included from /home/david/code/hana/include/boost/hana/constant.hpp:16:  
/home/david/code/hana/include/boost/hana/config.hpp:62:10: fatal error: 'ciso646' file not found  
#include <ciso646>  
         ^  
1 error generated.  
```  
  
Workaround is to edit the CMakeCache.txt and set BOOST_HANA_ENABLE_LIBCXX to OFF.  
  
This is a vanilla ArchLinux distro.

---

## Comment 1

> Username: ldionne  
> Created at: 2015-06-18 01:51:54 UTC  
> Url: https://github.com/boostorg/hana/issues/135#issuecomment-113009199  

Thanks for the report, David. For the record, did you specify `-DLIBCXX_ROOT=/path/to/libc++` when generating with CMake? Regardless, I think using `stdlib=libc++` is never actually required except on OS X before Mavericks, so I'll just switch it off by default.

---

## Comment 2

> Username: camio  
> Created at: 2015-06-18 15:11:27 UTC  
> Url: https://github.com/boostorg/hana/issues/135#issuecomment-113188190  

I tried specifying LIBCXX_ROOT to /usr and that brought on some strange errors. I didn't realize what was meant by "root". I'll verify your update later today. Thanks Louis.

---

## Comment 3

> Username: Manu343726  
> Created at: 2015-06-18 15:40:30 UTC  
> Url: https://github.com/boostorg/hana/issues/135#issuecomment-113196447  

Hi guys.  
  
I just checked this with my Arch box. Clang 3.6.0, libc++3.6.0 (Both vanilla from [official](https://www.archlinux.org/packages/extra/x86_64/clang/) [repositories](https://www.archlinux.org/packages/community/x86_64/libc++/)). Following build instructions with custom `-DLIBCXX_ROOT="/usr/include/c++/v1"` it worked like a charm.

---

## Comment 4

> Username: ldionne  
> Created at: 2015-06-18 16:45:55 UTC  
> Updated at: 2015-06-18 16:46:40 UTC  
> Url: https://github.com/boostorg/hana/issues/135#issuecomment-113214255  

@camio I think you're right that I'm doing things wrong. The purpose of `LIBCXX_ROOT` is both unclear from the `README` and it can also lead to incorrect results. My goal is to find the right include directory and the right `libc++.so` to link against. Normally, none of this should be necessary because just setting `CMAKE_CXX_COMPILER` properly should do the trick. However, on Linux, the default `libstdc++` that comes with the system is usually too old for Hana. That's why I decided to add this `LIBCXX_ROOT` hack in the first place, so you could override the standard library on Linux. I don't know how to do otherwise, any ideas?

---

## Comment 5

> Username: camio  
> Created at: 2015-06-18 20:20:08 UTC  
> Url: https://github.com/boostorg/hana/issues/135#issuecomment-113278428  

I made a pull request that properly handles the case when BOOST_HANA_ENABLE_LIBCXX is ON, but LIBCXX_ROOT is not defined. It will use the system installation of libc++ in that case.  
  
It seems like LIBCXX_ROOT should only be used when BOOST_HANA_ENABLE_LIBCXX is ON. That doesn't seem to be the case right now.  
  
For cases when there is no system installation of libcxx I presume we just need to link to the correct library and use the correct linker flags, build flags, and include directories?

---

## Comment 6

> Username: ldionne  
> Created at: 2015-06-19 00:23:19 UTC  
> Url: https://github.com/boostorg/hana/issues/135#issuecomment-113326517  

Like I said, I don't like my `LIBCXX_ROOT` hack. I think a better solution is to properly explain the issue and how to solve it on a case-per-case basis.

---

## Comment 7

> Username: ldionne  
> Created at: 2015-06-19 00:27:48 UTC  
> Url: https://github.com/boostorg/hana/issues/135#issuecomment-113327435  

I just wrote the above patch from my Ubuntu computer. It completely removes any arbitrary choice of a standard library inside the CMake files. It assumes that in the normal case where everything goes properly, the user has a recent compiler + standard library. Then, I updated the `README` to explain what to do when the system's standard library does not support C++14. If you guys could try to follow these instructions on your respective distros and tell me how that works, it would be awesome. _I also hope the Travis build will pass._

---

## Comment 8

> Username: ldionne  
> Created at: 2015-06-19 00:51:54 UTC  
> Url: https://github.com/boostorg/hana/issues/135#issuecomment-113331744  

Meh. I only compiled my tests and didn't run them. It won't find the `libc++.so.1` shared library on the Travis build. See [this](https://travis-ci.org/ldionne/hana/jobs/67456392#L1911); any idea what's going awry?

---

## Comment 9

> Username: ldionne  
> Created at: 2015-06-25 00:09:53 UTC  
> Url: https://github.com/boostorg/hana/issues/135#issuecomment-115051175  

Ok; so the workaround is not exactly pretty, but at least Hana does not make any arbitrary choices regarding the standard library that's used.
