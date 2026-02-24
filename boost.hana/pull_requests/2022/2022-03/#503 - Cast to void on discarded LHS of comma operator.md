# #503 Cast to void on discarded LHS of comma operator [Merged]

> Username: ecatmur  
> Created at: 2022-03-29 11:24:47 UTC  
> Updated at: 2022-10-21 02:19:11 UTC  
> Merged at: 2022-10-21 02:19:10 UTC  
> Closed at: 2022-10-21 02:19:10 UTC  
> Url: https://github.com/boostorg/hana/pull/503  

to avoid warnings in clang 14:  
```  
In file included from test/headers/boost/hana/any.cpp:1:  
In file included from include/boost/hana/any.hpp:15:  
In file included from include/boost/hana/any_of.hpp:16:  
In file included from include/boost/hana/at.hpp:16:  
In file included from include/boost/hana/concept/iterable.hpp:20:  
In file included from include/boost/hana/drop_front.hpp:20:  
In file included from include/boost/hana/integral_constant.hpp:13:  
In file included from include/boost/hana/bool.hpp:17:  
In file included from include/boost/hana/core/to.hpp:21:  
include/boost/hana/core/make.hpp:35:28: error: left operand of comma operator has no effect [-Werror,-Wunused-value]  
            static_assert((sizeof...(X), false),  
                           ^~~~~~~~~~~~  
```

---

## Comment 1

> Username: ldionne  
> Created_at: 2022-03-31 17:15:44 UTC  
> Url: https://github.com/boostorg/hana/pull/503#issuecomment-1084873471  

Thanks! Same as the other PR -- can you please rebase onto `develop` and re-push?

---
