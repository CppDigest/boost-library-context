# #56 docs(README.md): mention CMake's new FetchContent and EXCLUDE_FROM_ALL support [Merged]

> Username: pratikpc  
> Created at: 2024-04-07 12:22:55 UTC  
> Updated at: 2024-04-08 11:10:03 UTC  
> Merged at: 2024-04-08 11:10:03 UTC  
> Closed at: 2024-04-08 11:10:03 UTC  
> Url: https://github.com/boostorg/cmake/pull/56  

Makes it easier to use [EXCLUDE_FROM_ALL in the newer versions of CMake with FetchContent](https://cmake.org/cmake/help/latest/module/FetchContent.html)  
  
Closes #55

---

## Comment 1

> Username: pratikpc  
> Created_at: 2024-04-07 12:29:09 UTC  
> Url: https://github.com/boostorg/cmake/pull/56#issuecomment-2041454547  

@pdimov Hi I have retained compatibility with the existing old CMake approach because a lot of distros including [Fedora 39](https://packages.fedoraproject.org/pkgs/cmake/cmake/) apparently still ship with the older version of CMake  
  
This probably ensures compatibility with older workflows

---

## Comment 2

> Username: pratikpc  
> Created_at: 2024-04-07 12:32:48 UTC  
> Url: https://github.com/boostorg/cmake/pull/56#issuecomment-2041455596  

Should we also document [FetchContent's OVERRIDE_FIND_PACKAGE](https://cmake.org/cmake/help/latest/module/FetchContent.html) which would help users use FetchContent + Boost + Find Package approach instead of the current somewhat clumsy approach in a few future PR?

---

## Comment 3

> Username: pdimov  
> Created_at: 2024-04-08 11:09:55 UTC  
> Url: https://github.com/boostorg/cmake/pull/56#issuecomment-2042471087  

Possibly, yes. I haven't tried OVERRIDE_FIND_PACKAGE so I don't know how well it works in practice. In this specific case it will probably not work reliably as `find_package(Boost)` is generally followed by linking to `Boost::boost` or `Boost::headers` targets, making all header-only Boost libraries available, but this is not going to work for FetchContent-ed Boost.

---
