# #348 - asio using SSL_CTX_set_app_data breaks my usage of SSL_CTX_set_ex_data [Closed]

> Username: jaqenn  
> Created at: 2020-05-08 16:10:44 UTC  
> Updated at: 2020-12-30 01:14:19 UTC  
> Closed at: 2020-12-30 01:14:18 UTC  
> Url: https://github.com/boostorg/asio/issues/348  

OpenSSL allows you to stash user-data in their context object, as documented here:  https://www.openssl.org/docs/man1.0.2/man3/SSL_CTX_set_ex_data.html  
  
Since many different libraries might want to place user data, OpenSSL supports multiple user data slots on the same context.  You reserve a unique slot for your data by calling SSL_CTX_get_ex_new_index() sometime before your get/set.  
  
OpenSSL also provides SSL_CTX_set_app_data(), which is just a #define that calls get/set ex_data with a hardcoded index zero, as seen here: https://github.com/openssl/openssl/blob/master/include/openssl/ssl.h  
  
asio uses SSL_CTX_set_app_data() to arrange verify callback objects, for instance in context::do_set_verify_callback().  When my program calls SSL_CTX_get_ex_new_index() I get assigned index zero, which is in conflict with asio's implementation that uses a hardcoded zero.  I had to make a fake call to SSL_CTX_get_ex_new_index() with null new/copy/delete callbacks to get out of asio's way, and then I was able to followup with a second real call to SSL_CTX_get_ex_new_index() to get assigned a non-zero index.  
  
I think the best way to address this issue is having asio switch to using the ex_data family of functions instead.

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 01:14:17 UTC  
> Url: https://github.com/boostorg/asio/issues/348#issuecomment-752293647  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#761](https://github.com/chriskohlhoff/asio/issues/761).
