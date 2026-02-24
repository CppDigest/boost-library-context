# #2226 - Advice: pending read operation and re-initialized websocket stream [Closed]

> Username: WojciechMigda  
> Created at: 2021-05-07 11:40:09 UTC  
> Updated at: 2021-05-07 12:11:57 UTC  
> Closed at: 2021-05-07 12:11:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2226  

PLEASE DON'T FORGET TO "STAR" THIS REPOSITORY :)  
  
### Version of Beast  
1.72  
  
### Steps necessary to reproduce the problem  
Please see below.  
  
### All relevant compiler information  
  
g++-7, g++-8, clang++-8  
  
----------  
  
The initial problem I was dealing with, and which I thought I have solved, was related to re-building websocket stream after the ethernet connection is temporarily disconnected.  
The solution I used is based on advice presented in this thread: https://github.com/boostorg/beast/issues/1778, and meant that I had websocket stream wrapped with `std::optional`, and upon detecting broken network connectivity and subsequent attempt to reconnect I would emplace new stream instance.  
  
It worked ok on the surface, until I instrumented the compiler with sanitizing options. With that I discovered that there was an attempt to reuse freed buffer that occured at the end of the following sequence of events:  
* `async_read` is initiated for websocket stream wrapped inside `std::optional` and the read op is pending,  
* link is terminated,  
* reconnection is attempted and new websocket stream instance is emplaced,  
* after the link is up again the sanitizer raises issue about reusing deleted memory (which belongs to asio buffer). Report shown by the sanitizer indicates that the buffer was created when `beast::ssl_stream` was created when `beast::websocket_stream` was created for the first time. The reuse occurs when the old read operation is being picked up.  
  
The issue doesn't disappear whether the ssl context I use for websocket stream creation persists, or is subject to the same `std::optional` trick.  
  
Invoking `cancel()` or `close()` on the underlying tcp stream, or `shutdown()` on ssl stream, when the websocket stream is re-built, does not make the problem go away.  
  
What I have observed as an improvement is if instead of discarding the prior websocket stream and allowing it to be destroyed during emplace operation I store it for later, then the pending read operation that is being picked up does not cause the sanitizer to report an issue.  
This leads me to believe, that using shared pointers for websocket stream could be one of the solutions. With that the shared pointer would be passed also to the completion handler, hence prolonging the lifetime of the old websocket stream. I am not convinced about introducing shared pointers, yet.  
  
Hence, maybe there is another way to deal with that pending read operation and purge it completely before it would access already destroyed resources? Or even better, if beast could have a mechanism to deal with such case and detect if the underlying resources are no longer available and simply shortcut to report failed operation.  
  
What would be a recommended solution here?

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-05-07 11:52:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2226#issuecomment-834304611  

When you destroy the websocket stream you must ensure that there are no outstanding async operations.  
This includes async_read, async_write, async_close and async_shutdown.  
  
The shared_ptr solution helps with this as it ensures that the stream can only die when all handlers (which would be holding a shared reference to it) have completed.  
  
The optional solution must explicitly check for this case.  
  
There is no need to destroy and rebuild the ssl::context. It is designed to be shared.

---

## Comment 2

> Username: WojciechMigda  
> Created at: 2021-05-07 12:06:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2226#issuecomment-834314733  

Thank you for such a quick response.  
So if I were to keep the `std::optional` solution it will not work if such outstanding async operations exist, because there is no way to force them to complete?

---

## Comment 3

> Username: madmongo1  
> Created at: 2021-05-07 12:08:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2226#issuecomment-834316417  

Yes you would need some kind of async condition variable which you would wait on. The predicate would be that total outstanding ops == 0.  
  
this is of course exactly what a captured shared_ptr models.

---

## Comment 4

> Username: madmongo1  
> Created at: 2021-05-07 12:08:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2226#issuecomment-834317065  

The reason is that the intermediate completion handlers within the websocket stream’s operations, access the websocket stream state.

---

## Comment 5

> Username: WojciechMigda  
> Created at: 2021-05-07 12:10:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2226#issuecomment-834318197  

Thank you, I consider this solved/answered.

---

## Comment 6

> Username: madmongo1  
> Created at: 2021-05-07 12:11:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2226#issuecomment-834319311  

Many thanks. Happy to help.
