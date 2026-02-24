# #244 - Revisit http types in websocket handshake [Closed]

> Username: vinniefalco  
> Created at: 2017-01-30 13:29:19 UTC  
> Updated at: 2017-04-26 22:44:02 UTC  
> Closed at: 2017-04-26 22:44:02 UTC  
> Url: https://github.com/boostorg/beast/issues/244  

There's a case to be made that `request_type` and `response_type` could be `http::header` instead of `http::message`. This will also affect decorators.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-04-26 22:44:02 UTC  
> Url: https://github.com/boostorg/beast/issues/244#issuecomment-297562463  

This is already changed
