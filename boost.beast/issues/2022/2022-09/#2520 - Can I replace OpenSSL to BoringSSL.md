# #2520 - Can I replace OpenSSL to BoringSSL? [Closed]

> Username: xtayaitak  
> Created at: 2022-09-16 10:34:50 UTC  
> Updated at: 2024-10-10 12:35:01 UTC  
> Closed at: 2022-09-17 08:37:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2520  

Can I replace OpenSSL to BoringSSL for https and wws ?

---

## Comment 1

> Username: madmongo1  
> Created at: 2022-09-16 12:49:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2520#issuecomment-1249322502  

Strictly this is an ASIO question. The short answer is technically yes, but...  
  
ASIO supports OpenSSL and WolfSSL out of the box.  
  
```  
#include <boost/asio/detail/config.hpp>  
#include <boost/asio/detail/socket_types.hpp>  
#if defined(BOOST_ASIO_USE_WOLFSSL)  
# include <wolfssl/options.h>  
#endif // defined(BOOST_ASIO_USE_WOLFSSL)  
#include <openssl/conf.h>  
#include <openssl/ssl.h>  
#if !defined(OPENSSL_NO_ENGINE)  
# include <openssl/engine.h>  
#endif // !defined(OPENSSL_NO_ENGINE)  
```  
https://github.com/boostorg/asio/blob/develop/include/boost/asio/ssl/detail/openssl_types.hpp  
https://www.wolfssl.com/  
  
So BoringSSL support is not automatically provided. However, there is nothing to stop you from writing your own BoringSSL stream type.  
Beast would then work with it.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-09-16 15:50:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2520#issuecomment-1249526876  

Maybe there is info here, or a lead: https://github.com/chriskohlhoff/asio/issues/52

---

## Comment 3

> Username: xtayaitak  
> Created at: 2022-09-17 08:37:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2520#issuecomment-1250029806  

Thanks.

---

## Comment 4

> Username: sanketdiwale  
> Created at: 2024-10-10 12:34:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2520#issuecomment-2404969643  

I believe, I discovered an easier fix for this problem. You can replace your include path for openssl headers to the openssl headers included with boringssl instead of the ones distributed from openssl. This seems to build correctly when linking with bosringssl.  
  
For reproducing: Build and install boringssl, the boringssl install directory will have "lib","include" and "bin" directories. You can then link to the ssl and cryto libraries in "lib" and set the include directories to have "include" which contains a "openssl" directory with the necessary headers. I haven't investigated the differences in the headers thoroughly, but this seemed to fix the linking issues.
