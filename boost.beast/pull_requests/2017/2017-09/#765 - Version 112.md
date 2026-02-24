# #765 Version 112 [Merged]

> Username: vinniefalco  
> Created at: 2017-09-04 04:44:45 UTC  
> Updated at: 2017-09-13 00:16:26 UTC  
> Merged at: 2017-09-05 23:37:29 UTC  
> Closed at: 2017-09-05 23:37:29 UTC  
> Url: https://github.com/boostorg/beast/pull/765  

* Update websocket notes  
  
API Changes:  
  
* WebSocket writes return the bytes transferred  
* HTTP reads and writes return bytes transferred  
  
Actions Required:  
  
* Modify websocket write completion handlers to receive  
  the extra std::size_t bytes_transferred parameter.  
  
* Modify HTTP read and/or write completion handlers to  
  receive the extra std::size_t bytes_transferred parameter.

---
