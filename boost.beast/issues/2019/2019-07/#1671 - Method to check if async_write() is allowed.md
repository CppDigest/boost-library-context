# #1671 - Method to check if async_write() is allowed? [Closed]

> Username: reddwarf69  
> Created at: 2019-07-31 18:37:35 UTC  
> Updated at: 2019-10-05 19:06:50 UTC  
> Closed at: 2019-10-05 19:06:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1671  

I have a silly protocol implemented on top of websocket::stream. It has async_write and async_read operations that for the purposes of this issue could simply prepend a 0 byte to the written/read buffer. But it also has an async_accept operation, composed of an async_read followed by an async_write (with some acceptance logic in the middle which doesn't matter here). Being a silly protocol it doesn't have any async_close(), to close it you close the next_layer stream.  
  
Then I have the typical class ("ClassA") using this protocol which queues the messages to send, enable_shared_from_this etc. This class has a stop() method for when I want to force a session to finish. This stop() method ends up triggering an websocket::stream::async_close. It also sets an internal flag saying it's stopping, so it doesn't try to call websocket::stream::async_write any more (async_close says "No other operations except for message reading operations should be initiated on the stream after a close operation is started").  
  
Now, the problem: what if I do async_accept() and before it finishes stop() is called? async_accept() doesn't have access to the flag in "ClassA", so it will try to websocket::stream::async_write even after websocket::stream::async_close is called and... well, the documentation says it should not.  
  
I can obviously add an async_close() to my protocol which simply sets its own flag and calls websocket::stream::async_close. async_accept() would have access to this flag, it could skip the async_write and instead call the completion handler with operation_aborted. You could even argue it's the right thing to do. But... there is anything Beast could do to avoid this need? There is websocket::stream::is_open, but according to the documentation it will still return true after websocket::stream::async_close returns. Would it make sense to add a method to query whether the stream is "closed for writing"?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-08-29 17:58:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1671#issuecomment-526295442  

You need to always have a call to `async_read` pending, and simply detect the error `websocket::error::closed` to know that the stream is closed.

---

## Comment 2

> Username: stale[bot]  
> Created at: 2019-09-28 18:58:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1671#issuecomment-536216853  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 3

> Username: stale[bot]  
> Created at: 2019-10-05 19:06:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1671#issuecomment-538679718  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
