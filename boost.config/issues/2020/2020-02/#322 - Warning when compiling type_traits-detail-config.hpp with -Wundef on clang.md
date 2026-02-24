# #322 - Warning when compiling type_traits/detail/config.hpp with -Wundef on clang [Closed]

> Username: jredmondson  
> Created at: 2020-02-17 23:40:20 UTC  
> Updated at: 2021-07-04 16:21:00 UTC  
> Closed at: 2021-07-04 16:21:00 UTC  
> Url: https://github.com/boostorg/config/issues/322  

When compiling with -Wundef on clang we get the following warning when using/including type_traits/detail/config.hpp:  
  
```  
In file included from /x/boost/iostreams/stream.hpp:20:  
In file included from /x/boost/iostreams/detail/select.hpp:32:  
In file included from /x/boost/type_traits/is_base_and_derived.hpp:12:  
In file included from x/boost/type_traits/intrinsics.hpp:16:  
 [x/boost/type_traits/detail/config.hpp:85:52:  [0m [0;1;31merror:  [0m [1m'__clang_major___WORKAROUND_GUARD' is not defined, evaluates to 0 [-Werror,-Wundef] [0m  
      && !BOOST_WORKAROUND(BOOST_MSVC, < 1900) && !BOOST_WORKAROUND(__clang_major__, <= 4)  
 [0;1;32m                                                   ^  
 [0m [1m/x/boost/config/workaround.hpp:246:10:  [0m [0;1;30mnote:  [0mexpanded from macro 'BOOST_WORKAROUND' [0m  
       ((symbol ## _WORKAROUND_GUARD + 0 == 0) &&     \  
 [0;1;32m         ^  
 [0m [1m<scratch space>:17:1:  [0m [0;1;30mnote:  [0mexpanded from here [0m  
__clang_major___WORKAROUND_GUARD  
```  
  
I'm not sure that the usage of this in type_traits/detail/config.hpp is correct, but the issue is that `__clang_major__` + WORKAROUND_GUARD is being compared here without first checking that it has been defined and thus I get an error in environments where I am forced to use a strict clang (no undefs). My fix for this in a 1.70 base is similar to #186 in that the following can be added to config/workaround.hpp (tested in a UE4 plugin project that includes Boost libs and headers):  
  
```  
#ifndef __clang_major__  
#define __clang_major___WORKAROUND_GUARD 1  
#else  
#define __clang_major___WORKAROUND_GUARD 0  
#endif  
```  
  
Similar change to any other versions should also work. Thanks!

---

## Comment 1

> Username: Andor233  
> Created at: 2020-12-10 06:42:56 UTC  
> Url: https://github.com/boostorg/config/issues/322#issuecomment-742278430  

I got same problem, thank you bro
