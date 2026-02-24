# #211 - ssl::stream ignores handshake errors [Closed]

> Username: djarek  
> Created at: 2019-03-07 02:26:00 UTC  
> Updated at: 2019-03-07 16:00:37 UTC  
> Closed at: 2019-03-07 16:00:37 UTC  
> Url: https://github.com/boostorg/asio/issues/211  

When performing a synchronous handshake, errors can be ignored and overwritten here https://github.com/boostorg/asio/blob/develop/include/boost/asio/ssl/detail/io.hpp#L36 if `op` sets `ec` and returns `engine::want_output`. The latter causes a call to `asio::write` which then resets the error code.

---

## Comment 1

> Username: djarek  
> Created at: 2019-03-07 16:00:34 UTC  
> Url: https://github.com/boostorg/asio/issues/211#issuecomment-470583870  

Fixed in 0e7ad0b146e808c175eb8fb55271b7e1f3095dce.
