# #96 - build error when openssl is used with no-comp [Closed]

> Username: nazilliefesi  
> Created at: 2018-03-22 06:14:05 UTC  
> Updated at: 2020-12-30 00:50:04 UTC  
> Closed at: 2020-12-30 00:50:03 UTC  
> Url: https://github.com/boostorg/asio/issues/96  

the cleanup method SSL_COMP_free_compression_methods does not exist when openssl has been built with no-comp  
https://github.com/boostorg/asio/blob/c30ed33c5edf5b8d2bdbbfec125139889d69e136/include/boost/asio/ssl/detail/impl/openssl_init.ipp#L84  
  
and generates the following error:  
  
In file included from c:\sesteksdk/include\boost/asio/ssl/detail/openssl_init.hpp:100 :                                  c:\sesteksdk/include\boost/asio/ssl/detail/impl/openssl_init.ipp:84:7: error: no member named  
      'SSL_COMP_free_compression_methods' in the global namespace; did you mean 'SSL_COMP_get_compression_methods'?  
    ::SSL_COMP_free_compression_methods();  
    ~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  
inside the same method sk_SSL_COMP_free call has been called only if SSL_OP_NO_COMPRESSION has not been defined. Would doing the same here be a good idea? Would it affect anything else?

---

## Comment 1

> Username: Superlokkus  
> Created at: 2019-03-11 20:05:26 UTC  
> Url: https://github.com/boostorg/asio/issues/96#issuecomment-471702950  

This is a serious issue `SSL_COMP_free_compression_methods` is [marked as deprecated ](https://www.openssl.org/news/cl110.txt) and causes an undefined symbol when trying to use boost beast i.e. asio with SSL support, on my mac when building openssl via macports. As it seems opensuses openssl package also does not export the `_SSL_COMP_free_compression_methods` any more.

---

## Comment 2

> Username: ghost  
> Created at: 2020-12-30 00:50:02 UTC  
> Url: https://github.com/boostorg/asio/issues/96#issuecomment-752289263  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#604](https://github.com/chriskohlhoff/asio/issues/604).
