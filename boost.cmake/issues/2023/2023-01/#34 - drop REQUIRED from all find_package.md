# #34 - drop REQUIRED from all find_package [Open]

> Username: marakew  
> Created at: 2023-01-08 00:01:15 UTC  
> Updated at: 2025-12-14 08:20:21 UTC  
> Url: https://github.com/boostorg/cmake/issues/34  

can you please drop it  
let rewrite it by CMAKE_DISABLE_FIND_PACKAGE_<NAME>  
thanks

---

## Comment 1

> Username: pdimov  
> Created at: 2023-01-08 01:40:10 UTC  
> Url: https://github.com/boostorg/cmake/issues/34#issuecomment-1374682325  

What specific problem are you having?

---

## Comment 2

> Username: marakew  
> Created at: 2023-01-08 17:11:43 UTC  
> Url: https://github.com/boostorg/cmake/issues/34#issuecomment-1374883579  

i want set manual external library instead use find_package  
  
as example  
set(CMAKE_DISABLE_FIND_PACKAGE_ZLIB 1)  
set(ZLIB_FOUND 1)  
add_library(ZLIB::ZLIB UNKNOWN IMPORTED)  
...etc  
  
but with REQUIRED keyword i got always fail to processing cmake

---

## Comment 3

> Username: pdimov  
> Created at: 2023-01-08 17:17:43 UTC  
> Url: https://github.com/boostorg/cmake/issues/34#issuecomment-1374884781  

Does this refer to a Boost built with `b2` or to one built with CMake?

---

## Comment 4

> Username: pdimov  
> Created at: 2023-01-08 17:57:09 UTC  
> Url: https://github.com/boostorg/cmake/issues/34#issuecomment-1374891778  

If with CMake, the config files don't use `find_package` or `REQUIRED`; they use `find_dependency` (e.g. `find_dependency(ZLIB)`. This macro automatically sets `QUIET` or `REQUIRED` to match the parent `find_package` call. So if you call `find_package(boost_iostreams REQUIRED)`, it will invoke `find_package(ZLIB REQUIRED)`, but if you call `find_package(boost_iostreams)`, it will invoke `find_package(ZLIB)`. This is standard CMake behavior.

---

## Comment 5

> Username: pdimov  
> Created at: 2023-01-08 18:24:50 UTC  
> Url: https://github.com/boostorg/cmake/issues/34#issuecomment-1374897308  

Opened a CMake issue about this at https://gitlab.kitware.com/cmake/cmake/-/issues/24293.

---

## Comment 6

> Username: marakew  
> Created at: 2023-01-08 18:33:36 UTC  
> Url: https://github.com/boostorg/cmake/issues/34#issuecomment-1374898947  

you seems dont understand me  
i don't want use find_package at all  
i want rewrite it by my own infrastructure  
with REQUIRED in boost it will impossible  
  
my above example will  work it REQUIRED will drop from find_package  
and users will able to rewrite by own env settings without using  
find_package for searching  
  
2023-01-08 19:57 GMT+02:00, Peter Dimov ***@***.***>:  
> If with CMake, the config files don't use `find_package` or `REQUIRED`; they  
> use `find_dependency` (e.g. `find_dependency(ZLIB)`. This macro  
> automatically sets `QUIET` or `REQUIRED` to match the parent `find_package`  
> call. So if you call `find_package(boost_iostreams REQUIRED)`, it will  
> invoke `find_package(ZLIB REQUIRED)`, but if you call  
> `find_package(boost_iostreams)`, it will invoke `find_package(ZLIB)`. This  
> is standard CMake behavior.  
>  
> --  
> Reply to this email directly or view it on GitHub:  
> https://github.com/boostorg/cmake/issues/34#issuecomment-1374891778  
> You are receiving this because you authored the thread.  
>  
> Message ID: ***@***.***>

---

## Comment 7

> Username: pdimov  
> Created at: 2023-01-08 23:25:44 UTC  
> Updated at: 2023-01-08 23:26:12 UTC  
> Url: https://github.com/boostorg/cmake/issues/34#issuecomment-1374958322  

So, your complaint is not about `find_dependency(ZLIB)` in the installed CMake config files, but (for example) about this use of `find_package`:  
  
https://github.com/boostorg/iostreams/blob/d1c1114a611afb090a007c9ff3e94efbcb914ef3/CMakeLists.txt#L30

---

## Comment 8

> Username: marakew  
> Created at: 2023-01-09 21:08:09 UTC  
> Url: https://github.com/boostorg/cmake/issues/34#issuecomment-1376316865  

yes  
and all another "required" with may present or will present in  
boost/cmake infrastructure  
just remove it to allow users ovewrite by own libs settings  
  
2023-01-09 1:25 GMT+02:00, Peter Dimov ***@***.***>:  
> So, your complaint is not above `find_dependency(ZLIB)` in the installed  
> CMake config files, but (for example) about this use of `find_package`:  
>  
> https://github.com/boostorg/iostreams/blob/d1c1114a611afb090a007c9ff3e94efbcb914ef3/CMakeLists.txt#L30  
>  
> --  
> Reply to this email directly or view it on GitHub:  
> https://github.com/boostorg/cmake/issues/34#issuecomment-1374958322  
> You are receiving this because you authored the thread.  
>  
> Message ID: ***@***.***>

---

## Comment 9

> Username: nigels-com  
> Created at: 2025-12-14 08:20:21 UTC  
> Url: https://github.com/boostorg/cmake/issues/34#issuecomment-3650468332  

There was a change made to [boost::iostreams](https://github.com/boostorg/iostreams/pull/176) that seems relevant here.
