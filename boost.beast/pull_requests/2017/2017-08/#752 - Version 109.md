# #752 Version 109 [Merged]

> Username: vinniefalco  
> Created at: 2017-08-25 14:41:08 UTC  
> Updated at: 2017-08-26 21:50:36 UTC  
> Merged at: 2017-08-26 16:58:36 UTC  
> Closed at: 2017-08-26 16:58:36 UTC  
> Url: https://github.com/boostorg/beast/pull/752  

* refactor test::stream  
  
WebSocket:  
  
* Fix async_read_some handler signature  
* websocket close fixes and tests  
* websocket handshake uses coroutine  
* websocket ping tests  
* Fix websocket close_op resume state  
* websocket write tests  
* split up websocket tests  
* websocket read tests  
  
API Changes:  
  
* websocket accept refactoring  
  
Actions Required:  
  
* Do not call websocket accept overloads which take  
  both a message and a buffer sequence, as it is  
  illegal per rfc6455.

---
