# #72 - need SNI support [Closed]

> Username: zynfly  
> Created at: 2017-12-05 15:58:37 UTC  
> Updated at: 2020-12-30 00:46:59 UTC  
> Closed at: 2020-12-30 00:46:50 UTC  
> Url: https://github.com/boostorg/asio/issues/72  

It's good to be able to have a library that can provide such a simple network development.This makes the job very simple.But the support for SSL is not yet perfect.like SNI,and more.

---

## Comment 1

> Username: arvidn  
> Created at: 2017-12-14 09:42:20 UTC  
> Url: https://github.com/boostorg/asio/issues/72#issuecomment-351659316  

you can always do it [by hand](https://github.com/arvidn/libtorrent/blob/master/src/session_impl.cpp#L349) by getting the ``SSL_CTX``.

---

## Comment 2

> Username: zynfly  
> Created at: 2017-12-14 13:15:01 UTC  
> Url: https://github.com/boostorg/asio/issues/72#issuecomment-351707525  

Is it better to unify the interface?  
To call the OpenSSL function in the ASIO program  is too ugly.

---

## Comment 3

> Username: ghost  
> Created at: 2020-12-30 00:46:44 UTC  
> Url: https://github.com/boostorg/asio/issues/72#issuecomment-752288562  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#593](https://github.com/chriskohlhoff/asio/issues/593).
