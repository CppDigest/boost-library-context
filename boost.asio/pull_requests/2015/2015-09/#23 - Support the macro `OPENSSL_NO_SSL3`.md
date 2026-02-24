# #23 Support the macro `OPENSSL_NO_SSL3` [Closed]

> Username: usagi  
> Created at: 2015-09-03 09:52:03 UTC  
> Updated at: 2017-12-02 07:50:14 UTC  
> Closed at: 2017-12-02 07:50:14 UTC  
> Url: https://github.com/boostorg/asio/pull/23  

In LibreSSL, SSLv3 was removed 1 week ago. I was need supporting `OPENSSL_NO_SSL3` macro because I use Boost.Asio with the latest libressl.  
  
Ref.: https://github.com/libressl-portable/openbsd/commit/b038833f6e0ec4ab42792cb01c16303a183fb98c

---

## Comment 1

> Username: rioderelfte  
> Created_at: 2015-11-02 21:11:51 UTC  
> Url: https://github.com/boostorg/asio/pull/23#issuecomment-153157843  

OpenSSL added an option do disable SSLv3 support, too. Debian recently activated this flag in the OpenSSL Package (see [Changelog](https://tracker.debian.org/media/packages/o/openssl/changelog-1.0.2d-2)), so asio's SSL support currently does not compile on Debian:  
  
```  
# cat test.cpp  
#include <boost/asio/ssl.hpp>  
  
int main() {}  
# g++ test.cpp  
In file included from /usr/include/boost/asio/ssl/context.hpp:786:0,  
                from /usr/include/boost/asio/ssl.hpp:19,  
                from test.cpp:1:  
/usr/include/boost/asio/ssl/impl/context.ipp: In constructor 'boost::asio::ssl::context::context(boost::asio::ssl::context_base::method)':  
/usr/include/boost/asio/ssl/impl/context.ipp:93:29: error: '::SSLv3_method' has not been declared  
    handle_ = ::SSL_CTX_new(::SSLv3_method());  
                            ^  
/usr/include/boost/asio/ssl/impl/context.ipp:96:29: error: '::SSLv3_client_method' has not been declared  
    handle_ = ::SSL_CTX_new(::SSLv3_client_method());  
                            ^  
/usr/include/boost/asio/ssl/impl/context.ipp:99:29: error: '::SSLv3_server_method' has not been declared  
    handle_ = ::SSL_CTX_new(::SSLv3_server_method());  
                            ^  
```

---

## Comment 2

> Username: LocutusOfBorg  
> Created_at: 2015-11-04 11:50:29 UTC  
> Url: https://github.com/boostorg/asio/pull/23#issuecomment-153696956  

oops, I created two pull request prior to discover this one..  
My pull request also addresses the old code directory  
https://github.com/boostorg/asio/pull/30  
https://github.com/boostorg/asio/pull/29

---

## Comment 3

> Username: chriskohlhoff  
> Created_at: 2017-12-02 07:50:14 UTC  
> Url: https://github.com/boostorg/asio/pull/23#issuecomment-348675582  

This has already been fixed.

---
