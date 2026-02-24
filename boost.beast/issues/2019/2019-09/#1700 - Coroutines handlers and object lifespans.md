# #1700 - Coroutines handlers and object lifespans [Closed]

> Username: brjoha  
> Created at: 2019-09-11 16:06:44 UTC  
> Updated at: 2019-10-18 19:54:16 UTC  
> Closed at: 2019-10-18 19:54:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1700  

I'm running into an intermittent crash with a less then helpful stacktrace.  My suspicion is that a given socket object has gone out of scope, but a previously queued event is attempting to work with it.  My understanding is that this is a common scenario in ASIO that is handled by the use of a shared pointer in the callback handler.  But how can this be addressed when using a coroutine handler?  For example...  
  
```  
beast::ssl_stream<beast::tcp_stream> my_sock;  
// connection setup happens  
// coroutine is spawned for io  
  
// inside the coroutine loop  
beast::http::async_write(my_sock, my_request, yield[ec]);  
if (ec && ec.value() == ECANCELED) {  
   return;  // exit coroutine  
}  
// continue working  
```  
  
Before my_sock goes out of scope, I can invoke cancel() which will terminate the async_write() operation with an ECANCELED result.  However, if there is already an event queued for completion of the operation, it will execute without an ECANCELED result.  
  
Is the right way to handle this by maintaining a shared pointer for the lifespan of the coroutine, rather than the handler, and checking both the result and the state of the socket on return from async_write()?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-09-11 16:19:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1700#issuecomment-530455371  

Instead of `if (ec && ec.value() == ECANCELED)` you can write `if(ec == net::error::operation_aborted)`. This won't answer your question but it is idiomatic.  
  
> Before my_sock goes out of scope, I can invoke cancel()  
  
This is a red flag. How can you call `cancel` if your coroutine is blocked in the call to `async_write`? And if your coroutine is not blocked, then there is no I/O to cancel. The only way you can meaningfully call `cancel` is from a *second coroutine* which uses the *same strand* as the socket. And in this scenario, the lifetime issue will become obvious since they will both need to access the same socket variable.

---

## Comment 2

> Username: brjoha  
> Created at: 2019-09-11 16:59:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1700#issuecomment-530471102  

"This is a red flag. How can you call cancel if your coroutine is blocked in the call to async_write?"  
  
In my case, cancel() is being called from another coroutine (associated with a different object) on the same thread.  The coroutine of the object I'm trying to cleanup runs in a loop where it performs an async_pop on a queue (using a net::posix::descriptor around a Linux event_fd) and then an async_write on a socket.  Both the queue and socket are canceled to trigger the coroutine to exit.  But as I said, my suspicion is that there is already an event in the underlying ASIO queue for which the cancel has no effect.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-09-11 17:11:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1700#issuecomment-530475236  

> In my case, cancel() is being called from another coroutine (associated with a different object) on the same thread.  
  
How do you know it is on the same thread? Do you have an implicit strand (only one thread calling `io_context::run`)?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2019-09-11 17:12:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1700#issuecomment-530475859  

> In my case, cancel() is being called from another coroutine (associated with a different object)  
  
Then you have shared-ownership of the socket in question. The only way to sanely manage the lifetime is with a reference count (for example, using `shared_ptr`).

---

## Comment 5

> Username: brjoha  
> Created at: 2019-09-11 17:24:12 UTC  
> Updated at: 2019-09-11 17:24:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1700#issuecomment-530480585  

Yes, it's an implicit strand.  
Yes, I agree a shared pointer will be involved in the solution.  My understanding is that the typical approach involves a shared pointer in the async handler.  This ensures the object stays around as long as there are pending events.  But when using coroutines, the yield context is the handler.  So it seems the lifecycle now has to be managed externally, which then decouples pending events from the lifespan of the object.  Does that make the issue any clearer?

---

## Comment 6

> Username: vinniefalco  
> Created at: 2019-09-11 17:31:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1700#issuecomment-530483341  

> Does that make the issue any clearer?  
  
Kind of. Any coroutine that wants to access the same socket needs to have ownership of a `shared_ptr` to that socket. How you accomplish that is up to you.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2019-09-11 17:32:24 UTC  
> Updated at: 2019-09-11 17:32:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1700#issuecomment-530483934  

> when using coroutines, the yield context is the handler  
  
This is not correct. The yield context is the **CompletionToken**. The coroutine is the handler. Putting a `shared_ptr` in the coroutine is as easy as simply making it a local variable in the coroutine's stack frame. You will have to acquire the value from somewhere of course (perhaps a synchronized data structure).

---

## Comment 8

> Username: brjoha  
> Created at: 2019-09-11 17:41:56 UTC  
> Updated at: 2019-09-11 18:10:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1700#issuecomment-530487901  

"Putting a shared_ptr in the coroutine is as easy as simply making it a local variable in the coroutine's stack frame."  
This is effectively what I'm now doing, but am still seeing a (more infrequent) crash.  I wanted to first confirm that this was the correct approach under coroutines .  I'll insert some additional checks on return from async calls to see if I can better characterize the root cause then.

---

## Comment 9

> Username: stale[bot]  
> Created at: 2019-10-11 18:29:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1700#issuecomment-541174637  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 10

> Username: stale[bot]  
> Created at: 2019-10-18 19:54:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1700#issuecomment-543912414  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
