# #2407 - Boost Beast Http SSL Async Server | Example [Closed]

> Username: fatih-dogmus  
> Created at: 2022-04-16 20:54:47 UTC  
> Updated at: 2022-04-17 16:05:24 UTC  
> Closed at: 2022-04-17 04:53:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2407  

### Version of Beast  
  
322  
  
### Steps necessary to reproduce the problem  
  
This code shuts down the server 30 seconds after sending the request.:  
https://www.boost.org/doc/libs/1_79_0/libs/beast/example/http/server/async-ssl/http_server_async_ssl.cpp  
  
Because this line do it:  
beast::get_lowest_layer(stream_).expires_after(std::chrono::seconds(30));  
  
It just needs to close the socket, not server.  
  
### All relevant compiler information  
  
MSVC 2022

---

## Comment 1

> Username: sehe  
> Created at: 2022-04-16 21:19:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2407#issuecomment-1100756338  

The quoted code causes a particular connection (`session`) to be closed, not the server. How do you observe the server is shutdown?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-04-16 21:20:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2407#issuecomment-1100756476  

An unhandled exception would take the server down

---

## Comment 3

> Username: sehe  
> Created at: 2022-04-16 21:28:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2407#issuecomment-1100757372  

That's what I was thinking, so I was hoping that requesting more information can help @zavoyevatyel realize the origin of such an exception.

---

## Comment 4

> Username: fatih-dogmus  
> Created at: 2022-04-16 21:49:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2407#issuecomment-1100760031  

I just compile the code and make a request from the browser. After 30 seconds the server shuts down.   
I didn't add any code

---

## Comment 5

> Username: sehe  
> Created at: 2022-04-16 21:55:18 UTC  
> Updated at: 2022-04-16 21:57:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2407#issuecomment-1100760825  

Is there any output? Can you see whether exceptions are thrown by attaching a debugger to the server?  
  
Here's a comparison:  
  
https://user-images.githubusercontent.com/324097/163692549-cf3d895d-0b96-4272-a9bf-3efe05e6d364.mp4  
  
 For completeness, that's the exact same code but with three times the line  
  
```c++  
        std::cerr << __PRETTY_FUNCTION__ << " reset expires_after" << std::endl;  
```  
  
before each call to `expires_after`.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2022-04-16 23:53:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2407#issuecomment-1100772432  

have you seen `BOOST_CURRENT_LOCATION`? https://www.boost.org/doc/libs/1_77_0/boost/assert/source_location.hpp
