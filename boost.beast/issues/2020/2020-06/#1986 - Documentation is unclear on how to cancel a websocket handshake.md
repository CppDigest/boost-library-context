# #1986 - Documentation is unclear on how to cancel a websocket handshake [Closed]

> Username: madmongo1  
> Created at: 2020-06-14 07:08:42 UTC  
> Updated at: 2024-09-20 19:45:44 UTC  
> Closed at: 2020-07-25 05:51:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1986  

Given:  
  
`ws` is an object of a type which is a specialisation of `boost::beast::websocket::stream<>`  
  
Scenario:  
  
`ws.async_handshake(...)` is in progress.  
  
and   
  
Client program wishes to terminate while being a good citizen.  
  
Should the client program:  
  
a) simply `cancel()` the underlying socket and exit?  
b) wait for the `async_handshake` to complete, remembering the intent to close the connection and then issue `ws.async_close()`?  
c) issue `ws.async_close()` during the handshake?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-06-14 23:07:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1986#issuecomment-643834617  

The only thing you can do is cancel the underlying socket. `async_close` does not work before the handshake is complete.

---

## Comment 2

> Username: stale[bot]  
> Created at: 2020-07-18 05:48:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1986#issuecomment-660431764  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 3

> Username: stale[bot]  
> Created at: 2020-07-25 05:51:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1986#issuecomment-663814205  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!

---

## Comment 4

> Username: uohmak4fvpqe  
> Created at: 2024-09-15 09:37:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1986#issuecomment-2351496899  

> The only thing you can do is cancel the underlying socket. `async_close` does not work before the handshake is complete.  
  
But doing so would prevent a clean shutdown of the ssl layer, or would it ?

---

## Comment 5

> Username: ashtum  
> Created at: 2024-09-15 13:55:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1986#issuecomment-2351604379  

> But doing so would prevent a clean shutdown of the ssl layer, or would it ?  
  
Canceling the handshake operation leaves the stream in an undefined state, which is why only terminal cancellation is allowed. A clean SSL shutdown requires coordination between both sides, but that is no longer possible on this stream.

---

## Comment 6

> Username: uohmak4fvpqe  
> Created at: 2024-09-20 19:45:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1986#issuecomment-2364511833  

It's a bit off the topic of this thread but I was curious to know exactly how to handle disconnection of wss stream.  
  
there are several configurations to take care of:  
- the stream is connecting to tcp : apparently it's ok to just cancel that layer  
- the stream is connected to tcp : same thing  
- the stream is connecting to ssl : based on my tests trying to close ssl will crash, and the assertion is clear that  
I should not make a call while it's connecting, so once again we are left with cancelling the tcp layer I suppose.  
- the stream is connected to ssl : now we can gracefully close the full ssl stack via the ssl layer  
- the stream is connecting to wss : now we can't, again assertions fire. but now we have a ssl session open which we  
can't close properly unless we wait for the outstanding wss handshake to be done.  
- the stream is connected to wss : now we can close, but only when no i/o operation is outstanding, so we need to  
cancel them first and then async_close the wss stack. but in practise the per-op cancellation was taking ages so the only practical way to cancel i/o was to cancel the tcp layer.  
  
So in the end I gave up on all that undocumented complexity and I decided that just cancelling tcp was working for all cases.  
I was concerned with security but it seems to be just fine since the connection is entierly dropped anyway, and obviously the applicative protocol should be designed to always await confirmation for transactions.  
Am I correct ?
