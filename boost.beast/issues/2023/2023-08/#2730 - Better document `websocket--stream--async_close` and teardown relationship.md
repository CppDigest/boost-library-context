# #2730 - Better document `websocket::stream::async_close` and teardown relationship [Closed]

> Username: ecorm  
> Created at: 2023-08-28 23:38:58 UTC  
> Updated at: 2025-02-11 08:55:28 UTC  
> Closed at: 2025-02-11 08:55:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2730  

Version of Beast: Boost 1.83.0  
  
The [documentation](https://www.boost.org/doc/libs/1_83_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_close.html) for `websocket::stream::async_close` does not specify what happens to the underlying TCP socket in both success and error paths (same goes for `websocket::stream::close`).   
  
I'm guessing the [teardown customization point](https://www.boost.org/doc/libs/1_83_0/libs/beast/doc/html/beast/using_websocket/teardown.html) has something to do with this, but there is no mention of it in the `async_close` documentation.  
  
In particular, I need to know if the underlying TCP socket is closed when `websocket::stream::async_close` completes either successfully or with an error.  
  
I know that I can do `if (websocket_.next_layer().is_closed())` in the completion handler to find out at runtime, but I'd still like to know the behavior.  
  
ADDENDUM: `websocket::stream::async_close` also does not document whether `is_open() == false` is a postcondition whether or not the operation succeeds. I would expect `is_open() == false` to always be a postcondition whether or not it succeeds.

---

## Comment 1

> Username: ecorm  
> Created at: 2023-08-29 00:40:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2730#issuecomment-1696608115  

I have a question related to this. When `websocket::stream::async_close` fails, will read operations receive an error (so that the application knows to end)?

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2023-08-29 05:32:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2730#issuecomment-1696796088  

What happens to the tcp socket depends on the other side. I think `!is_open()` is not a post-condition.  
  
It sends the close frame to the other side, so the server might decide to just drop the connection at this point without a response (unlikely, but possible) in which case you'd get an error from the tcp layer. This would also apply to your second question.  
  
But ideally, you'd send the close-frame to the server, which then initialized an ordered teardown, then tears down the ssl layer (which google usually skips) and then closes the socket.

---

## Comment 3

> Username: ecorm  
> Created at: 2023-08-29 05:54:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2730#issuecomment-1696814738  

I have refactored my server logic to not depend on `websocket::stream::is_open`.  
  
I now also destroy the `websocket::stream` upon completion of `websocket::stream::async_close` (regardless of success/failure), so that should take care of closing the underlying TCP socket.

---

## Comment 4

> Username: ecorm  
> Created at: 2023-08-29 06:01:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2730#issuecomment-1696820647  

> I now also destroy the `websocket::stream` upon completion of `websocket::stream::async_close` (regardless of success/failure), so that should take care of closing the underlying TCP socket.  
  
Oops, I think that would violate this note from the `websocket::stream` destructor documentation:  
  
> A stream object must not be destroyed while there are pending asynchronous operations associated with it.  
  
...as the `websocket::async_read` operation might still be pending.  
  
The Asio sockets are more robust in this respect, as calling `close` or the destructor cancels all pending asynchronous operations.

---

## Comment 5

> Username: ecorm  
> Created at: 2024-01-12 21:01:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2730#issuecomment-1889944056  

As requested by the author [here](https://github.com/boostorg/beast/issues/2798#issuecomment-1889898238), I'm bumping this issue so that the relationship between `websocket::stream::async_close` and teardown is better documented:  
  
- There's no mention of "teardown" operations in the `websocket::stream::close` documentation, so one needs to have a least glanced at the entire documentation to be aware that his mechanism exists, and _remember_ that it exists. I have 1485 other things I have to constantly keep in my head, so it's easy for me to forget that `websocket::stream::close` performs a customizable teardown operation. :grin:  Just a note with a link to the Teardown page in the `websocket::stream::close` documentation should suffice.  
- It's not clear if the teardown operation is completed or is kicked off by time the `websocket::stream::async_close` handler is invoked.  
- The Teardown documentation page does not describe what the default teardown operation actually does. It sorta implies that the default teardown is per RFC6455 section 7.1.1., but it would be better if this were explicitly stated.  
- It's not clear what state the underlying TCP socket is in if the `websocket::stream::close` operation fails. With Asio, `ip::tcp::socket::close` is a no-fail operation and I can count on the socket being actually closed from my perspective.

---

## Comment 6

> Username: xim  
> Created at: 2024-12-12 09:46:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2730#issuecomment-2538389797  

I've also been confused by this, but wrote some code that appeared to work well for a few years. But then [beast::ssl_stream was deprecated](https://github.com/boostorg/beast/pull/2875/files#diff-e9bfd606f10413507332a3cfe2ae705c85a58395c05fbcf271af49d29c1a436e) and it started crashing in some cases. At least, in new code, it looks like closing a `websocket::stream` will close the socket and everything «just works» if you're using `ssl` or `tcp` as in the examples? =)

---

## Comment 7

> Username: ashtum  
> Created at: 2024-12-12 10:37:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2730#issuecomment-2538506348  

> But then [beast::ssl_stream was deprecated](https://github.com/boostorg/beast/pull/2875/files#diff-e9bfd606f10413507332a3cfe2ae705c85a58395c05fbcf271af49d29c1a436e) and it started crashing in some cases. At least, in new code.  
  
The new changes shouldn't affect this behavior. Did you make any other changes to your code? In what scenario does your code crash? Could you provide a minimal reproducible example?

---

## Comment 8

> Username: xim  
> Created at: 2024-12-13 09:02:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2730#issuecomment-2540927090  

This snippet is the code that triggers the crash, at least in some scenarios  
  
It throws `boost::asio::execution::bad_executor` while handling the `async_shutdown` it appears.  
  
It appears that we might be able to just drop all this extra shutdown and close stuff?  
  
```cpp  
void WebsocketClient::on_close_frame_sent(boost::system::error_code ec)  
{  
    if (ec) {  
        // We could not send a close frame => the connection is not  
        // responsive => there is not going to be a clean teardown.  
        boost::beast::get_lowest_layer(*stream_).close();  
        return;  
    }  
  
    auto & ssl = stream_->next_layer();  
    ssl.next_layer().expires_at(timeout_at_);  
    ssl.async_shutdown(  
        [this, self = shared_from_this()](const boost::system::error_code & ec) {  
            boost::beast::get_lowest_layer(*stream_).close();  
        });  
}  
```

---

## Comment 9

> Username: ashtum  
> Created at: 2024-12-13 09:34:29 UTC  
> Updated at: 2025-02-10 18:14:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2730#issuecomment-2540989958  

> It throws `boost::asio::execution::bad_executor` while handling the `async_shutdown` it appears.  
  
This is probably due to this bug: [boostorg/beast#2925](https://github.com/boostorg/beast/issues/2925). It's fixed in Boost 1.87.0.  
  
> It appears that we might be able to just drop all this extra shutdown and close stuff?  
>   
> void WebsocketClient::on_close_frame_sent(boost::system::error_code ec)  
> {  
>     if (ec) {  
>         // We could not send a close frame => the connection is not  
>         // responsive => there is not going to be a clean teardown.  
>         boost::beast::get_lowest_layer(*stream_).close();  
>         return;  
>     }  
>   
>     auto & ssl = stream_->next_layer();  
>     ssl.next_layer().expires_at(timeout_at_);  
>     ssl.async_shutdown(  
>         [this, self = shared_from_this()](const boost::system::error_code & ec) {  
>             boost::beast::get_lowest_layer(*stream_).close();  
>         });  
> }  
  
You only need to initiate a [websocket::stream::async_close](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_close.html). It handles everything for you.  
  
By the way, this is only necessary if you are the one initiating the close operation. If the peer initiates the close, `async_read` will complete with [websocket::error](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__websocket__error.html)::closed, and no further action is needed.

---

## Comment 10

> Username: xim  
> Created at: 2024-12-13 10:20:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2730#issuecomment-2541098265  

Is that only true if you use a `boost::beast::ip::tcp::socket` or `boost::asio::ssl::stream`? Is it true for `boost::asio::tcp_stream` as well? What about `boost::asio::generic::stream_protocol::socket`?  
  
I have tried to read the documentation mentioned by @ecorm and agree that the documentation should be clear about this stuff. =)

---

## Comment 11

> Username: ashtum  
> Created at: 2024-12-13 10:27:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2730#issuecomment-2541112456  

> Is that only true if you use a `boost::beast::ip::tcp::socket` or `boost::asio::ssl::stream`? Is it true for `boost::asio::tcp_stream` as well? What about `boost::asio::generic::stream_protocol::socket`?  
  
Sorry, which part are you referring to?

---

## Comment 12

> Username: xim  
> Created at: 2024-12-13 10:31:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2730#issuecomment-2541120326  

> Sorry, which part are you referring to?  
  
The part here:  
  
> You only need to initiate a [websocket::stream::async_close](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_close.html). It handles everything for you.  
  
The `async_close` / `teardown customization` stuff doesn't make that perfectly clear.

---

## Comment 13

> Username: ashtum  
> Created at: 2024-12-13 11:40:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2730#issuecomment-2541258860  

> The part here:  
>   
> > You only need to initiate a [websocket::stream::async_close](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_close.html). It handles everything for you.  
  
Yes, this is independent of the type of underlying stream you use with `websocket::stream`.  
  
[websocket::async_teardown](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__websocket__async_teardown/overload1.html) is a customization point that `[async_close](websocket::stream::async_close)` internally uses to close the connection. For instance, when working with `asio::ssl::stream`, it invokes `asio::ssl::stream::async_shutdown` to perform a graceful TLS shutdown. You only need to implement this function if you use a custom stream type. (It already provides a generic implementation for `asio::basic_stream_socket`, so it should work seamlessly with all `asio` stream types.)

---

## Comment 14

> Username: xim  
> Created at: 2024-12-13 11:48:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2730#issuecomment-2541272304  

That's great news. It would be great if the documentation could be equally clear =)

---

## Comment 15

> Username: ashtum  
> Created at: 2025-02-11 08:55:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2730#issuecomment-2650174660  

Addressed in #2981.
