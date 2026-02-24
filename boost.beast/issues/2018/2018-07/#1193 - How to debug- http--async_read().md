# #1193 - How to debug: http::async_read() ? [Closed]

> Username: mirkub  
> Created at: 2018-07-16 16:21:44 UTC  
> Updated at: 2018-09-01 11:53:49 UTC  
> Closed at: 2018-09-01 11:53:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1193  

### Version of Beast  
167  
  
### Steps necessary to reproduce the problem  
Use gcov with  http::async_read().  
  
### All relevant compiler information  
g++ (GCC) 4.8.5 20150623 (Red Hat 4.8.5-11)  
  
I'm implementing a client (with pipelinening) but the asio never calls completion handler on for the async_read. When I compile it without gcov all works fine and the completion handler is called. Is there a way to debug the completion queue somehow?  
  
I can see the responses on the network arriving like that (which is fine). But the handler is not called for unknown reason:  
  
```  
127.000.000.001.58206-127.000.000.001.08000: GET /whatsup/1 HTTP/1.1  
Host: 127.0.0.1  
User-Agent: Boost.Beast/167  
  
  
127.000.000.001.58206-127.000.000.001.08000: GET /whatsup/2 HTTP/1.1  
Host: 127.0.0.1  
User-Agent: Boost.Beast/167  
  
  
127.000.000.001.08000-127.000.000.001.58206: HTTP/1.1 200 OK  
Server: unit-test-mock  
Content-Length: 11  
Content-Type: application/vnd.api+json  
  
dummy delay  
127.000.000.001.08000-127.000.000.001.58206: HTTP/1.1 200 OK  
Server: unit-test-mock  
Content-Length: 7  
Content-Type: application/vnd.api+json  
  
instant  
  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-07-16 16:27:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1193#issuecomment-405305595  

Try Asio's handler tracking:  https://www.boost.org/doc/libs/1_66_0/doc/html/boost_asio/overview/core/handler_tracking.html

---

## Comment 2

> Username: mirkub  
> Created at: 2018-07-26 11:27:38 UTC  
> Url: https://github.com/boostorg/beast/issues/1193#issuecomment-408065903  

That's handy, thanks.

---

## Comment 3

> Username: stale[bot]  
> Created at: 2018-08-25 11:37:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1193#issuecomment-415963266  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 4

> Username: stale[bot]  
> Created at: 2018-09-01 11:53:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1193#issuecomment-417854371  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
