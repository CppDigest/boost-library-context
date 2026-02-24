# #47 - strong using is deprecated [Closed]

> Username: Kojoley  
> Created at: 2017-12-29 18:38:02 UTC  
> Updated at: 2017-12-29 22:42:46 UTC  
> Closed at: 2017-12-29 22:12:14 UTC  
> Url: https://github.com/boostorg/log/issues/47  

```  
$ gcc-7 --version  
gcc-7 (Ubuntu 7.2.0-1ubuntu1~14.04) 7.2.0  
```  
```  
In file included from ../../../boost/log/detail/custom_terminal_spec.hpp:25:0,  
                 from core/custom_terminal.cpp:8:  
../../../boost/log/detail/config.hpp:358:27: error: strong using is deprecated; use inline namespaces instead [-Werror=deprecated]  
 __attribute__((__strong__))  
                           ^  
cc1plus: all warnings being treated as errors  
    "g++-7"   -O3 -finline-functions -Wno-inline -Wall -Werror -pthread -fPIC -m64 -std=c++03  -DBOOST_ALL_NO_LIB=1 -DNDEBUG  -I"../../.." -c -o "../../../bin.v2/libs/phoenix/test/custom_terminal.test/gcc-gnu-7/release/cxxstd-03-iso/threadapi-pthread/threading-multi/core/custom_terminal.o" "core/custom_terminal.cpp"  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2017-12-29 22:12:14 UTC  
> Url: https://github.com/boostorg/log/issues/47#issuecomment-354506414  

This attribute is only used in C++03 mode because there is no other way to emulate C++11 inline namespaces. If gcc removes the attribute, I will condition its use on that version of gcc. Until then I intend to leave the attribute as is.  
  
My advice is to switch to C++11 or later with newer compilers.

---

## Comment 2

> Username: Lastique  
> Created at: 2017-12-29 22:40:42 UTC  
> Url: https://github.com/boostorg/log/issues/47#issuecomment-354508733  

I've silenced the warning for now (https://github.com/boostorg/log/commit/f422e1bb8acaabeb331231fde1af2b7fc865d1a6) and reported a [gcc bug](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=83624).

---

## Comment 3

> Username: Kojoley  
> Created at: 2017-12-29 22:42:46 UTC  
> Url: https://github.com/boostorg/log/issues/47#issuecomment-354508919  

Thanks!
