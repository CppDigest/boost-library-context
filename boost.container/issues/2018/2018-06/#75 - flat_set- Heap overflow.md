# #75 - flat_set: Heap overflow [Closed]

> Username: gjasny  
> Created at: 2018-06-22 17:33:56 UTC  
> Updated at: 2018-06-26 19:51:40 UTC  
> Closed at: 2018-06-26 19:51:40 UTC  
> Url: https://github.com/boostorg/container/issues/75  

Hello,  
  
the following code results in a heap buffer overflow detected by ASAN:  
```  
#include <cstdint>  
#include <boost/container/flat_set.hpp>  
  
using PacketSet = boost::container::flat_set<std::uint32_t>;  
  
int main(int argc, const char * argv[]) {  
  
    PacketSet a{1u};  
    PacketSet b{1u, 2u};  
    PacketSet c{1u, 2u, 4u};  
    PacketSet d{1u, 2u, 3u, 4u, 5u};  
  
    PacketSet final;  
  
    final.merge(a);  
    final.merge(b);  
    final.merge(c);  
    final.merge(d);  
  
    return 0;  
}  
```  
  
A bisection points to: [Add improved range insertion to flat associative containers...](https://github.com/boostorg/container/commit/6ce2b2d0f896d5c4d2c1d071a2908e1aea0311f5)  
  
Thanks,  
Gregor

---

## Comment 1

> Username: gjasny  
> Created at: 2018-06-25 06:57:34 UTC  
> Url: https://github.com/boostorg/container/issues/75#issuecomment-399851360  

Hello @igaztanaga,  
  
could I somehow help here?  
  
Thanks,  
Gregor

---

## Comment 2

> Username: igaztanaga  
> Created at: 2018-06-25 12:15:10 UTC  
> Url: https://github.com/boostorg/container/issues/75#issuecomment-399930531  

Thanks for the report. I'll investigate it ASAP.

---

## Comment 3

> Username: igaztanaga  
> Created at: 2018-06-26 19:51:40 UTC  
> Url: https://github.com/boostorg/container/issues/75#issuecomment-400440522  

I reproduced it thanks to your testcase, there was clearly an error and ASAN is happy now. Many thanks for the report.
