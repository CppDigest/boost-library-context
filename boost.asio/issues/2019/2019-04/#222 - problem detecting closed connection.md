# #222 - problem detecting closed connection [Closed]

> Username: brjoha  
> Created at: 2019-04-11 22:42:14 UTC  
> Updated at: 2020-12-30 01:02:46 UTC  
> Closed at: 2020-12-30 01:02:45 UTC  
> Url: https://github.com/boostorg/asio/issues/222  

Not sure if this is the right forum for user questions. Please feel free to point me elsewhere.  
  
I have a client connection that is making asynchronous calls in a coroutine like this (error checking not shown)...  
  
myQueue.async_deque(req, yield);  // linux event_fd wrapped by posix::descriptor  
mySock.async_write(req, yield);  
mySock.async_read(rsp, yield);  
myOtherQueue.enqueue(rsp, yield);  
// repeat from beginning  
  
The loop continues until the connection is dropped. Where the problem lies is that I don't detect the peer has closed or half-closed the connection while I'm pending on the dequeue.  
  
I tried having a parallel coroutine do this...  
  
mySock.async_wait(wait_read, yield);  
if (mySock.available() == 0)  then peer closed connection  
  
...but it doesn't work because it will get a false positive when the first coroutine completes its read. Is there a better way to do this?

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 01:02:44 UTC  
> Url: https://github.com/boostorg/asio/issues/222#issuecomment-752291762  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#681](https://github.com/chriskohlhoff/asio/issues/681).
