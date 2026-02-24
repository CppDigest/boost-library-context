# #1766 - async APIs callback guaranteed to be called? [Closed]

> Username: gladiacxtylish  
> Created at: 2019-11-15 07:22:04 UTC  
> Updated at: 2019-11-18 19:02:01 UTC  
> Closed at: 2019-11-18 19:02:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1766  

I was using the async APIs like async_write, async_read, etc of the websocket APIs. I have noticed sometimes the async callback would not be called, possibly due to client disconnect, network issues, etc. And since my io_context has only 1 thread, the rest of the logic were blocked as well. Is this expected? If so, is there a simple to handle this? Does switching to sync APIs solve this problem? Thanks.  
  
### Version of Beast  
1.69

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-11-15 13:02:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1766#issuecomment-554350637  

> the async callback would not be called, possibly due to client disconnect, network issues  
  
There is no way for the operating system to know when the client disconnects in all cases. See:  
https://www.boost.org/doc/libs/1_71_0/libs/beast/doc/html/beast/using_io/timeouts.html  
  
> Does switching to sync APIs solve this problem?  
  
No, it makes it worse - because synchronous APIs do not support timeouts.

---

## Comment 2

> Username: gladiacxtylish  
> Created at: 2019-11-15 19:17:16 UTC  
> Updated at: 2019-11-15 19:20:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1766#issuecomment-554492150  

Does that mean it is expected that the callback would not be called in certain scenarios?  
All I care is that the APIs do not permanently block. Say if the client disconnected, and OS does not know about it, what happens to the running async_write, async_read calls? Would it hung there forever? If so, even I timeout the call and close the connection, would it release the io_context to unblock other tasks?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-11-15 19:41:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1766#issuecomment-554500357  

>Would it hung there forever?  
  
Yes.  
  
>If so, even I timeout the call and close the connection, would it release the io_context  
> to unblock other tasks?  
  
Yes.
