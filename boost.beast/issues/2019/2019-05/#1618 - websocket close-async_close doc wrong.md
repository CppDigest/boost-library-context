# #1618 - websocket close/async_close doc wrong [Closed]

> Username: vinniefalco  
> Created at: 2019-05-22 21:07:53 UTC  
> Updated at: 2025-04-27 16:35:31 UTC  
> Closed at: 2025-04-27 16:35:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1618  

The javadocs are outdated, they state that the close frame is only written. The current implementation also reads and waits to receive a close frame, then performs a teardown.

---

## Comment 1

> Username: jpramosi  
> Created at: 2020-03-16 12:56:18 UTC  
> Updated at: 2020-03-16 13:03:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1618#issuecomment-599521118  

I think the documentation for async_close should also state:  
  
-when it makes sense to call this function respectively when it does has an effect  
-where it is allowed to use it  
-are there any caveats (for e.g. check if stream is open or an error_code check is required from   
 read/write handler)  
  
Currently it looks like the examples for session / server related code doesn't make use of async_close.  
After i have tried to implement a close function for server sessions, i came up with this but not bullet-proof solution which doesn't fire an assert:  
```cpp  
void on_async_read()  
{  
    // ...  
  
    if (m_read_async_close)  
    {  
        // Maybe it needs additional checks  
        // with a wrapper function  
        m_stream.async_close(  
            websocket::close_code::normal,  
            boost::beast::bind_front_handler(&session::on_async_close,  
                                             this->shared_from_this()));  
        return;  
    }  
  
    //...  
  
    // Next read  
    m_stream.async_read(  
        m_read_buffer,  
        boost::beast::bind_front_handler(&session::on_async_read,  
                                            this->shared_from_this()));  
}  
  
void on_async_close(boost::beast::error_code ec)  
{  
}  
  
void handle_close()  
{  
    m_read_async_close = true;  
    m_stream.next_layer().cancel();  
    // respectively   
    // m_stream.next_layer().next_layer().cancel();  
}  
  
void close()  
{  
    boost::asio::post(m_stream.get_executor(),  
                        boost::beast::bind_front_handler(  
                            &session::handle_close,  
                            this->shared_from_this()));  
}  
```  
Using async_close just with post and possibly other handlers (on_async_write) triggers this assert in my stress tests:  
https://github.com/boostorg/beast/blob/79e6c61db5d8d9ba12ad9d2371d34e4bc5a4b9ff/include/boost/beast/websocket/detail/impl_base.hpp#L328  
```  
BOOST_BEAST_VERSION 277 a.k.a Boost 1.72.0  
/usr/local/include/boost/beast/websocket/detail/impl_base.hpp:328:   
std::size_t boost::beast::websocket::detail::impl_base<true>::read_size_hint_pmd(  
std::size_t, bool, std::uint64_t, const detail::frame_header &) const:   
Assertion `rd_remain > 0' failed.  
```  
However this happens only for plain websocket sessions (dunno if this is relevant).  
  
A few references:  
https://github.com/boostorg/beast/blob/1e5b4f531d620879e152979351df920ce339d018/example/websocket/client/async/websocket_client_async.cpp#L171  
https://github.com/boostorg/beast/blob/ff2c0ca91e445812d49de68beeed7942fc8d10e2/test/beast/websocket/test.hpp#L138

---

## Comment 2

> Username: madmongo1  
> Created at: 2020-03-16 14:16:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1618#issuecomment-599560500  

Hi @reapler ,  
  
Would you be in a position to share a complete test program that demonstrates the assert?  
  
If your program has only one of each async operation outstanding at one time we are surprised that this happens.  
  
I would like to investigate ASAP.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-03-16 14:18:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1618#issuecomment-599561694  

That's asking a lot, but I guess it can't hurt to ask :)

---

## Comment 4

> Username: madmongo1  
> Created at: 2020-03-16 15:11:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1618#issuecomment-599590567  

@reapler Actually you may have uncovered a bug. I am investigating.

---

## Comment 5

> Username: jpramosi  
> Created at: 2020-03-16 15:36:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1618#issuecomment-599603833  

@madmongo1 I will try to boil this down to an example if i can. At the first sight it doesn't seem to be an issue with concurrent async operations. async_close can only be called once in  write/read/timer handler and further async operations concerning read, write, ping or other are stopped with a variable in its handlers.  
I will add later more information to aid here.  
Thank you

---

## Comment 6

> Username: jpramosi  
> Created at: 2020-03-16 17:27:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1618#issuecomment-599665477  

I have found the problem at my side.. moved a part of the code which returns if close was initiated to a separate function, the check was ignored and caused additional async_read after async_close.  
Now async_close does work also on other handlers without this cancel trickery.  
I guess this is what the triggered assert actually tried to tell me :)  
I'm sorry that i have wasted your time. In the future i need to dig deeper in my code, dang

---

## Comment 7

> Username: madmongo1  
> Created at: 2020-03-16 18:47:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1618#issuecomment-599702789  

@reapler fantastic news. You didn't waste my time at all. It gave me a reason to familiarise myself with the websocket code.  
Thanks for letting us know. If you need any more help don't hesitate.  
We're on slack in the #beast channel.  
https://cppalliance.org/slack/

---

## Comment 8

> Username: vinniefalco  
> Created at: 2020-03-16 20:00:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1618#issuecomment-599733380  

> I'm sorry that i have wasted your time.  
  
Actually, this helped us identify some small improvements, so it wasn't a waste of time.

---

## Comment 9

> Username: ashtum  
> Created at: 2025-04-27 16:35:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1618#issuecomment-2833538896  

Addressed in #2981.
