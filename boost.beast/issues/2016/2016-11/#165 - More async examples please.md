# #165 - More async examples please. [Closed]

> Username: miguelportilla  
> Created at: 2016-11-02 14:16:54 UTC  
> Updated at: 2017-02-01 23:46:33 UTC  
> Closed at: 2017-02-01 23:46:33 UTC  
> Url: https://github.com/boostorg/beast/issues/165  

Please provide more async examples. It would be nice to see how an asynchronous websocket server should send a standard string. More Handler examples etc...

---

## Comment 1

> Username: xhh  
> Created at: 2016-11-08 10:09:39 UTC  
> Url: https://github.com/boostorg/beast/issues/165#issuecomment-259096733  

Any examples on how to achieve `server.set_message_handler(&on_message)` like in WebSocket++?

---

## Comment 2

> Username: GregoryEAllen  
> Created at: 2017-01-09 21:04:31 UTC  
> Url: https://github.com/boostorg/beast/issues/165#issuecomment-271407324  

I'll second this one. I'd like to see an async websocket server example.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-01-09 21:18:35 UTC  
> Url: https://github.com/boostorg/beast/issues/165#issuecomment-271410668  

You mean like this one?  
https://github.com/vinniefalco/Beast/blob/master/test/websocket/websocket_async_echo_server.hpp

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-01-09 21:19:33 UTC  
> Url: https://github.com/boostorg/beast/issues/165#issuecomment-271410908  

>Any examples on how to achieve server.set_message_handler(&on_message) like in WebSocket++  
  
No, because Beast is not designed like websocketpp. It follows the Boost.Asio pattern of one completion handler per asynchronous operation. If you want to read messages, use `read` or `async_read`:  
http://vinniefalco.github.io/beast/beast/ref/websocket__stream/async_read.html

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-01-09 21:21:28 UTC  
> Url: https://github.com/boostorg/beast/issues/165#issuecomment-271411367  

I should make a copy of the echo servers without the extra code for the unit tests and put it examples/

---

## Comment 6

> Username: GregoryEAllen  
> Created at: 2017-01-10 16:37:20 UTC  
> Url: https://github.com/boostorg/beast/issues/165#issuecomment-271626058  

@vinniefalco I did find the one in test, but had a hard time telling how much was test and how much was example. Is there a compelling reason for wrapping all the members in `Peer::data`?

---

## Comment 7

> Username: vinniefalco  
> Created at: 2017-01-10 16:46:55 UTC  
> Updated at: 2017-01-10 16:53:42 UTC  
> Url: https://github.com/boostorg/beast/issues/165#issuecomment-271628724  

> Is there a compelling reason for wrapping all the members in `Peer::data`?  
  
Yes, this is the style of code I use when I write asynchronous _composed operations_.  
http://blog.think-async.com/2009/08/composed-operations-coroutines-and-code.html  
  
The data is stored in a `shared_ptr` because its expensive to copy, and Asio loves to make copies of completion handlers.
