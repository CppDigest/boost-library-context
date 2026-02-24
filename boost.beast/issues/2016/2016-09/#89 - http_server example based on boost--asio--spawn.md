# #89 - http_server example based on boost::asio::spawn [Closed]

> Username: e-fominov  
> Created at: 2016-09-24 13:34:50 UTC  
> Updated at: 2016-10-14 23:09:26 UTC  
> Closed at: 2016-10-14 23:09:26 UTC  
> Url: https://github.com/boostorg/beast/issues/89  

boost::asio offers asynchronous implementation based on Coroutines [that can use single thread for multiple parallel requests](http://www.boost.org/doc/libs/1_61_0/doc/html/boost_asio/example/cpp03/spawn/echo_server.cpp)  
  
It would be nice to have such example code for http server

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-09-25 23:33:14 UTC  
> Url: https://github.com/boostorg/beast/issues/89#issuecomment-249453826  

Note that the provided asynchronous server example can also use a single thread for multiple requests, just set the number of threads to 1 in the constructor.

---

## Comment 2

> Username: e-fominov  
> Created at: 2016-09-26 07:50:31 UTC  
> Url: https://github.com/boostorg/beast/issues/89#issuecomment-249502110  

Yes, but "spawn" technology uses Boost.Coroutines internally and can produce code that will be as simple as http_sync_server but it will be asynchronous  
I will try to make sample and show you. I think it will be interesting, as Beast has its own asyncronour read/write methods and we should check if they can be safely used with Coroutines

---

## Comment 3

> Username: vinniefalco  
> Created at: 2016-09-26 11:07:51 UTC  
> Url: https://github.com/boostorg/beast/issues/89#issuecomment-249541609  

I'm sure that Beast works with coroutines, because some of the unit tests use them:  
https://github.com/vinniefalco/Beast/blob/master/test/websocket/stream.cpp#L436  
  
I have to be honest, part of my objection to providing a coroutine server example is that I don't want to have to maintain two copies of each server, especially because I plan on making significant changes to those servers (which I would have to replicate for the coroutine versions).

---

## Comment 4

> Username: e-fominov  
> Created at: 2016-09-26 11:21:49 UTC  
> Url: https://github.com/boostorg/beast/issues/89#issuecomment-249543974  

I accept your position. Beast should have 100% reliable and compatible low-level http/websocket protocol. And making servers should be done later or somewhere else. If I will find any problems with coroutines, I will make test for it  
  
BTW, any plans for HTTP2?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2016-09-26 11:27:41 UTC  
> Url: https://github.com/boostorg/beast/issues/89#issuecomment-249544995  

Yes, I have a design for what HTTP/2 would look like. First, note that the `message` class is already HTTP/2 compatible. One complication is that HTTP/2 requires a stateful connection, in order to decompress the headers and keep track of request/response channels. So there has to be an object wrapping a next layer that represents an active HTTP/2 session. Very much like how the current `websocket::stream` is designed.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2016-10-14 23:09:26 UTC  
> Url: https://github.com/boostorg/beast/issues/89#issuecomment-253940778  

Closing this since its addressed by the tests which use coroutines
