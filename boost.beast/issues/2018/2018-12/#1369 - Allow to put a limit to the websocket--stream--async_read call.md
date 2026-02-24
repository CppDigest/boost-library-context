# #1369 - Allow to put a limit to the websocket::stream::async_read call [Closed]

> Username: reddwarf69  
> Created at: 2018-12-13 10:35:03 UTC  
> Updated at: 2018-12-13 14:15:38 UTC  
> Closed at: 2018-12-13 14:15:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1369  

I wanted to read a message no bigger than X bytes. That message can be composed of as many frames as it wants, I don't mind.  
  
I am using a flat_static_buffer, with a limit. But the limit is bigger than what I want to async_read because that same buffer is used for a bigger async_write before. Ideally I would like to use the same buffer object, but being able to set a different limit.  
  
async_read_some does have a `limit` parameter, or alternatively it accepts a MutableBufferSequence. If those overloads would exist also for async_read it would solve my issue. I guess there could also be a (mutable) DynamicBufferView which would wrap my flat_static_buffer with a smaller limit.  
  
Does this make sense? There is any reason why it can be done for frames/async_read_some but not for messages/async_read?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-12-13 13:32:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1369#issuecomment-446969108  

https://www.boost.org/doc/libs/1_69_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/read_message_max/overload1.html

---

## Comment 2

> Username: reddwarf69  
> Created at: 2018-12-13 14:15:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1369#issuecomment-446983046  

Didn't look in the most obvious place...
