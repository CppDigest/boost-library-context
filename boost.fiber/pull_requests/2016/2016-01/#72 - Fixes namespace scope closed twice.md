# #72 Fixes namespace scope closed twice. [Closed]

> Username: nullsauce  
> Created at: 2016-01-11 15:21:41 UTC  
> Updated at: 2016-06-06 11:36:32 UTC  
> Closed at: 2016-01-11 16:41:06 UTC  
> Url: https://github.com/boostorg/fiber/pull/72  

MSVC was failing to resolve some types in [fiber/future/future.hpp](https://github.com/olk/boost-fiber/blob/3a75f00f10261f1be160621fca7223849ff945e4/include/boost/fiber/future/future.hpp#L96). It looks like the boost::fibers::detail scope is beeing closed twice. The offending brace seems to be at [line 96](https://github.com/olk/boost-fiber/blob/3a75f00f10261f1be160621fca7223849ff945e4/include/boost/fiber/future/future.hpp#L96)

---

## Comment 1

> Username: olk  
> Created_at: 2016-01-11 16:41:06 UTC  
> Url: https://github.com/boostorg/fiber/pull/72#issuecomment-170612238  

Thank you, the bug is already solved in branch develop.

---
