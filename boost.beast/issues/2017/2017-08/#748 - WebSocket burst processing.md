# #748 - WebSocket burst processing [Closed]

> Username: Boronak  
> Created at: 2017-08-18 19:00:32 UTC  
> Updated at: 2017-09-01 03:33:12 UTC  
> Closed at: 2017-09-01 03:33:12 UTC  
> Url: https://github.com/boostorg/beast/issues/748  

I have several applications that receive many small messages over websocket. Sometimes these messages arrive in a burst - or have simply backlogged in the buffers.  
I would like to be able to keep reading frames until there are no frames or partial frames present in any buffer on the local machine and then run a relatively expensive "commit" operation before waiting for new frames.  
Is there a clever way to do this with Beast, or might it be possible to implement in the future?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-08-18 19:50:16 UTC  
> Url: https://github.com/boostorg/beast/issues/748#issuecomment-323445723  

Currently I don't think there's a way, but it might be possible using "non-blocking I/O." You set the socket into non-blocking mode and then read frames until you get the error `boost::asio::error::would_block`. Unfortunately the current version of Beast isn't going to work for you because it fails the stream on any error. At some point I will try to make the non-blocking mode work.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-09-01 03:33:12 UTC  
> Url: https://github.com/boostorg/beast/issues/748#issuecomment-326478674  

There's an open issue to try reactor-style operations so I will go ahead and close this. See: https://github.com/boostorg/beast/issues/445
