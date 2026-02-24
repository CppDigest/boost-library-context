# #128 - OSMemoryBarrier deprecated [Closed]

> Username: DesWurstes  
> Created at: 2018-07-02 10:00:30 UTC  
> Updated at: 2020-12-30 00:52:13 UTC  
> Closed at: 2020-12-30 00:52:12 UTC  
> Url: https://github.com/boostorg/asio/issues/128  

Clang output:  
  
```  
../boost/boost/asio/detail/macos_fenced_block.hpp:51:5: warning: 'OSMemoryBarrier' is deprecated: first deprecated in macOS 10.12 - Use std::atomic_thread_fence() from <atomic> instead [-Wdeprecated-declarations]  
    OSMemoryBarrier();  
    ^  
/usr/include/libkern/OSAtomicDeprecated.h:749:9: note: 'OSMemoryBarrier' has been explicitly marked deprecated here  
void    OSMemoryBarrier( void );  
 ```  
  
It refers to:  
  
https://github.com/boostorg/asio/blob/886839cf55d65439791515c7de252d2430913f6a/include/boost/asio/detail/macos_fenced_block.hpp#L44-L53

---

## Comment 1

> Username: Detry322  
> Created at: 2019-01-06 19:24:08 UTC  
> Url: https://github.com/boostorg/asio/issues/128#issuecomment-451767298  

I'm getting the same issue here...

---

## Comment 2

> Username: tiendq  
> Created at: 2019-01-18 07:25:48 UTC  
> Url: https://github.com/boostorg/asio/issues/128#issuecomment-455451710  

@DesWurstes @Detry322 This warning can be suppressed in Asio ([here](https://github.com/boostorg/asio/blob/develop/include/boost/asio/detail/fenced_block.hpp#L25)) by adding more checks for `__apple_build_version__` and `__clang_version__` if you're using Clang. But I don't know if there is a reliable checks with GCC.  
  
So the easiest way is defining `BOOST_ASIO_HAS_STD_ATOMIC` in your code, before including Asio:  
  
```  
#define BOOST_ASIO_HAS_STD_ATOMIC  
#include <boost/asio.hpp>  
```

---

## Comment 3

> Username: ghost  
> Created at: 2020-12-30 00:52:11 UTC  
> Url: https://github.com/boostorg/asio/issues/128#issuecomment-752289701  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#627](https://github.com/chriskohlhoff/asio/issues/627).
