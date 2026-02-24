# #403 - boost.fusion and variadic template [Closed]

> Username: xiangfan-ms  
> Created at: 2018-05-29 21:36:31 UTC  
> Updated at: 2018-05-30 22:43:18 UTC  
> Closed at: 2018-05-30 04:02:29 UTC  
> Url: https://github.com/boostorg/hana/issues/403  

This is no longer an issue in boost 1.67 but I think it is still worth reporting.  
  
In earlier version of boost (like 1.64), boost.fusion added a workaround for MSVC which will use the non-variadic-template version. However, some tests in boost.hana don't work when variadic template isn't used.  
  
For example, 'test\ext\boost\fusion\deque\auto\_specs.hpp' needs to define something like  
   #define FUSION_MAX_VECTOR_SIZE 50  
  
Otherwise, tests under 'test\ext\boost\fusion\deque\auto' will fail.  
  
BTW, I think there are other issues in the non-variadic-template version because it is now almost dead code, so I didn't dig deeper into it. I put some of my findings here: https://github.com/xiangfan-ms/hana/blob/master/include/boost/hana/config.hpp (search for BOOST_HANA_WORKAROUND_MSVC_BOOST_FUSION_DEQUE_SIZE).

---

## Comment 1

> Username: ldionne  
> Created at: 2018-05-30 04:02:29 UTC  
> Url: https://github.com/boostorg/hana/issues/403#issuecomment-393023365  

Thanks for the report. Hana doesn't officially support older versions of Boost (doing so is too much work because it requires systematically testing with all the supported versions of Boost). Because this is not officially supported, I won't move on this issue unless you tell me it is a significant problem on your side.

---

## Comment 2

> Username: xiangfan-ms  
> Created at: 2018-05-30 22:43:18 UTC  
> Url: https://github.com/boostorg/hana/issues/403#issuecomment-393343189  

Maybe you want to update this in the make file if only the latest version of boost is officially supported / tested?  
  
find_package(Boost 1.64)
