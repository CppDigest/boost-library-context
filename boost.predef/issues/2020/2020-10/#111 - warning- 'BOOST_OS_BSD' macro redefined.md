# #111 - warning: 'BOOST_OS_BSD' macro redefined [Closed]

> Username: brad0  
> Created at: 2020-10-26 01:33:05 UTC  
> Updated at: 2021-01-04 20:16:52 UTC  
> Closed at: 2021-01-04 20:16:52 UTC  
> Url: https://github.com/boostorg/predef/issues/111  

This is Boost 1.70.  
  
```  
In file included from uuid-utils.cc:33:  
In file included from /usr/local/include/boost/uuid/uuid_generators.hpp:17:  
In file included from /usr/local/include/boost/uuid/random_generator.hpp:21:  
In file included from /usr/local/include/boost/uuid/detail/random_provider.hpp:29:  
In file included from /usr/local/include/boost/uuid/detail/random_provider_detect_platform.hpp:16:  
/usr/local/include/boost/predef/os/bsd/open.h:82:16: warning: 'BOOST_OS_BSD' macro redefined [-Wmacro-redefined]  
#       define BOOST_OS_BSD BOOST_VERSION_NUMBER_AVAILABLE  
               ^  
/usr/local/include/boost/predef/os/bsd.h:59:9: note: previous definition is here  
#define BOOST_OS_BSD BOOST_VERSION_NUMBER_NOT_AVAILABLE  
        ^  
1 warning generated.  
```
