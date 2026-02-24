# #1289 - Websocket assertion during read() when close() is called [Closed]

> Username: dmpriso  
> Created at: 2018-11-07 06:50:49 UTC  
> Updated at: 2018-11-08 04:49:08 UTC  
> Closed at: 2018-11-08 04:49:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1289  

### Version of Beast  
144  
### Steps necessary to reproduce the problem  
We are using the synchronous methods to read and write from/to a websocket stream. Specifically, one thread spends most of the time waiting for data in `boost::beast::websocket::stream<Types::Socket&>::read()`, waiting for incoming data.  
We then want to initiate closing of the websocket connection by calling `close` on the same stream (but from another thread obviously). As a result, the server answers with a close frame, and we would expect our `read` method to return after having read the response's close frame.  
  
However, sometimes we run into a debug assertion instead: `read.ipp:1072 BOOST_ASSERT(! wr_close_);`  
  
I don't really look through the beast implementation, but the doc states that `close` *sends a close stream ... blocks until ... The close frame finishes sending.*. However, looking at the implementation, `close` also reads the close response and tries to finish the close handshake. My best bet is that something is getting messy cause of the waiting `read` and the reading `close` both trying to read?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-11-07 11:53:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1289#issuecomment-436599357  

Like all I/O objects.  `websocket::stream`  objects are not thread-safe. Member functions may not be called concurrently.

---

## Comment 2

> Username: dmpriso  
> Created at: 2018-11-07 12:29:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1289#issuecomment-436607624  

According to the docs, albeit not thread-safe, one may have one `read` and one `write` or `close` operation at a given time:  
https://www.boost.org/doc/libs/1_66_0/libs/beast/doc/html/beast/using_websocket/notes.html  
  
If not it would be hardly possible to shutdown a websocket in synchronous mode.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-11-07 12:50:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1289#issuecomment-436612456  

> one may have one read and one write or close operation at a given time:  
  
Those are asynchronous operations

---

## Comment 4

> Username: dmpriso  
> Created at: 2018-11-07 13:01:42 UTC  
> Updated at: 2018-11-07 13:02:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1289#issuecomment-436615369  

OK this renders the synchronous operations useless for our application (and possibly for most others). We will switch to the async calls. Thanks for your fast response!

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-11-07 13:02:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1289#issuecomment-436615539  

Well, that is normal. Synchronous operations are applicable for a smaller range of domains than the asynchronous ones.
