# #1443 - Better integration of HTTP and WebSocket [Closed]

> Username: vinniefalco  
> Created at: 2019-02-09 01:42:56 UTC  
> Updated at: 2022-06-16 16:22:22 UTC  
> Closed at: 2022-06-16 16:22:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1443  

HTTP and WebSocket need to work together better for handshaking. Specifically, WebSocket algorithms which operate on requests and responses (e.g. handshake and accept) should be free functions. This allows the HTTP session to perform more of the business logic necessary to handle invalid requests (either due to syntax, or business logic such as authentication failure).  
  
For example, it should be possible for the HTTP session to call a free function that calculates the proper response to a WebSocket upgrade request, and then if the response is failed (i.e. not a 101 status code) the session can simply send the response back in its normal keep-alive workflow. Otherwise, it can construct a websocket stream and pass the response in to complete the handshake.  
  
The websocket stream should not be constructed until there is every indication that a received HTTP upgrade request will be accepted successfully, otherwise it forces the http_session to implement clumsy code.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-06-16 16:22:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1443#issuecomment-1157870973  

We aren't doing this anymore
