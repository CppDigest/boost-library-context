# #51 Add a Boost-friendly subproject case to CMakeLists [Merged]

> Username: pdimov  
> Created at: 2021-06-04 18:12:40 UTC  
> Updated at: 2021-06-05 22:59:39 UTC  
> Merged at: 2021-06-05 21:58:24 UTC  
> Closed at: 2021-06-05 21:58:24 UTC  
> Url: https://github.com/boostorg/stl_interfaces/pull/51  



---

## Comment 1

> Username: pdimov  
> Created_at: 2021-06-04 18:20:39 UTC  
> Url: https://github.com/boostorg/stl_interfaces/pull/51#issuecomment-854917956  

It should be possible to update `cmake/dependencies.cmake` to use `FetchContent` for only the required Boost dependencies nowadays, if you like this sort of thing. :-)

---

## Comment 2

> Username: tzlaine  
> Created_at: 2021-06-05 21:56:10 UTC  
> Url: https://github.com/boostorg/stl_interfaces/pull/51#issuecomment-855301020  

Thanks, Peter.  Where can I find FetchContent?  I assume that's a thing you've created?

---

## Comment 3

> Username: pdimov  
> Created_at: 2021-06-05 22:05:56 UTC  
> Updated_at: 2021-06-05 22:12:15 UTC  
> Url: https://github.com/boostorg/stl_interfaces/pull/51#issuecomment-855302089  

`FetchContent` is a standard CMake module in ~3.9~ 3.11 and up: https://cmake.org/cmake/help/latest/module/FetchContent.html

---

## Comment 4

> Username: tzlaine  
> Created_at: 2021-06-05 22:59:39 UTC  
> Url: https://github.com/boostorg/stl_interfaces/pull/51#issuecomment-855307299  

Thanks!  I had no idea.

---
