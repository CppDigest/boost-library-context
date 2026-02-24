# #214 - Remove deprecated _SSL_COMP_free_compression_methods usage [Closed]

> Username: Superlokkus  
> Created at: 2019-03-11 20:11:19 UTC  
> Updated at: 2020-12-30 01:02:20 UTC  
> Closed at: 2020-12-30 01:02:19 UTC  
> Url: https://github.com/boostorg/asio/issues/214  

`_SSL_COMP_free_compression_methods` i.e. `SSL_COMP_free_compression_methods` is marked as ** deprecated** since OpenSSL 1.1.0k (see [https://www.openssl.org/news/cl110.txt](https://www.openssl.org/news/cl110.txt) ).  
Since this symbol is not exported anymore with default OpenSSL build as by the Macports or OpenSuse OpenSSL packages, any usage of SSL enabled `boost::asio` and `boost::beast` will fail.  
  
Possible duplicate of [!96](https://github.com/boostorg/asio/issues/96) but I thought since this is a request and the other is a bug report, this issue will trigger another process. Also !96 was by building with an additional build flag, I think the fact that this behaviour is now produced by simple default builds by package managers is new.

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 01:02:17 UTC  
> Url: https://github.com/boostorg/asio/issues/214#issuecomment-752291678  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#678](https://github.com/chriskohlhoff/asio/issues/678).
