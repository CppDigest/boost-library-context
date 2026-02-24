# #376 - Boost defines BOOST_HAS_FLOAT128 when it is not supported in clang [Closed]

> Username: lvella  
> Created at: 2021-03-19 15:29:38 UTC  
> Updated at: 2021-03-22 17:30:14 UTC  
> Closed at: 2021-03-22 17:29:47 UTC  
> Url: https://github.com/boostorg/config/issues/376  

Using the compiler combination clang-11 + glibc 2.31 on AArch64 Linux, macro `BOOST_HAS_FLOAT128` is defined even when it shouldn't be, and triggers compilation error `__float128 is not supported on this target` on header file `boost/config/detail/suffix.hpp:510`.  
  
It seems the issue originates at file `boost/config/compiler/clang.hpp`:  
```c++  
#if (defined(linux) || defined(__linux) || defined(__linux__) || defined(__GNU__) || defined(__GLIBC__)) && !defined(_CRAYC)  
#if (__clang_major__ >= 4) && defined(__has_include)  
#if __has_include(<quadmath.h>)  
#  define BOOST_HAS_FLOAT128  
#endif  
#endif  
#endif  
```  
  
Header `quadmath.h` is indeed available, but it doesn't mean `__float128` is supported, it was just installed alongside gcc in this system. Needlessly, it seems, because gcc does not support `__float128` either, and switching to gcc on the same system, `BOOST_HAS_FLOAT128` is correctly ***not*** defined and I get no compilation error on `boost/config/detail/suffix.hpp`. Unfortunately, I don't know of a better way to check for `__float128` support in Clang.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-03-19 17:38:36 UTC  
> Url: https://github.com/boostorg/config/issues/376#issuecomment-803000165  

Can you give https://github.com/boostorg/config/pull/377 a try and let me know if this fixes the issue?

---

## Comment 2

> Username: lvella  
> Created at: 2021-03-22 15:37:17 UTC  
> Url: https://github.com/boostorg/config/issues/376#issuecomment-804159377  

@jzmaddock Yes, it fixes the issue.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2021-03-22 17:30:14 UTC  
> Url: https://github.com/boostorg/config/issues/376#issuecomment-804254305  

Thanks for testing that out!
