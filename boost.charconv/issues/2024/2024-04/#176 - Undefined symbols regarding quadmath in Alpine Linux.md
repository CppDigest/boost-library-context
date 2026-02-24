# #176 - Undefined symbols regarding quadmath in Alpine Linux [Closed]

> Username: anarthal  
> Created at: 2024-04-06 17:56:22 UTC  
> Updated at: 2024-04-11 07:12:52 UTC  
> Closed at: 2024-04-11 07:12:52 UTC  
> Url: https://github.com/boostorg/charconv/issues/176  

* System: Alpine Linux (as per `docker run -it alpine:3.18.2`).  
* Compiler: g++ 12.2  
* System packages:  
  
```  
apk update && apk add --no-cache \  
        g++ \  
        cmake \  
        ninja \  
        openssl-dev \  
        icu-dev \  
        git \  
        linux-headers \  
        wget \  
        libquadmath  
```  
  
* Boost version: Boost 1.85 RC1  
* Boost built using: `./b2 --with-charconv -d0 --prefix=/opt/boost install`  
* `cat main.cpp`:  
  
```  
#include <boost/charconv/from_chars.hpp>  
  
using namespace boost::charconv;  
  
int main()  
{  
        const char* buffer="21";  
        double v = 0.0;  
        from_chars(buffer, buffer + 2, v);  
  
}  
```  
  
* `cat CMakeLists.txt`:  
  
```  
cmake_minimum_required(VERSION 3.10.0)  
project(a)  
find_package(Boost REQUIRED COMPONENTS charconv)  
add_executable(main main.cpp)  
target_link_libraries(main PRIVATE Boost::charconv)  
```  
  
* Building fails with:  
  
```  
/usr/lib/gcc/x86_64-alpine-linux-musl/12.2.1/../../../../x86_64-alpine-linux-musl/bin/ld: /opt/boost/lib/libboost_charconv.so.1.85.0: undefined reference to `quadmath_snprintf'  
/usr/lib/gcc/x86_64-alpine-linux-musl/12.2.1/../../../../x86_64-alpine-linux-musl/bin/ld: /opt/boost/lib/libboost_charconv.so.1.85.0: undefined reference to `isnanq'  
/usr/lib/gcc/x86_64-alpine-linux-musl/12.2.1/../../../../x86_64-alpine-linux-musl/bin/ld: /opt/boost/lib/libboost_charconv.so.1.85.0: undefined reference to `strtoflt128'  
/usr/lib/gcc/x86_64-alpine-linux-musl/12.2.1/../../../../x86_64-alpine-linux-musl/bin/ld: /opt/boost/lib/libboost_charconv.so.1.85.0: undefined reference to `isinfq'  
```  
  
Some thing to note:  
  
* In Alpine, `libquadmath` is a separate package. Having the header != having the binary library.  
* Adding `target_link_libraries(... quadmath)` doesn't work even if the library is installed in `/usr/lib`. However, linking using the full library path works.  
* When building Boost, the config check `GCC libquadmath and __float128 support` says `no`. However, charconv seems to still include and use the header. `config.log` output:  
  
```  
...failed updating 0 target...  
/usr/lib/gcc/x86_64-alpine-linux-musl/12.2.1/../../../../x86_64-alpine-linux-musl/bin/ld: cannot find -lquadmath: No such file or directory  
collect2: error: ld returned 1 exit status  
```  
  
* Reading the code, the problem seems to stem from assuming that `BOOST_HAS_FLOAT128` is a synonym of having `libquadmath`, which seems to not be true here.

---

## Comment 1

> Username: mborland  
> Created at: 2024-04-08 06:37:14 UTC  
> Url: https://github.com/boostorg/charconv/issues/176#issuecomment-2041968411  

If you add `-DBOOST_CHARCONV_NO_QUADMATH=1` you can disable it manually. The issue here is you have the type, you have the header, and you're compiling with GNU extensions (implicit with CMake). There's no way for me to tell you've decided not to link to the library. I think the only way for me to fix this is to make quadmath support opt-in rather than opt-out through the previous macro.

---

## Comment 2

> Username: pdimov  
> Created at: 2024-04-08 09:49:05 UTC  
> Url: https://github.com/boostorg/charconv/issues/176#issuecomment-2042322213  

This  
  
> When building Boost, the config check GCC libquadmath and __float128 support says no. However, charconv seems to still include and use the header.  
  
seems wrong. If the config check fails, Charconv shouldn't try to enable quadmath support.

---

## Comment 3

> Username: mborland  
> Created at: 2024-04-08 09:59:14 UTC  
> Updated at: 2024-04-08 10:02:27 UTC  
> Url: https://github.com/boostorg/charconv/issues/176#issuecomment-2042341780  

> This  
>   
> > When building Boost, the config check GCC libquadmath and __float128 support says no. However, charconv seems to still include and use the header.  
>   
> seems wrong. If the config check fails, Charconv shouldn't try to enable quadmath support.  
  
The library will be built without quadmath support. When using the library with all the above conditions met this: https://github.com/boostorg/charconv/blob/develop/include/boost/charconv/detail/config.hpp#L30 will pull the header back in, and the templated routines will try and use the `__float128` specializations: https://github.com/boostorg/charconv/blob/develop/include/boost/charconv/detail/fallback_routines.hpp#L27

---

## Comment 4

> Username: mborland  
> Created at: 2024-04-08 10:07:59 UTC  
> Url: https://github.com/boostorg/charconv/issues/176#issuecomment-2042358696  

I could try and move everything into a new `float128.cpp` so it's only ever configured at build time. That would require anyone who built with `__float128` support to always need to link against libquadmath

---

## Comment 5

> Username: pdimov  
> Created at: 2024-04-08 10:09:15 UTC  
> Url: https://github.com/boostorg/charconv/issues/176#issuecomment-2042360798  

This implies that the library calls quadmath functions from the header-only part, rather than only from the compiled part. Is that the case, and why is it needed?

---

## Comment 6

> Username: pdimov  
> Created at: 2024-04-08 10:10:27 UTC  
> Updated at: 2024-04-08 10:11:16 UTC  
> Url: https://github.com/boostorg/charconv/issues/176#issuecomment-2042363190  

Sorry, didn't see your comment (and the edit); looks like you're proposing to move the calls from the headers into `flioat128.cpp`, which would indeed solve the issue if I'm not mistaken.

---

## Comment 7

> Username: mborland  
> Created at: 2024-04-08 10:18:50 UTC  
> Updated at: 2024-04-08 10:19:29 UTC  
> Url: https://github.com/boostorg/charconv/issues/176#issuecomment-2042378918  

> This implies that the library calls quadmath functions from the header-only part, rather than only from the compiled part. Is that the case  
  
Yes  
  
> and why is it needed?  
  
It doesn't really need to be anymore. I don't think I am going to add constexpr floating-point support even though it is viable with C++20. I could disable all the individual component tests, and basically just move everything related to floating point from include/detail/ folder into the src/ folder.

---

## Comment 8

> Username: anarthal  
> Created at: 2024-04-08 10:38:10 UTC  
> Url: https://github.com/boostorg/charconv/issues/176#issuecomment-2042414475  

I forgot to mention: even if you build with extensions disabled, the problem is still there. In the configuration I'm describing, this check passes:  
  
```  
#if defined(BOOST_HAS_FLOAT128) && !defined(__STRICT_ANSI__) && !defined(BOOST_CHARCONV_NO_QUADMATH)  
#  define BOOST_CHARCONV_HAS_FLOAT128  
#  include <quadmath.h>  
#endif  
```  
  
but the b2 config check does not, because `libquadmath` is not on the system. Now going into `fallback_routines.hpp`, which is included in the built library:  
  
```  
#ifdef BOOST_CHARCONV_HAS_FLOAT128  
inline int print_val(char* first, std::size_t size, char* format, __float128 value) noexcept  
{  
    return quadmath_snprintf(first, size, format, value);  
}  
#endif  
```  
  
So you end up with a call to `quadmath_snprintf` even if the b2 check failed (the Boost.Config check doesn't interact with the b2 check anyhow).  
  
So disabling CXX extensions on my side doesn't fix it, because the library is compiled with a call to quadmath.

---

## Comment 9

> Username: anarthal  
> Created at: 2024-04-08 10:39:05 UTC  
> Url: https://github.com/boostorg/charconv/issues/176#issuecomment-2042415973  

My use case only involves converting doubles, BTW.

---

## Comment 10

> Username: mborland  
> Created at: 2024-04-08 10:55:21 UTC  
> Updated at: 2024-04-08 10:56:41 UTC  
> Url: https://github.com/boostorg/charconv/issues/176#issuecomment-2042444760  

> So disabling CXX extensions on my side doesn't fix it, because the library is compiled with a call to quadmath.  
  
Disabling CXX extensions is a now fixed bug in CMake. You would think `CXX_EXTENSIONS OFF` would mean `-std=c++XX` instead of `-std=gnu++XX`, but that was not the case: https://gitlab.kitware.com/cmake/cmake/-/merge_requests/6177. By using `-std=c++XX` instead of `-std=gnu++XX` you would have `__STRICT_ANSI__` defined thus making `BOOST_CHARCONV_HAS_FLOAT128` undefined.

---

## Comment 11

> Username: anarthal  
> Created at: 2024-04-08 11:30:14 UTC  
> Url: https://github.com/boostorg/charconv/issues/176#issuecomment-2042508679  

If you also set `CMAKE_CXX_STANDARD` (which was my case in the original project) cmake will use `std=c++xx`, and the issue will still show.  
  
I've put together a self-contained repro. You can clone it and run `docker build .` to see it. https://github.com/anarthal/charconv-repro-176

---

## Comment 12

> Username: anarthal  
> Created at: 2024-04-08 11:30:37 UTC  
> Url: https://github.com/boostorg/charconv/issues/176#issuecomment-2042509401  

The repro builds with `ninja -v` to show the command line used.
