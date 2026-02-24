# #718 Version 100 [Merged]

> Username: vinniefalco  
> Created at: 2017-08-05 01:42:59 UTC  
> Updated at: 2017-08-05 15:39:21 UTC  
> Merged at: 2017-08-05 15:36:59 UTC  
> Closed at: 2017-08-05 15:36:59 UTC  
> Url: https://github.com/boostorg/beast/pull/718  

* Fix doc convenience includes  
* Fix doc includes  
* Remove unused test header  
* Rename test macros  
* Reorder define test macro params  
* vcxproj workaround for include symlinks  
* Add variadic min/max  
  
WebSocket:  
  
* Remove obsolete frame tests  
* Refactor fail/clode code  
* Call do_fail from read_some  
* eof on accept returns error::closed  
* Fix stream::read_size_hint calculation  
* Documentation  
  
API Changes:  
  
* Calls to stream::close and stream::async_close will  
  automatically perform the required read operations  
  
* drain_buffer is removed  
  
* role_type replaces teardown_tag  
  
Actions Required:  
  
* Remove calling code which drains the connection after  
  calling stream::close or stream::async_close  
  
* Replace code which uses drain_buffer. For websocket::stream,  
  it is no longer necessary to manually drain the connection  
  after closing.  
  
* Modify signatures of teardown and async_teardown to use  
  role_type instead of teardown_tag  
  
* Change calls to teardown and async_teardown to pass the  
  correct role_type, client or server, depending on context.

---
