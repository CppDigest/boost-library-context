# #154 - Does asio::async_read need extra support for cancellation [Closed]

> Username: KategoryBee  
> Created at: 2018-10-18 06:53:36 UTC  
> Updated at: 2020-12-30 00:53:53 UTC  
> Closed at: 2020-12-30 00:53:51 UTC  
> Url: https://github.com/boostorg/asio/issues/154  

Suppose I have an `asio::async_read` running that I wish to cancel, and call the `.cancel()` function on the socket. Is it possible that as I call cancel the async_read is between finishing one read and starting the next (since it's a composed operation)?   
  
Does asio already protect against this, or do I need to hook in to `CompletionCondition` somehow to prevent this

---

## Comment 1

> Username: djarek  
> Created at: 2018-10-18 20:33:11 UTC  
> Updated at: 2018-10-18 20:33:49 UTC  
> Url: https://github.com/boostorg/asio/issues/154#issuecomment-431152242  

In theory, it is possible for the composed operation to be scheduled on your Executor behind the operation that performs `cancel()` which would cause it to be missed. In practice, it's going to be fairly rare.  
  
 Note, that calling `cancel()` on the underlying socket that is wrapped into a stream e.g. `boost::asio::ssl::stream<socket>` will result in the "top" stream being left in an invalid state.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-12-11 15:48:59 UTC  
> Updated at: 2018-12-11 15:49:10 UTC  
> Url: https://github.com/boostorg/asio/issues/154#issuecomment-446251044  

You can only cancel **all** I/O, you cannot cancel an individual read. Furthermore, socket objects are _not thread-safe_. You have to call `cancel` from the same implicit or explicit strand as that used for the other operations.

---

## Comment 3

> Username: ghost  
> Created at: 2020-12-30 00:53:50 UTC  
> Url: https://github.com/boostorg/asio/issues/154#issuecomment-752290029  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#644](https://github.com/chriskohlhoff/asio/issues/644).
