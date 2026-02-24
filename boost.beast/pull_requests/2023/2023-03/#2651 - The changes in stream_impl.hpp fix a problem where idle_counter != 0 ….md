# #2651 The changes in stream_impl.hpp fix a problem where idle_counter != 0 … [Closed]

> Username: johannesparty  
> Created at: 2023-03-15 16:47:32 UTC  
> Updated at: 2023-10-09 09:07:40 UTC  
> Closed at: 2023-10-09 09:07:40 UTC  
> Url: https://github.com/boostorg/beast/pull/2651  

The change in stream_impl.hpp is fixes a bug that occurs when a stream that timed out is reused.  This would often result in the first assert in update_timer (near line 422) getting called erroneously.  This fix is just resetting idle_counter to 0 on open and reset.  This looks (to me) like a very straightforward and completely safe change.  
  
The change in read.hpp is to fix the ping timeout.  The code currently doesn't reset the timeout when a regular (non-ping) message is received. This is  because the coroutine for operator() near line 81 seldom executes the update near line 97 but instead jumps back into the main loop somewhere, bypassing the call to update_timer.    
  
While the fix I included works (tested), I don't claim it is the best possible fix.  Someone more familiar with the internals may have a better solution.  I'm happy as long as the problem is fixed.  If am happy to test an alternative fix, but may not have time in the short term to provide a test to others (I will do my best, just trying to be honest).  
  
With this fix, the ping timeout behaves as documented, however it is still not ideal when one side cannot send ping (for example Browser).  Filed Issue #2652  with details and proposed solution (but no code).

---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2023-03-16 03:27:40 UTC  
> Url: https://github.com/boostorg/beast/pull/2651#issuecomment-1471240535  

I don't think you can reuse a stream that timed out to begin with. Are you using it with a websocket or just for raw tcp?

---

## Comment 2

> Username: johannesparty  
> Created_at: 2023-03-16 16:07:26 UTC  
> Url: https://github.com/boostorg/beast/pull/2651#issuecomment-1472265829  

I am successfully reusing a stream with websocket (with the very minor changes suggest in the PR).  I might be reusing it mostly in cases where it is closed independently of timeout.  I call async_connect again on beast::websocket::stream<tcp_socket>.next_layer(), then async_handshake.  I've tested with 1.80.0 and 1.81.0.

---

## Comment 3

> Username: klemens-morgenstern  
> Created_at: 2023-03-17 00:28:16 UTC  
> Url: https://github.com/boostorg/beast/pull/2651#issuecomment-1472936528  

Well just because it works, doesn't mean it'll always work.  
  
I probably need to create some example of a reusable stream with a timeout, because I don't think the tcp_stream is cutting it.

---

## Comment 4

> Username: johannesparty  
> Created_at: 2023-03-17 16:31:59 UTC  
> Url: https://github.com/boostorg/beast/pull/2651#issuecomment-1474098899  

Ok I appreciate your help.  
  
It is interesting that variables including timed_out in stream_impl are set in the constructor, in open(), and in reset() which did lead me to believe the websocket stream could be reused.    
  
Of course, I could create a unique_ptr to the stream or something if I have to, but I would need to understand the rules clearly.  With that said, the changes I made in the PR do seem to work...

---

## Comment 5

> Username: klemens-morgenstern  
> Created_at: 2023-03-19 12:46:14 UTC  
> Url: https://github.com/boostorg/beast/pull/2651#issuecomment-1475243435  

have you looked into per-op cancellation? I personally feel the tcp_stream timeouts are an obsolete design since asio introduced that.

---

## Comment 6

> Username: klemens-morgenstern  
> Created_at: 2023-08-18 07:25:49 UTC  
> Url: https://github.com/boostorg/beast/pull/2651#issuecomment-1683482430  

@johannesparty where are we at with this pr?

---
