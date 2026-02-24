# #2544 - boost::asio::async_read fixed buffer size alternative for websockets [Closed]

> Username: gunzino  
> Created at: 2022-10-18 09:20:41 UTC  
> Updated at: 2022-10-18 15:17:57 UTC  
> Closed at: 2022-10-18 15:17:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2544  

Hello,  
  
I develop application which currently uses simple TCP protocol based on packet structure:  
header 2byte - message size - boost::asio::async_read on fixed 2byte buffer  
after header is received than boost::asio::async_read on buffer based on message size  
This guarantee that always full message with header + body is received.  
  
I would like to rewrite the application so it would be able to use websockets too and found out this wonderful library. However, it only offers me usage of async_read_some on static buffer and async_read is based on dynamic buffers only. To re-use handlers from my TCP-socket based library it's better for me to use async_read_some on fixed buffer size. However, I am not sure if async_read_some always returns all requested bytes from the stream.  
  
Sending messages is done purely by async_write so header and body should be part of one message.  
  
I wonder if I can use same approach as I use on TCP socket using async_read_some, altough websocket is message-based using async_read_some should read bytes of already received FULL message or is it possible that in the second step (during receiving the message body)  not all bytes will be received as requested? Do I need to check this using bytes_transferred?  
  
Thanks!!

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2022-10-18 11:03:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2544#issuecomment-1282208743  

That depends on whether or not you want to align your packets with the messages/frames. If so, you code gets easier, but you can also just use the websocket like it's a stream and ignore the message boundaries as an implementation detail. It's up to you.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-10-18 12:13:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2544#issuecomment-1282287764  

Call `websocket::stream::is_message_done` to find out if you need to read more:  
  
https://www.boost.org/doc/libs/1_80_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/is_message_done.html

---

## Comment 3

> Username: gunzino  
> Created at: 2022-10-18 13:21:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2544#issuecomment-1282377991  

@vinniefalco   
  
Yeah I just figured out I'm getting only 1387 bytes out of about 11000 on one async_read_some call. Seems like I'll have to use async_read and therefore cannot avoid using dynamic buffers or implement multiple async_read_some calls for one message.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-10-18 15:03:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2544#issuecomment-1282541231  

> Seems like I'll have to use async_read  
  
No, just call `async_read_some` over and over until `is_message_done` returns `true`.

---

## Comment 5

> Username: gunzino  
> Created at: 2022-10-18 15:17:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2544#issuecomment-1282565852  

> > Seems like I'll have to use async_read  
>   
> No, just call `async_read_some` over and over until `is_message_done` returns `true`.  
  
Yeah, works flawlessly. Thanks.
