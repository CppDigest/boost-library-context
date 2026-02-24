# #1857 - beast::ssl_stream::write_some() observations [Closed]

> Username: brjoha  
> Created at: 2020-02-25 01:28:42 UTC  
> Updated at: 2020-02-25 17:29:16 UTC  
> Closed at: 2020-02-25 13:03:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1857  

Boost v1.71.0  
  
Hi,   
  
I have a large HTTP request that is written in increments using beast::ssl_stream::write_some().  When the socket gets congested, it seems the behavior is not consistent with the documentation...  
  
https://www.boost.org/doc/libs/1_71_0/libs/beast/doc/html/beast/ref/boost__beast__ssl_stream/write_some/overload2.html  
  
It states that 0 is returned if an error occurs, but I'm seeing both a return value equal to the passed buffer length and a boost::asio::error::would_block error.  
  
Once the socket is writable again, I've tried to resume writing as if no bytes were written as well as if all bytes were written.  Both result in a corrupted SSL stream (sslv3 alert bad record mac).  
  
Am I missing something subtle?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-02-25 02:01:17 UTC  
> Updated at: 2020-02-25 02:01:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1857#issuecomment-590643123  

`beast::ssl_stream` is just a thin wrapper around asio's `ssl_stream`, which combines buffer sequences having length greater than one into single buffer in most cases, which improves performance. And the wrapper is also movable (asio's is not). So any behaviors that you are seeing, are from the underlying asio SSL stream.  
  
Are you setting non-blocking mode on the socket?

---

## Comment 2

> Username: brjoha  
> Created at: 2020-02-25 02:26:46 UTC  
> Updated at: 2020-02-25 02:31:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1857#issuecomment-590649429  

Yes, setting non_blocking (and no_delay).  
  
I'll toddle over to the asio project and ask about this observation there.  
  
Thanks.

---

## Comment 3

> Username: brjoha  
> Created at: 2020-02-25 04:23:54 UTC  
> Updated at: 2020-02-25 04:32:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1857#issuecomment-590676445  

I tried bypassing the wrapper.  Instead of calling...  
   ` beast::ssl_stream<beast::tcp_stream>::write_some()`  
I called...  
    `beast::ssl_stream<beast::tcp_stream>::next_layer().write_some()`  
Which is the asio::ssl_stream if I understand correctly.  
  
This is giving the correct EWOULDBLOCK behavior.  That is, I get back bytes for a partial write and then the next call gives zero bytes and the EWOULDBLOCK result.  Whereas if I call the wrapper, I get back bytes for a complete write as well as the EWOULDBLOCK result.  
  
I'm still getting an SSL error (tlsv1 alert protocol version), but it's different and comes at the end of writing out the complete message.  
  
Does this difference in observed EWOULDBLOCK behavior make sense?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-02-25 05:10:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1857#issuecomment-590686861  

> beast::ssl_stream<beast::tcp_stream>::next_layer().write_some()  
> Which is the asio::ssl_stream if I understand correctly.  
  
No, `beast::ssl_stream::next_layer()` gives you the `tcp_stream`. The beast wrapper keeps the same API as `asio::ssl::stream`.  
  
> Does this difference in observed EWOULDBLOCK behavior make sense?  
  
No, because as I said, the Beast wrapper just forwards all the calls to Asio. `write_some` is a one line function that just calls `ssl::stream::write_some`. See for yourself:  
  
https://github.com/boostorg/beast/blob/c058567ec1b6b767343706cdc58f45e69d491dda/include/boost/beast/ssl/ssl_stream.hpp#L506

---

## Comment 5

> Username: brjoha  
> Created at: 2020-02-25 13:03:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1857#issuecomment-590855617  

OK, thanks for the clarification.  When I saw this statement in the documentation...  
  
https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__ssl_stream/next_layer/overload2.html  
  
"The next layer is the wrapped stream and not the flat_stream used in the implementation."  
  
...It made me think the wrapped asio stream instead of the lower layer TCP stream.  
  
Back to the asio forum...

---

## Comment 6

> Username: brjoha  
> Created at: 2020-02-25 15:33:33 UTC  
> Updated at: 2020-02-25 17:29:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1857#issuecomment-590926767  

Found this discussion...  
  
https://github.com/chriskohlhoff/asio/issues/271  
  
...and everything worked after disabling non-blocking mode.  
  
This is confusing to me though.  Doesn't write_some() become a blocking call if non-blocking mode is not set? Under what circumstances would it ever return a partial write value?  
  
The background here is that an asio::coroutine invokes a 3rd party library, which invokes a registered callback to transmit data.  I don't have a way to pass the asio::yield_context through the 3rd party library to the callback and invoke async_write_some() with it, but thought write_some() in non-blocking mode would work.
