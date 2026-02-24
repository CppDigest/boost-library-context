# #65 - Abstract WebSocket servers [Closed]

> Username: vinniefalco  
> Created at: 2016-08-26 13:10:14 UTC  
> Updated at: 2017-06-21 01:02:29 UTC  
> Closed at: 2017-06-21 01:02:28 UTC  
> Url: https://github.com/boostorg/beast/issues/65  

The echo WebSocket servers in the test directory should be refactored and made abstract, and moved to extras, so they may serve as a foundation for users to write their own application specific servers.  
  
The server will follow Asio best practices especially for managing concurrency properly, and shutting down without race conditions.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-09-25 15:22:45 UTC  
> Url: https://github.com/boostorg/beast/issues/65#issuecomment-249427717  

This could use whatever model is decided in #58

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-06-21 01:02:28 UTC  
> Url: https://github.com/boostorg/beast/issues/65#issuecomment-309933049  

This is done in server-framework
