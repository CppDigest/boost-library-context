# #455 - Configuration doesn't work correctly with clang on Linux [Closed]

> Username: mojca  
> Created at: 2022-11-12 17:19:09 UTC  
> Updated at: 2022-11-20 19:45:52 UTC  
> Closed at: 2022-11-20 19:45:52 UTC  
> Url: https://github.com/boostorg/config/issues/455  

I seem to have troubles using `config` with `clang++-14` on Ubuntu 22.04 running Boost 1.80.0:  
* `BOOST_NO_CXX98_FUNCTION_BASE` is not defined  
* `BOOST_LIBSTDCXX_VERSION` is too low  
  
See: https://github.com/boostorg/container_hash/issues/22#issuecomment-1312526022

---

## Comment 1

> Username: pdimov  
> Created at: 2022-11-12 17:22:37 UTC  
> Url: https://github.com/boostorg/config/issues/455#issuecomment-1312532325  

Maybe we should use `_GLIBCXX_RELEASE` when defined under Clang, instead of `__has_include`.  
  
"_GLIBCXX_RELEASE  
  
The major release number for libstdc++. This macro is defined to the GCC major version that the libstdc++ headers belong to, as an integer constant. When compiling with GCC it has the same value as GCC's pre-defined macro __GNUC__. This macro can be used when libstdc++ is used with a non-GNU compiler where __GNUC__ is not defined, or has a different value that doesn't correspond to the libstdc++ version. This macro first appeared in the GCC 7.1 release and is not defined for GCC 6.x or older releases."

---

## Comment 2

> Username: jzmaddock  
> Created at: 2022-11-12 17:27:48 UTC  
> Url: https://github.com/boostorg/config/issues/455#issuecomment-1312533422  

Cool, I had no idea they had added that macro, and yes using that when present looks good.  
  
@mojca can you investigate why `__has_include(<expected>)` is failing?

---

## Comment 3

> Username: mojca  
> Created at: 2022-11-12 17:47:03 UTC  
> Url: https://github.com/boostorg/config/issues/455#issuecomment-1312537052  

> @mojca can you investigate why `__has_include(<expected>)` is failing?  
  
Hmmm ... it actually works.  
  
I guess that must be the reason: https://github.com/boostorg/config/pull/445  
  
It was added in August and it hasn't been released in Boost 1.80.0 yet.  
  
Though it's still not clear to me why `BOOST_LIBSTDCXX_VERSION` needs to be checked rather than just the C++ standard.  
  
Also, `_GLIBCXX_RELEASE` returns 12 and that seems like a lot more reliable and robust way to check for the libstdc++ version compared to the other hacks checking for headers.

---

## Comment 4

> Username: mojca  
> Created at: 2022-11-12 17:48:05 UTC  
> Updated at: 2022-11-12 17:48:15 UTC  
> Url: https://github.com/boostorg/config/issues/455#issuecomment-1312537224  

A similar problem for `libc++`.  
  
The source code currently says:  
```c++  
#if _LIBCPP_VERSION >= 15000  
//  
// Unary function is now deprecated in C++11 and later:  
//  
#if __cplusplus >= 201103L  
#define BOOST_NO_CXX98_FUNCTION_BASE  
#endif  
#endif  
```  
but my `_LIBCPP_VERSION` returns 14000 which is smaller and I'll run into the exact same issue with libc++.

---

## Comment 5

> Username: mojca  
> Created at: 2022-11-12 18:12:19 UTC  
> Url: https://github.com/boostorg/config/issues/455#issuecomment-1312542126  

I tested it now though and it seems that libc++ 14 in fact doesn't complain about the deprecated function (even if it technically is).
