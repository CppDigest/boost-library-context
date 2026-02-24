# #36 Fix boringssl support. [Closed]

> Username: budnyjj  
> Created at: 2016-04-20 14:02:41 UTC  
> Updated at: 2017-12-02 07:44:31 UTC  
> Closed at: 2017-12-02 07:44:31 UTC  
> Url: https://github.com/boostorg/asio/pull/36  

There are some incopatibilities between current version of asio in latest stable version of Boost (1.60.0) and the BoringSSL library, which prevents us from using them together:  
- in boringssl, ERR_PACK macro takes only two arguments;  
- there is no SSL_R_SHORT_READ value in boringssl.  
  
This patch is inspired by   
https://gist.github.com/kzemek/37aa2a2138b2651f2c55 .

---

## Comment 1

> Username: budnyjj  
> Created_at: 2016-04-21 08:18:28 UTC  
> Updated_at: 2016-04-21 08:21:39 UTC  
> Url: https://github.com/boostorg/asio/pull/36#issuecomment-212800025  

Oh, I see that this issue was fixed in the upstream project: https://github.com/chriskohlhoff/asio/blob/master/asio/include/asio/ssl/detail/impl/engine.ipp  
  
So feel free to close this request, if it is not needed anymore.

---

## Comment 2

> Username: chriskohlhoff  
> Created_at: 2017-12-02 07:44:31 UTC  
> Url: https://github.com/boostorg/asio/pull/36#issuecomment-348675337  

Closing as latest has now been merged from upstream.

---
