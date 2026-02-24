# #597 - Setting arch to combined when running b2 on apple silicon generates only x86 library [Open]

> Username: vinayakr  
> Created at: 2021-12-20 05:28:52 UTC  
> Updated at: 2021-12-20 05:28:52 UTC  
> Url: https://github.com/boostorg/boost/issues/597  

When setting the command line arch=combined on a macbook pro m1 max, the library generated is x86_64 only.   
```vinayakr@Vinayaks-MacBook-Pro boost % b2  --with-regex --with-thread --with-chrono architecture=combined address-model=64 cxxflags="-arch x86_64 -arch arm64" toolset=gcc variant=debug link=static install --prefix=/Users/vinayakr/workspace/project2_threading/ext/boostinstall  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached) [1]  
    - default architecture     : x86 (cached) [1]  
    - symlinks supported       : yes (cached)  
    - has_icu builds           : no  (cached) [2]  
    - lockfree boost::atomic_flag : no  (cached) [2]  
  
[1] gcc-13  
[2] gcc-13/debug/architecture-combined/link-static/python-2.7/threadapi-pthread/threading-multi/visibility-hidden  
  
Component configuration:  
  
    - atomic                   : not building  
    - chrono                   : building  
    - container                : not building  
    - context                  : not building  
    - contract                 : not building  
    - coroutine                : not building  
    - date_time                : not building  
    - exception                : not building  
    - fiber                    : not building  
    - filesystem               : not building  
    - graph                    : not building  
    - graph_parallel           : not building  
    - headers                  : not building  
    - iostreams                : not building  
    - json                     : not building  
    - locale                   : not building  
    - log                      : not building  
    - math                     : not building  
    - mpi                      : not building  
    - nowide                   : not building  
    - program_options          : not building  
    - python                   : not building  
    - random                   : not building  
    - regex                    : building  
    - serialization            : not building  
    - stacktrace               : not building  
    - system                   : not building  
    - test                     : not building  
    - thread                   : building  
    - timer                    : not building  
    - type_erasure             : not building  
    - wave                     : not building  
  
...patience...  
...patience...  
...found 34822 targets...  
...updating 4 targets...  
common.copy /Users/vinayakr/workspace/project2_threading/ext/boostinstall/lib/libboost_chrono.a  
common.copy /Users/vinayakr/workspace/project2_threading/ext/boostinstall/lib/libboost_regex.a  
common.copy /Users/vinayakr/workspace/project2_threading/ext/boostinstall/lib/libboost_atomic.a  
common.copy /Users/vinayakr/workspace/project2_threading/ext/boostinstall/lib/libboost_thread.a  
...updated 4 targets...  
vinayakr@Vinayaks-MacBook-Pro boost % lipo -archs ../../../boostinstall/lib/libboost_regex.a  
x86_64  
```  
  
If the architecture is set to arm, they it properly generates arm64  
```  
vinayakr@Vinayaks-MacBook-Pro boost % b2  --with-regex --with-thread --with-chrono architecture=arm address-model=64 cxxflags="-arch x86_64 -arch arm64" toolset=gcc variant=debug link=static install --prefix=/Users/vinayakr/workspace/project2_threading/ext/boostinstall  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached) [1]  
    - default architecture     : x86 (cached) [1]  
    - symlinks supported       : yes (cached)  
    - has_icu builds           : no  (cached) [2]  
    - lockfree boost::atomic_flag : no  (cached) [2]  
  
[1] gcc-13  
[2] gcc-13/debug/architecture-arm/link-static/python-2.7/threadapi-pthread/threading-multi/visibility-hidden  
  
Component configuration:  
  
    - atomic                   : not building  
    - chrono                   : building  
    - container                : not building  
    - context                  : not building  
    - contract                 : not building  
    - coroutine                : not building  
    - date_time                : not building  
    - exception                : not building  
    - fiber                    : not building  
    - filesystem               : not building  
    - graph                    : not building  
    - graph_parallel           : not building  
    - headers                  : not building  
    - iostreams                : not building  
    - json                     : not building  
    - locale                   : not building  
    - log                      : not building  
    - math                     : not building  
    - mpi                      : not building  
    - nowide                   : not building  
    - program_options          : not building  
    - python                   : not building  
    - random                   : not building  
    - regex                    : building  
    - serialization            : not building  
    - stacktrace               : not building  
    - system                   : not building  
    - test                     : not building  
    - thread                   : building  
    - timer                    : not building  
    - type_erasure             : not building  
    - wave                     : not building  
  
...patience...  
...patience...  
...found 34822 targets...  
...updating 4 targets...  
common.copy /Users/vinayakr/workspace/project2_threading/ext/boostinstall/lib/libboost_chrono.a  
common.copy /Users/vinayakr/workspace/project2_threading/ext/boostinstall/lib/libboost_atomic.a  
common.copy /Users/vinayakr/workspace/project2_threading/ext/boostinstall/lib/libboost_regex.a  
common.copy /Users/vinayakr/workspace/project2_threading/ext/boostinstall/lib/libboost_thread.a  
...updated 4 targets...  
vinayakr@Vinayaks-MacBook-Pro boost % lipo -archs ../../../boostinstall/lib/libboost_regex.a  
arm64  
```
