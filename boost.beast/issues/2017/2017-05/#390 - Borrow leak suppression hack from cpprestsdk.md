# #390 - Borrow leak suppression hack from cpprestsdk [Closed]

> Username: vinniefalco  
> Created at: 2017-05-27 21:12:06 UTC  
> Updated at: 2017-06-21 23:46:17 UTC  
> Closed at: 2017-06-21 23:46:17 UTC  
> Url: https://github.com/boostorg/beast/issues/390  

Found this little nugget:  
https://github.com/Microsoft/cpprestsdk/blob/381f5aa92d0dfb59e37c0c47b4d3771d8024e09a/Release/src/websockets/client/ws_client_wspp.cpp#L62  
  
This could help the SSL examples.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-21 23:46:17 UTC  
> Url: https://github.com/boostorg/beast/issues/390#issuecomment-310235770  

SSL examples don't leak, so nothing to do.
