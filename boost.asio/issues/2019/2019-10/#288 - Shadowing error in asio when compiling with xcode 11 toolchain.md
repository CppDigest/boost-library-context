# #288 - Shadowing error in asio when compiling with xcode 11 toolchain [Closed]

> Username: WillerZ  
> Created at: 2019-10-08 17:26:33 UTC  
> Updated at: 2020-12-30 01:09:46 UTC  
> Closed at: 2020-12-30 01:09:45 UTC  
> Url: https://github.com/boostorg/asio/issues/288  

When I compile my asio-using project after installing xcode 11 it fails with this error messaging:  
```  
In file included from third-party/boost/boost/boost/asio.hpp:80:  
In file included from third-party/boost/boost/boost/asio/ip/basic_resolver.hpp:28:  
In file included from third-party/boost/boost/boost/asio/ip/basic_resolver_query.hpp:21:  
In file included from third-party/boost/boost/boost/asio/ip/resolver_query_base.hpp:19:  
third-party/boost/boost/boost/asio/ip/resolver_base.hpp:69:5: error: declaration shadows a variable in namespace 'boost::asio::ip' [-Werror,-Wshadow]  
    v4_mapped = BOOST_ASIO_OS_DEF(AI_V4MAPPED),  
    ^  
third-party/boost/boost/boost/asio/ip/address_v6.hpp:291:20: note: previous declaration is here  
enum v4_mapped_t { v4_mapped };  
                   ^  
1 error generated.  
```  
  
Locally I have fixed this for my project by turning off the shadow warning while the boost headers are being read, like this:  
```  
#pragma GCC diagnostic push  
#pragma GCC diagnostic ignored "-Wshadow"  
#include <boost/asio.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <boost/asio/ssl.hpp>  
#include <boost/enable_shared_from_this.hpp>  
#pragma GCC diagnostic pop  
```  
  
To fix it within asio the options I'm aware of are:  
1. Wrap the definition of `v4_mapped` within resolver_base in a similar warning-suppression block.  
2. Change the name of either of the `v4_mapped` symbols to something else - this is API-breaking as both symbols are public

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 01:09:44 UTC  
> Url: https://github.com/boostorg/asio/issues/288#issuecomment-752292974  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#721](https://github.com/chriskohlhoff/asio/issues/721).
