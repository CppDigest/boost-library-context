# #1215 - websocket timeouts are difficult to implement [Closed]

> Username: vinniefalco  
> Created at: 2018-08-03 22:52:50 UTC  
> Updated at: 2019-02-23 14:39:11 UTC  
> Closed at: 2019-02-23 14:39:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1215  

( From a discussion in #1213 )  
  
Beast websocket stream certainly doesn't make it any easier for users to correctly implement timeouts which cooperate with the ping/pong mechanism and the "Close the WebSocket Connection" algorithm. Users need a better way.

---

## Comment 1

> Username: litwr2  
> Created at: 2018-08-24 13:45:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1215#issuecomment-415763395  

Dear Vinnie  
Thank you very much for beast library.  I am not sure that my problem exactly fits the topic but it is definitely close to it.  I need a synchronous read operation with timeout, something like this ([https://stackoverflow.com/questions/13126776/asioread-with-timeout](https://stackoverflow.com/questions/13126776/asioread-with-timeout))  
  
    template <typename MutableBufferSequence>  
    bool read_with_timeout(tcp::socket& sock, const MutableBufferSequence& buffers, unsigned ms)  
    {  
        boost::optional<boost::system::error_code> timer_result;  
        boost::asio::deadline_timer timer(sock.get_io_service());  
        timer.expires_from_now(boost::posix_time::milliseconds(ms));  
        timer.async_wait([&timer_result] (const boost::system::error_code& error) { timer_result.reset(error); });  
        boost::optional<boost::system::error_code> read_result;  
        boost::asio::async_read(sock, buffers, [&read_result]  
            (const boost::system::error_code& error, size_t) { read_result.reset(error); });  
  
       sock.get_io_service().reset();  
       while (sock.get_io_service().run_one())  
       {  
           if (read_result)  
               timer.cancel();  
           else if (timer_result)  
               sock.cancel();  
       }  
       if (*read_result)  
           return false;  
       return true;  
    }  
  
I have written the next terrible code  
  
    template <typename MutableBufferSequence>  
    bool read_with_timeout(websocket::stream<ssl::stream<tcp::socket>>& sock, const MutableBufferSequence& buffers, unsigned ms)  
    {  
        boost::optional<boost::system::error_code> timer_result;  
        boost::asio::deadline_timer timer(sock.next_layer().next_layer().get_io_service());  
        timer.expires_from_now(boost::posix_time::milliseconds(ms));  
        timer.async_wait([&timer_result] (const boost::system::error_code& error) { timer_result.reset(error); });  
  
        boost::optional<boost::system::error_code> read_result;  
        boost::asio::async_read(sock, buffers, [&read_result]  
            (const boost::system::error_code& error, size_t) { read_result.reset(error); });  
  
        sock.next_layer().next_layer().get_io_service().reset();  
        while (sock.next_layer().next_layer().get_io_service().run_one())  
        {  
            if (read_result)  
                timer.cancel();  
            else if (timer_result)  
               sock.next_layer().next_layer().cancel();  
       }  
  
        if (*read_result)  
            return false;  
        return true;  
    }  
  
Indeed it doesn't work because of **cancel**, beast doesn't have it and we need its functionality for websocket.  Is there any way to solve my problem?  
It looks like that we will have problems with the implementation of work with timeout even with asynchronous read.  Is it true?  A lot of thanks in advance for any hints.  
Regards,

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-08-24 17:07:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1215#issuecomment-415821583  

`beast::websocket::stream` doesn't need a cancel, you just need to call cancel on the underlying stream which it looks like you are doing:  
```  
sock.next_layer().next_layer().cancel();  
```

---

## Comment 3

> Username: litwr2  
> Created at: 2018-08-25 17:06:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1215#issuecomment-415983276  

Thank you but it doesn't work - it gives an error (_an exception EXC_BAD_ACCESS (code=EXC_I386_GPFLT)_) at **sock.next_layer().next_layer().get_io_service().run_one()** call. Sometimes it happens after a call to **sock.next_layer().next_layer().cancel()**...  BTW I am not familiar with POCO library but I like the idea [https://pocoproject.org/docs/Poco.Net.StreamSocket.html#25800](https://pocoproject.org/docs/Poco.Net.StreamSocket.html#25800).

---

## Comment 4

> Username: vinniefalco  
> Created at: 2018-08-25 20:08:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1215#issuecomment-415993516  

Are there any other threads calling `io_context::run()`? If you are getting an exception, you should be able to debug it to find out why the bad access is occurring. You might need to step into the Asio sources for that.

---

## Comment 5

> Username: Eelis  
> Created at: 2018-10-13 08:13:57 UTC  
> Updated at: 2018-10-13 08:27:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1215#issuecomment-429521428  

@vinniefalco You mentioned in #1213 that  
> the advanced-server and advanced-server-flex examples are wrong, and fixing them is non-trivial. This is not ideal and I think it needs to be simplified.  
  
Simplification sounds great! But if it is going to take a while to do that, then I think it would be valuable to fix the examples in the mean time, especially if it's nontrivial to do so. That way, users like me at least have a chance of using the library in a correct way with confidence (by emulating the fixed examples), and won't have to wait for the simplification before they can actually deploy websocket servers implemented with Beast.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2018-10-13 12:14:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1215#issuecomment-429537070  

Yes, a fix for this was merged recently:  
https://github.com/boostorg/beast/commit/2b977f27703bb937e270308a87d02093693b1cae

---

## Comment 7

> Username: Eelis  
> Created at: 2018-10-13 17:46:57 UTC  
> Url: https://github.com/boostorg/beast/issues/1215#issuecomment-429561377  

Ah great, thanks!

---

## Comment 8

> Username: vinniefalco  
> Created at: 2019-02-23 14:39:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1215#issuecomment-466657239  

This should all be fixed now that websocket has built-in configurable timeouts. If you have trouble getting them to work, or if you find a bug please feel free to open a new issue, thanks!
