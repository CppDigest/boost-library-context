# #423 - How to gracefully shutdown WSS client [Closed]

> Username: WojciechMigda  
> Created at: 2017-06-05 15:06:03 UTC  
> Updated at: 2018-11-05 11:37:40 UTC  
> Closed at: 2017-07-04 02:53:57 UTC  
> Url: https://github.com/boostorg/beast/issues/423  

### Version of Beast  
47  
  
I have a C++ client which connects to a WSS server and asynchronously receives periodic messages.  
The client runs in a separate thread.  
WSS Connection is setup/torn down in response to commands received over AZMQ socket pair.  
WSS session is kept in unique pointers:  
  
    struct WssState  
    {  
        using socket_type = boost::asio::ip::tcp::socket;  
        using socket_pointer = std::unique_ptr<socket_type>;  
      
        using ssl_stream_type = boost::asio::ssl::stream<socket_type &>;  
        using ssl_stream_pointer = std::unique_ptr<ssl_stream_type>;  
      
        using ssl_ctx_type = boost::asio::ssl::context;  
        using ssl_ctx_pointer = std::unique_ptr<ssl_ctx_type>;  
      
        using wss_stream_type = beast::websocket::stream<ssl_stream_type&>;  
        using wss_stream_pointer = std::unique_ptr<wss_stream_type>;  
      
        socket_pointer m_socket_p;  
        ssl_stream_pointer m_ssl_stream_p;  
        ssl_ctx_pointer m_ssl_ctx_p;  
        wss_stream_pointer m_wss_stream_p;  
    };  
  
All is ok, until I attempt to tear the session down. My intent is to have all four unique pointers being null after close, but so far I cannot come up with a way to achieve that without segfault.  
  
If upon receiving 'close' command I do the [following](https://stackoverflow.com/questions/15312219/need-to-call-sslstreamshutdown-when-closing-boost-asio-ssl-socket):  
  
    m_ssl_stream_p->lowest_layer().cancel();  
    m_wss_stream_p = nullptr;  
    m_ssl_ctx_p = nullptr;  
  
the reader handler receives `boost::asio::error::operation_aborted` error code and exits, and there is no segfault.  
  
When I attempt to reset ssl stream pointer as well  
  
    state.m_ssl_stream_p = nullptr;  
  
I end up with a segfault.  
The read handler is being run wrapped in a strand.  
Is there a suggested way to gracefully shutdown WSS client so that all resources are being released in a proper order? I would like to ensure that I am doing it by the book.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-05 15:26:10 UTC  
> Url: https://github.com/boostorg/beast/issues/423#issuecomment-306217733  

>If upon receiving 'close' command  
  
What close command? Is this a custom command you are sending over a separate communication channel (ZMQ) or is this a WebSocket close?

---

## Comment 2

> Username: WojciechMigda  
> Created at: 2017-06-05 15:33:50 UTC  
> Url: https://github.com/boostorg/beast/issues/423#issuecomment-306219907  

Yes, exactly, a custom command I send over ZMQ to the client thread so that it closes WSS stream.

---

## Comment 3

> Username: WojciechMigda  
> Created at: 2017-06-05 15:36:31 UTC  
> Url: https://github.com/boostorg/beast/issues/423#issuecomment-306220615  

At this point I am interested in learning whether one should pay special attention to closing WSS stream, and if so, then what are the specifics beyond just having respective destructors being called. Initially, that was my assumption that just releasing resources wrapped with unique pointers would suffice, but it appears there more to that.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-06-18 04:45:20 UTC  
> Updated at: 2017-06-18 04:45:44 UTC  
> Url: https://github.com/boostorg/beast/issues/423#issuecomment-309256564  

You need to send a close frame and then "drain" the connection, here is the ssl client which works. This will be merged to master shortly.  
  
```  
#include <beast/core.hpp>  
#include <beast/websocket.hpp>  
#include <beast/websocket/ssl.hpp>  
#include <boost/asio.hpp>  
#include <boost/asio/ssl.hpp>  
#include <cstdlib>  
#include <iostream>  
#include <string>  
  
int main()  
{  
    // A helper for reporting errors  
    auto const fail =  
        [](std::string what, beast::error_code ec)  
        {  
            std::cerr << what << ": " << ec.message() << std::endl;  
            std::cerr.flush();  
            return EXIT_FAILURE;  
        };  
  
    boost::system::error_code ec;  
  
    // Set up an asio socket to connect to a remote host  
    std::string const host = "echo.websocket.org";  
    boost::asio::io_service ios;  
    boost::asio::ip::tcp::resolver r{ios};  
    boost::asio::ip::tcp::socket sock{ios};  
  
    // Look up the domain name  
    auto const lookup = r.resolve(boost::asio::ip::tcp::resolver::query{host, "https"}, ec);  
    if(ec)  
        return fail("resolve", ec);  
  
    // Make the connection on the IP address we get from a lookup  
    boost::asio::connect(sock, lookup, ec);  
    if(ec)  
        return fail("connect", ec);  
  
    // Wrap the now-connected socket in an SSL stream  
    using stream_type = boost::asio::ssl::stream<boost::asio::ip::tcp::socket&>;  
    boost::asio::ssl::context ctx{boost::asio::ssl::context::sslv23};  
    stream_type stream{sock, ctx};  
    stream.set_verify_mode(boost::asio::ssl::verify_none);  
  
    // Perform SSL handshaking  
    stream.handshake(boost::asio::ssl::stream_base::client, ec);  
    if(ec)  
        return fail("ssl handshake", ec);  
  
    // Now wrap the handshaked SSL stream in a websocket stream  
    beast::websocket::stream<stream_type&> ws{stream};  
  
    // Perform the websocket handshake  
    ws.handshake(host, "/", ec);  
    if(ec)  
        return fail("handshake", ec);  
  
    // Send a message  
    ws.write(boost::asio::buffer("Hello, world!"), ec);  
    if(ec)  
        return fail("write", ec);  
  
    // This buffer will hold the incoming message  
    beast::multi_buffer b;  
  
    // Read the message into our buffer  
    ws.read(b, ec);  
    if(ec)  
        return fail("read", ec);  
  
    // Send a "close" frame to the other end, this is a websocket thing  
    ws.close(beast::websocket::close_code::normal, ec);  
    if(ec)  
        return fail("close", ec);  
  
    // The buffers() function helps print a ConstBufferSequence  
    std::cout << beast::buffers(b.data()) << std::endl;  
  
    // WebSocket says that to close a connection you have  
    // to keep reading messages until you receive a close frame.  
    // Beast delivers the close frame as an error from read.  
    //  
    beast::drain_buffer drain; // Throws everything away efficiently  
    for(;;)  
    {  
        // Keep reading messages...  
        ws.read(drain, ec);  
  
        // ...until we get the special error code  
        if(ec == beast::websocket::error::closed)  
            break;  
  
        // Some other error occurred, report it and exit.  
        if(ec)  
            return fail("close", ec);  
    }  
  
    return EXIT_SUCCESS;  
}  
```

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-07-04 02:53:57 UTC  
> Url: https://github.com/boostorg/beast/issues/423#issuecomment-312768353  

This was merged, so there's now a good working example of how to close the session cleanly. If you still have problems feel free to open a new issue. I'll close this for now and keep things tidy! Thanks!

---

## Comment 6

> Username: WojciechMigda  
> Created at: 2017-07-16 16:50:36 UTC  
> Url: https://github.com/boostorg/beast/issues/423#issuecomment-315621617  

Thank you, I've just returned from long-ish vacation. I will try this shortly.

---

## Comment 7

> Username: WojciechMigda  
> Created at: 2017-07-30 11:18:59 UTC  
> Url: https://github.com/boostorg/beast/issues/423#issuecomment-318895170  

I think the problem with my segfault is rooted in the async read handler baing called with end of file/stream code even though closing and draining of wss stream is completed. Is there a way to "undo" async_read binding of the handler?

---

## Comment 8

> Username: vinniefalco  
> Created at: 2017-07-30 12:57:43 UTC  
> Url: https://github.com/boostorg/beast/issues/423#issuecomment-318900157  

You can cancel the operation, but the completion handler will still be called with the error `boost::asio::error::operation_aborted`. The caller is responsible for managing the lifetime of the stream or socket to make sure that it is not destroyed until all the completion handlers have been called. Usually this is done by using a `shared_ptr` bound to the handler. For example:  
https://github.com/boostorg/beast/blob/9e29a52fcb01791aaf3693bd785409a7f3339136/example/websocket-server-async/websocket_server_async.cpp#L164  
  
Note the call to `shared_from_this`.  
  
Thanks

---

## Comment 9

> Username: quazeeee  
> Created at: 2018-07-16 11:24:02 UTC  
> Url: https://github.com/boostorg/beast/issues/423#issuecomment-405217092  

Hello, i have issue with drain buffer (no type named 'drain_buffer' in namespace 'boost::beast'). It is still actual type of buffer?

---

## Comment 10

> Username: vinniefalco  
> Created at: 2018-07-16 13:48:03 UTC  
> Url: https://github.com/boostorg/beast/issues/423#issuecomment-405253107  

That stuff was removed, the websocket stream now does this for you automatically. You do not need to take any special action.

---

## Comment 11

> Username: quazeeee  
> Created at: 2018-07-16 13:56:57 UTC  
> Url: https://github.com/boostorg/beast/issues/423#issuecomment-405255940  

Thank you for fast response! I can just write "ws.close(beast::websocket::close_code::normal, ec);" and after call return the socket will fully closed ?

---

## Comment 12

> Username: vinniefalco  
> Created at: 2018-07-16 15:14:36 UTC  
> Url: https://github.com/boostorg/beast/issues/423#issuecomment-405281748  

Yes

---

## Comment 13

> Username: litwr2  
> Created at: 2018-09-14 12:06:36 UTC  
> Url: https://github.com/boostorg/beast/issues/423#issuecomment-421338300  

I use "ws.close(beast::websocket::close_code::normal, ec);" after "ws.next_layer().next_layer().cancel(ec);" and get a "short read" error.  If I use "ws.next_layer().next_layer().shutdown(boost::asio::ip::tcp::socket::shutdown_both, ec)" instead of cancel() I get a "broken pipe" error. Please help to find out how to close the socket.  BTW should I use "ws_.next_layer().next_layer().release(ec);" after close()?

---

## Comment 14

> Username: tombouctou  
> Created at: 2018-11-05 11:37:40 UTC  
> Url: https://github.com/boostorg/beast/issues/423#issuecomment-435844871  

@vinniefalco Hi, We have the following problem closing sockets (after error): we send shutdown signal, receive error  
```  
ws_.next_layer().next_layer().shutdown(boost::asio::ip::tcp::socket::shutdown_both, ec);  
```  
We have this shutdown error: Bad file descriptor (ec=9)  
Then we call  
```  
ws_.close(boost::beast::websocket::close_code::normal, ec);  
```  
And get "Operation canceled"  
  
But we still have file descriptor open after that (lsof shows growing number of descriptors - we do reconnect on every error).  
We decided to close file descriptor manually (after calling `ws_.close()`), is there any other 'beast-way' to ensure fd is closed?  
  
Btw, code above (using `beast::drain_buffer drain`) is outdated, I see it was removed in this commit: https://github.com/boostorg/beast/commit/de03a1a32d6128e32363ddc2688c59bea219f91c
