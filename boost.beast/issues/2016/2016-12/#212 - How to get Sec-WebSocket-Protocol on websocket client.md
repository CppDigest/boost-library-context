# #212 - How to get Sec-WebSocket-Protocol on websocket client [Closed]

> Username: redboltz  
> Created at: 2016-12-23 10:44:27 UTC  
> Updated at: 2017-05-09 14:21:09 UTC  
> Closed at: 2017-05-09 14:21:09 UTC  
> Url: https://github.com/boostorg/beast/issues/212  

After #211 solved, I come up with the next question.  
  
Client can add a set of subprotocols using set_subprotocls decorator.  
According to RFC6455, the server checks a set of subprotocols sent by client, and then, chooses one of them, then sends it as the response.  
  
The server can read the fields of the request using `beast::http::async_read()`.  
https://vinniefalco.github.io/beast/beast/ref/http__async_read.html  
  
The third parameter contains a list of fields. And using #195 way, I can add `Sec-WebSocket-Protocol` field in the response.   
I'm not sure whether https://github.com/vinniefalco/Beast/issues/195#issuecomment-261727703 or https://github.com/vinniefalco/Beast/issues/195#issuecomment-263746647 is better.  
  
Anyway, after the server sent the response, the client needs to check the field to check which protocol is chosen. However, `async_handshake` and its callback don't seems to contain the response.  
https://vinniefalco.github.io/beast/beast/ref/websocket__stream/async_handshake.html  
  
It there any way to get the specific filed in the response?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-12-28 14:13:27 UTC  
> Url: https://github.com/boostorg/beast/issues/212#issuecomment-269482254  

We probably need to make some changes to the websocket interfaces to handle your use-case.

---

## Comment 2

> Username: redboltz  
> Created at: 2016-12-28 14:47:20 UTC  
> Url: https://github.com/boostorg/beast/issues/212#issuecomment-269486537  

@vinniefalco , thank you for the response.  
  
The model in my brain is something like as follows:  
  
|No|Client|Network|Server|  
|---|---|---|---|  
|1|websocket::stream::async_handshake|||  
|2|decorated header|->|http::async_read 3rd parameter|  
|3|*A | |websocket::stream::async_accept 1st parameter|  
|4|*B |<-|decorated header|  
  
We can do No.1 and 2 using existing mechanism. It's good.  
  
I don't understand the difference between No.3 and 4. But I tested other websocket libraries (e.g. node.js, Java) as the client. Then I got a response header *B. So I use the decorator for adding the specific header to upgrade response. I think that adding the new overload of `async_handshake` that have `message< isRequest, Body, Fields >& msg` parameter like as `http::async_read 3rd parameter` seems to be good. The client can get *B information.  
  
By the way, what does `websocket::stream::async_accept 1st parameter` mean? Is the data sent to the client?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2016-12-28 14:57:58 UTC  
> Updated at: 2016-12-28 15:00:27 UTC  
> Url: https://github.com/boostorg/beast/issues/212#issuecomment-269488116  

>By the way, what does websocket::stream::async_accept 1st parameter mean? Is the data sent to the client?  
  
There are three versions of `async_accept` (http://vinniefalco.github.io/beast/beast/ref/websocket__stream/async_accept.html):  
  
```  
template<class AcceptHandler>  
void  
async_accept(AcceptHandler&& handler);  
  
template<class ConstBufferSequence, class AcceptHandler>  
void-or-deduced  
async_accept(ConstBufferSequence const& buffers, AcceptHandler&& handler);  
  
template<class Body, class Fields, class AcceptHandler>  
void  
async_accept(http::request< Body, Fields > const& request, AcceptHandler&& handler);  
```  
  
The second version is for when you have already read some bytes from the socket which belong to the request, and you want to provide those bytes to the accept call. One way this is used by users is to detect if the connection is using TLS/SSL (by reading the first 6 bytes). If not, those bytes are forwarded to the accept call. Example of such code:  
https://github.com/ripple/rippled/blob/8425e4558a350d3145be288af328c3b849f3c475/src/ripple/server/impl/Door.h#L118  
  
The third version is for when you already have the HTTP request and you have determined it is a WebSocket upgrade. This could happen if you are implementing a web server that serves both HTTP and WebSocket connections.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2016-12-28 15:05:20 UTC  
> Url: https://github.com/boostorg/beast/issues/212#issuecomment-269489104  

>I think that adding the new overload of async_handshake that have `message< isRequest, Body, Fields & msg` parameter like as `http::async_read` 3rd parameter seems to be good. The client can get *B information.  
  
What about this:  
  
```  
using response_predicate =  
    std::function<http::response_header<http::fields> const&, error_code&>;  
  
/** Set the response predicate for the stream.  
  
    The response predicate is called just before a successful call to @ref handshake or  
    @ref async_handshake would return. It provides the function object the opportunity to generate  
    a failure based on the contents of the upgrade response, or to calculate metadata based on the  
    upgrade response.  
*/  
void set_option(response_predicate&& o);  
```

---

## Comment 5

> Username: redboltz  
> Created at: 2016-12-29 02:19:03 UTC  
> Url: https://github.com/boostorg/beast/issues/212#issuecomment-269572201  

@vinniefalco , it looks nice!  
Now I understand a little deeper the design policy:)  `set_option` is the expansion point. It can avoid increasing interfaces.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-04-26 22:45:12 UTC  
> Url: https://github.com/boostorg/beast/issues/212#issuecomment-297562663  

I'm moving in the opposite direction of `std::function` based options. Because they can cause dynamic allocation, and they take up room in the `stream` object even when they aren't being used (such as when a connection is already established).

---

## Comment 7

> Username: vinniefalco  
> Created at: 2017-04-27 01:29:58 UTC  
> Updated at: 2017-04-28 01:36:48 UTC  
> Url: https://github.com/boostorg/beast/issues/212#issuecomment-297586159  

To achieve the feature described in the issue, the following changes are being made:  
  
`handshake`, `handshake_ex`, `async_handshake`, and `async_handshake_ex` overloads will return the HTTP Upgrade Response object (`response_type`) in a reference parameter.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2017-05-09 14:21:09 UTC  
> Url: https://github.com/boostorg/beast/issues/212#issuecomment-300180258  

This is all done and merged to **master** !!!
