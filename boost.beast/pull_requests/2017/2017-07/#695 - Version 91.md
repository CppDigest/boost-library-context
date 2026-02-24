# #695 Version 91 [Merged]

> Username: vinniefalco  
> Created at: 2017-07-29 02:03:02 UTC  
> Updated at: 2017-07-31 21:07:35 UTC  
> Merged at: 2017-07-29 07:27:26 UTC  
> Closed at: 2017-07-29 07:27:26 UTC  
> Url: https://github.com/boostorg/beast/pull/695  

* Adjust redirect html  
* Don't build pre-C++11  
* source.dox is path-independent  
* Tidy up namespace qualifiers  
* Tidy up MSVC CMakeLists.txt  
* Optimize buffered_read_stream  
* constexpr in derived buffers  
* Set BOOST_ASIO_NO_DEPRECATED  
* Use Asio array optimization in static_buffer_base  
* Rename wstest source file  
* Use fopen_s on Windows  
* Fix Appveyor script  
* Update project metadata  
* Move benchmarks to bench/  
* Fix doc title  
* Build stand-alone doc  
* Update doc copyrights  
* Refactor test build scripts  
  
WebSocket:  
  
* Tidy up websocket javadocs  
* Refactor accept, handshake ops  
* Use read buffer instead of buffered stream  
  
API Changes  
  
* control frame callbacks are non-const references  
  
Actions Required:  
  
* Modify calls to set the control frame callback, to  
  pass non-const reference instances, and manage the  
  lifetime of the instance.

---
