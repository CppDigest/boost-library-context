# #188 - Build fails when RTTI is disabled [Closed]

> Username: joaosigma  
> Created at: 2021-07-16 13:55:55 UTC  
> Updated at: 2021-08-07 22:06:12 UTC  
> Closed at: 2021-08-07 22:06:12 UTC  
> Url: https://github.com/boostorg/container/issues/188  

Trying to compile Boost 1.76 with "-fno-rtti" (and with BOOST_NO_RTTI and BOOST_NO_TYPEID defined) results in the following errors:  
```  
libs/container/src/monotonic_buffer_resource.cpp:169:19: error: use of dynamic_cast requires -frtti  
{  return this == dynamic_cast<const monotonic_buffer_resource*>(&other);  }  
  
libs/container/src/pool_resource.cpp:241:19: error: use of dynamic_cast requires -frtti  
{  return this == dynamic_cast<const pool_resource*>(&other);  }  
  
libs/container/src/synchronized_pool_resource.cpp:90:19: error: use of dynamic_cast requires -frtti  
{  return this == dynamic_cast<const synchronized_pool_resource*>(&other);  }  
  
libs/container/src/unsynchronized_pool_resource.cpp:58:19: error: use of dynamic_cast requires -frtti  
{  return this == dynamic_cast<const unsynchronized_pool_resource*>(&other);  }  
```  
  
The entire build prompt is:  
```  
-c -x c++ -fvisibility-inlines-hidden --target=armv7-a-linux-androideabi21 -march=armv7-a -mthumb -mfpu=vfpv3-d16 -fno-addrsig -stdlib=libc++ -Oz -fPIC -g -DNDEBUG -ffunction-sections -fdata-sections -fno-stack-protector -funwind-tables -Wno-unused-local-typedefs -Wno-unused-command-line-argument -fno-rtti -fexceptions  -pthread -O3 -Wall -fvisibility=hidden -Wno-inline -std=c++17 -DBOOST_ALL_NO_LIB=1 -DBOOST_ASIO_NO_TYPEID -DBOOST_CONTAINER_STATIC_LINK=1 -DBOOST_DISABLE_ASSERTS -DBOOST_NO_RTTI -DBOOST_NO_TYPEID -DBOOST_REGEX_NO_FASTCALL -DBOOST_SYSTEM_NO_DEPRECATED -DNDEBUG -I  
```

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-08-07 22:04:32 UTC  
> Url: https://github.com/boostorg/container/issues/188#issuecomment-894711635  

Thanks for the report. dynamic_cast was on the specification, but it's not necessary, just like reported in:  
  
https://cplusplus.github.io/LWG/issue3000  
  
so I'll remove those dynamic casts following the proposed resolution
