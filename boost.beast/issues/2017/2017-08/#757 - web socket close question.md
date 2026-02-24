# #757 - web socket close question [Closed]

> Username: crashtestdummy32  
> Created at: 2017-08-30 15:13:06 UTC  
> Updated at: 2017-08-30 17:11:22 UTC  
> Closed at: 2017-08-30 16:51:16 UTC  
> Url: https://github.com/boostorg/beast/issues/757  

beast: v105  
boost: v62  
compiler: gcc 4.9.4  
  
# preface:  
  
wasn't sure where to post this. this probably is more of a misuse of beast/asio but i'm posting it here.   
  
i'm new to boost/asio/beast.  
  
# background:  
  
i'm following the asynchronous design laid out in this example:  
http://www.boost.org/doc/libs/1_57_0/doc/html/boost_asio/example/cpp03/timeouts/async_tcp_client.cpp  
  
# problem:  
calling websocket::stream::async_close asserts if the websocket hasn't connected to the endpoint.  
  
# why i am calling close if i haven't connected:   
to cause all of the work items to get expunged with operation_aborted. namely the connection timeout deadline and the async_connect request (or async_handshake request).  
  
# chronologic psuedo-code problem  
  
deadline_.expires_from_now(boost::posix_time::seconds(60));  
deadline_.async_wait(&client::connection_timeout_handler);  
websocket.async_connect(connection_handler);  
...  
//after some time, the timeout handler fires  
void connection_timeout_handler()  
{  
      // The deadline has passed. The socket is closed so that any outstanding  
      // asynchronous operations are cancelled. (namely the connection request in this example)  
  websocket.async_close(...);  
}  
  
that call immediately asserts in close.ipp:148 => BOOST_ASSERT(d.ws.wr_block_ != d.tok);  
  
# solution?:  
i think i need to keep track of whether i've successfully connected, or possibly successfully handshaked, then before calling websocket::stream::async_close, check if i haven't successfully connected/handshaked, and instead maybe i need to call websocket::stream::next_layer::async_close.  
  
  
sorry for raising the issue when it's probably just a misuse. i tried googling it but found nothing.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-08-30 15:27:52 UTC  
> Url: https://github.com/boostorg/beast/issues/757#issuecomment-326027146  

>to cause all of the work items to get expunged with operation_aborted. namely the connection timeout deadline and the async_connect request (or async_handshake request).  
  
The key piece of information is that `websocket::stream::close` and `websocket::stream::async_close` perform a websocket **protocol level** close operation. That means, the implementation sends a websocket "close frame" and waits for the remote host to send back a "close frame." This is completely different from the function of `boost::asio::ip::tcp::socket::close`, which just closes the file handle; admittedly, they both have the same name so I can understand the confusion.  
  
Given this declaration:  
```  
boost::beast::websocket::stream<boost::asio::ip::tcp::socket> ws;  
```  
  
The function `ws.next_layer()` has return type `boost::asio::ip::tcp::socket&`. So if you want to close the socket and cancel all pending I/O operations, you can write:  
  
```  
ws.next_layer().close(ec);  
```  
  
or  
  
```  
ws.next_layer().close();  
```

---

## Comment 2

> Username: djarek  
> Created at: 2017-08-30 15:37:20 UTC  
> Url: https://github.com/boostorg/beast/issues/757#issuecomment-326030267  

BTW, shouldn't async_close return an appropriate error instead of asserting if the connection was never established?

---

## Comment 3

> Username: crashtestdummy32  
> Created at: 2017-08-30 15:43:21 UTC  
> Url: https://github.com/boostorg/beast/issues/757#issuecomment-326032432  

thanks for the quick response. as noted in my original post, i suspected that was the issue.  
so i'll keep track of whether the websocket upgrade succeeded and only call websocket::stream::close or the likes thereafter.  
  
is it safe to assume that, once having established a succesful tcp connection, and once having successfully negotiated the websocket upgrade, that one can always close websocket::stream::close or websocket::stream::close_async without the same assertion happening.  
  
the race i imagine might be possible, if the following happened chronologically:  
1. tcp successfully connects  
2. http websocket upgrade succeeds. user has full blown websocket in tact.  
3. the endpoint closes the socket right as the deadline timer expires.  
3. possibly the deadline timer work item is handled by asio before the read_async handler is handled with the operation_aborted error.  
4. user would then call close on the websocket but the tcp socket is in a closed state (probably different internally than never having connecte).  
5. maybe same assertion hit?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-08-30 16:04:37 UTC  
> Url: https://github.com/boostorg/beast/issues/757#issuecomment-326038868  

>BTW, shouldn't async_close return an appropriate error instead of asserting if the connection was never established?  
  
Yes, I believe @anonstupid8675 is using an older version of Beast:  
https://github.com/boostorg/beast/blob/c8a42bba0ac33b5ca749aa96b1d490b0db7e8cd8/include/boost/beast/websocket/impl/close.ipp#L348

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-08-30 16:09:07 UTC  
> Updated at: 2017-08-30 16:09:28 UTC  
> Url: https://github.com/boostorg/beast/issues/757#issuecomment-326040213  

>i'll keep track of whether the websocket upgrade succeeded and only call websocket::stream::close or the likes thereafter.  
  
Right  
  
>is it safe to assume that, once having established a succesful tcp connection, and once having successfully negotiated the websocket upgrade, that one can always close websocket::stream::close or websocket::stream::close_async without the same assertion happening.  
  
Yes, that should be safe, as long as no previous call to stream read or write functions returned an error.  
  
>user would then call close on the websocket but the tcp socket is in a closed state  
  
You should not be calling `websocket::stream::close` or `websocket::stream::async_close` when the timer expires, because that is a websocket protocol level close not a socket close. You should be calling `close` on the underlying TCP/IP socket, which you can usually obtain by calling `stream::next_layer`.  
  
If you want to implement timeouts for idle connections, then when your timer expires you should be calling `async_ping`, after setting the "control frame callback" function:  
http://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/control_callback/overload1.html  
  
When you receive a ping or pong in your control frame callback, simply reset the timer. If the timer expires after sending a ping, with no pong received, then you can close the socket (by calling `next_layer().close()`).

---

## Comment 6

> Username: crashtestdummy32  
> Created at: 2017-08-30 16:51:16 UTC  
> Updated at: 2017-08-30 16:51:29 UTC  
> Url: https://github.com/boostorg/beast/issues/757#issuecomment-326051788  

> Yes, I believe @anonstupid8675 is using an older version of Beast:  
  
i pulled the latest, 105->110, and that crash is now handled gracefully (as are a few others).    
  
sorry it took me a minute to fully grasp the nature of the websocket close. i should have read the rfc first. i assumed it kind of handled either case. apologies.  
  
thanks for walking me through this. sorry for the hassle.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2017-08-30 17:11:22 UTC  
> Url: https://github.com/boostorg/beast/issues/757#issuecomment-326057573  

No hassle at all, if anything, it means I should make the distinction between the different flavors of `close` more clear in the docs - thanks!
