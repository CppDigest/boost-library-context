# #164 - Should ssl::stream::async_shutdown wait for the remote peer answer? [Closed]

> Username: reddwarf69  
> Created at: 2018-11-07 12:46:19 UTC  
> Updated at: 2020-12-30 00:56:49 UTC  
> Closed at: 2020-12-30 00:56:48 UTC  
> Url: https://github.com/boostorg/asio/issues/164  

I don't  think `ssl::stream::async_shutdown` should be waiting to receive the close_notify from the remote peer (https://github.com/boostorg/asio/blob/develop/include/boost/asio/ssl/detail/impl/engine.ipp#L301)  
A common scenario is going to be to have the ssl::stream into my "the buck stops here" class. That class is likely to be doing `ssl::stream::async_read_some` continuosly. At some point I may decide I want to `stop()` this class. Ideally this `stop()` method would simply run `ssl::stream::async_shutdown` and I would find when the peer sends me the close_notify because of a specific ssl error returned by `ssl::stream::async_read_some`. That's the style used by Beast's `websocket::stream::async_close`.  
  
Since `ssl::stream::async_shutdown` also waits for the remote peer to send the close_notify, I am guessing you can't have `ssl::stream::async_shutdown` running/pending at the same time than `ssl::stream::async_read_some`, is this correct? This would make the implementation of `stop()` more complex, requiring it to cancel() the operations in the underlying stream (e.g. `basic_stream_socket::cancel()`), so `ssl::stream::async_read_some` returns... the completion handlers checks if `stop()` has been called and if so calling `ssl::stream::async_shutdown`.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-12-11 15:47:31 UTC  
> Url: https://github.com/boostorg/asio/issues/164#issuecomment-446250421  

You are way overthinking this. Your asynchronous read operations need to have a timer which lets you cancel all I/O if things take too long (call `cancel` on the lowest layer). In this scenario there is no need to go through `async_shutdown` as the host is already unresponsive; what makes you think they will answer the CLOSE_NOTIFY if you aren't even getting any data in the read?  
  
You only call `async_shutdown` under these conditions:  
  
* You get an EOF on a read (means the remote host initiated the shutdown),  
* The last protocol-specific message received from the host indicates that the session is over (for example, if you read a response from an HTTP server with Connection: close specified), or  
* You send a protocol-specific message to the remote host which indicates that the session is over (for example, you send an HTTP response with Connection: close specified)  
  
In all of these cases of `async_shutdown` you need a timeout to cancel all I/O if things take too long. When the timer expires, simply cancel I/O and then let the completion handlers run, then the connection will be destroyed.

---

## Comment 2

> Username: ghost  
> Created at: 2020-12-30 00:56:47 UTC  
> Url: https://github.com/boostorg/asio/issues/164#issuecomment-752290576  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#650](https://github.com/chriskohlhoff/asio/issues/650).
