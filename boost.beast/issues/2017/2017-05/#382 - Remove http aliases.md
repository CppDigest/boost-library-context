# #382 - Remove http aliases [Closed]

> Username: vinniefalco  
> Created at: 2017-05-23 21:18:26 UTC  
> Updated at: 2017-05-31 00:44:09 UTC  
> Closed at: 2017-05-31 00:44:09 UTC  
> Url: https://github.com/boostorg/beast/issues/382  

Providing `request`, `response`, `request_header`, and `response_header` is confusing. Just do away with them and let users decide if they want aliases and if so, how they should be structured.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-05-26 04:29:04 UTC  
> Url: https://github.com/boostorg/beast/issues/382#issuecomment-304188192  

Maybe we should keep `request` and `response` and ditch the rest?
