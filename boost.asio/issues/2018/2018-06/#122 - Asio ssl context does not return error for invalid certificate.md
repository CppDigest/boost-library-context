# #122 - Asio ssl context does not return error for invalid certificate [Closed]

> Username: fabiansperber  
> Created at: 2018-06-13 13:08:58 UTC  
> Updated at: 2020-12-30 00:51:58 UTC  
> Closed at: 2020-12-30 00:51:57 UTC  
> Url: https://github.com/boostorg/asio/issues/122  

context::add_certificate_authority does a break at line 518/519 if a certificate couldn't be loaded to break out of the loop, but doesn't set a error code.

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 00:51:56 UTC  
> Url: https://github.com/boostorg/asio/issues/122#issuecomment-752289632  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#624](https://github.com/chriskohlhoff/asio/issues/624).
