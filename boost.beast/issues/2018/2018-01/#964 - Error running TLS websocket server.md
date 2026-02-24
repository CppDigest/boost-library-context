# #964 - Error running TLS websocket server [Closed]

> Username: krojew  
> Created at: 2018-01-02 07:03:34 UTC  
> Updated at: 2018-01-05 19:09:45 UTC  
> Closed at: 2018-01-05 19:09:45 UTC  
> Url: https://github.com/boostorg/beast/issues/964  

When trying to run a websocket server with TLS enabled, the connection always ends with short read during accept from Chrome. This is also repeatable for the server example. What's strange is that it works with Firefox. Both browsers accept the certificate. With a test websocketpp server, everything works on both browsers.  
  
Version: 144 with boost 1.66  
Steps to reproduce: run example async ssl server and try to connect via a browser (using e.g. http://www.websocket.org/echo.html)

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-01-02 14:57:37 UTC  
> Url: https://github.com/boostorg/beast/issues/964#issuecomment-354783768  

Browser WebSocket implementations don't behave nicely with TLS enabled and self-signed certificates like those used in the examples. I think Internet Explorer allowed me to successfully connect, the others all failed the handshake. And Chrome in particular is very bad about ending a TLS session, Google decided that it is faster to just abort the connection than to go through a graceful closure. With Chrome you will notice that it aborts connections instead of shutting them down cleanly.  
  
Chrome will allow a self-signed certificate when accessing the example servers using HTTP, after showing you a warning message. But not over WebSocket. In short, it is a mess. All of these problems go away with a proper certificate set on a real domain.

---

## Comment 2

> Username: krojew  
> Created at: 2018-01-03 10:27:43 UTC  
> Url: https://github.com/boostorg/beast/issues/964#issuecomment-354979717  

Then this can be closed, I guess. Although it's interesting to see what websocketpp does to suppress this error.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-01-03 13:16:56 UTC  
> Url: https://github.com/boostorg/beast/issues/964#issuecomment-355009453  

websocketpp simply doesn't report it, I believe

---

## Comment 4

> Username: vinniefalco  
> Created at: 2018-01-05 19:09:45 UTC  
> Url: https://github.com/boostorg/beast/issues/964#issuecomment-355639114  

It looks like this has been resolved, thanks for bringing this up! Feel free to open any new issues as needed.
