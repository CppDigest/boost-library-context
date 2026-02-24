# #2188 - how to subscribe to multiple streams websocket [Closed]

> Username: alezz94  
> Created at: 2021-03-14 17:11:25 UTC  
> Updated at: 2022-01-09 05:17:22 UTC  
> Closed at: 2022-01-09 05:17:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2188  

Hi all,   
I am trying to follow this example : libs/beast/example/websocket/client/async-ssl/websocket_client_async_ssl.cpp and I would like to subscribe to multiple streams  using the same websocket ssl connection, but I can't find a way to do that.  
  
This is the code I am using now:  
  
  
```  
int main(){  
    boost::asio::io_context ioc;  
    boost::asio::ssl::context ctx(boost::asio::ssl::context::tlsv12_client);  
    std::make_shared<Connection>(ioc, ctx)->Start_Connection();  
    ioc.run();  
}  
  
void Connection::on_resolve(boost::beast::error_code ec, boost::asio::ip::tcp::resolver::results_type results){  
    if(ec){  
        return fail(ec, "resolve");  
    }  
    std::cout << "Host resolved!" << std::endl;  
    boost::beast::get_lowest_layer(_sock).async_connect(results,   
        boost::beast::bind_front_handler(&Connection::on_connect, shared_from_this()));  
}  
  
  
void Connection::on_connect(boost::beast::error_code ec, boost::asio::ip::tcp::resolver::results_type::endpoint_type ep){  
    if(ec){  
        return fail(ec, "connect");  
    }  
  
    std::cout << "Connected! trying handshake" << std::endl;  
  
  
    //update host string (necessary for handshake)  
    _host += ':' + std::to_string(ep.port());  
  
    boost::beast::get_lowest_layer(_sock).expires_after(std::chrono::seconds(30));  
  
  
    //perform ssl handshake  
    _sock.next_layer().async_handshake(  
        boost::asio::ssl::stream_base::client,  
        boost::beast::bind_front_handler(&Connection::on_ssl_handshake, shared_from_this()));  
  
    // _sock.async_handshake(_host, "/realtime?subscribe=instrument,orderBookL2_25:XBTUSD", boost::beast::bind_front_handler(  
    //     &Connection::on_handshake, shared_from_this()));  
  
}  
  
  
void Connection::on_ssl_handshake(boost::beast::error_code ec){  
    if(ec){  
        return fail(ec, "ssl_handshake");  
    }  
  
    //reccommended timeout settings  
    boost::beast::get_lowest_layer(_sock).expires_never();  
    _sock.set_option(boost::beast::websocket::stream_base::timeout::suggested(boost::beast::role_type::client));  
  
  
    //set decorator to change user-agent of handhsake  
    _sock.set_option(boost::beast::websocket::stream_base::decorator(  
        [](boost::beast::websocket::request_type &req)  
        {  
            req.set(boost::beast::http::field::user_agent, std::string(BOOST_BEAST_VERSION_STRING) +  
                " websocket-client-async-ssl");  
        }));  
  
      
    //perform the websocket handshake  
    _sock.async_handshake(_host, "/realtime?subscribe=instrument,orderBookL2_25:XBTUSD",  
        boost::beast::bind_front_handler(&Connection::on_handshake, shared_from_this()));  
  
}  
  
  
void Connection::on_handshake(boost::beast::error_code ec){  
    if(ec){  
        return fail(ec, "ssl_handshake");  
    }  
  
    do_read();  
  
    // //send the message  
    // _sock.async_write(boost::asio::buffer(_text),  
    //     boost::beast::bind_front_handler(&Connection::on_write, shared_from_this()));  
}  
  
void Connection::on_write(boost::beast::error_code ec, std:: size_t bytes){  
    boost::ignore_unused(bytes);  
    if(ec){  
        return fail(ec, "write");  
    }  
  
    do_read();  
    // //read a message  
    // _sock.async_read(_buffer,   
    //     boost::beast::bind_front_handler(&Connection::on_read, shared_from_this()));  
}  
  
void Connection::on_read(boost::beast::error_code ec, std::size_t bytes){  
    boost::ignore_unused(bytes);  
  
    if(ec){  
        return fail(ec, "read");  
    }  
  
    //print data  
    std::cout << "received: " << boost::beast::make_printable(_buffer.data()) << std::endl;  
  
  
    do_read();  
  
  
    // //close  
    // _sock.async_close(boost::beast::websocket::close_code::normal,  
    //     boost::beast::bind_front_handler(&Connection::on_close, shared_from_this()));  
}  
  
void Connection::on_close(boost::beast::error_code ec){  
    if(ec){  
        return fail(ec, "close");  
    }  
  
    std::cout << "disconnected" << std::endl;  
}  
  
void Connection::fail(boost::beast::error_code ec, const char *where){  
    std::cerr << where << ": " << ec.message() << "\n";  
}  
  
  
Connection::Connection(boost::asio::io_context &ioc, boost::asio::ssl::context &ctx): _resolver(boost::asio::make_strand(ioc)),   
    _sock(boost::asio::make_strand(ioc), ctx) {  
        std::cout << "Connecting to host..." << std::endl;  
}  
  
void Connection::Start_Connection(){  
    _host = "www.bitmex.com";  
    _text = "test";  
    std::cout << "trying to resolve host!" << std::endl;  
    _resolver.async_resolve(_host, "443",   
        boost::beast::bind_front_handler(&Connection::on_resolve, shared_from_this()));  
}  
  
  
void Connection::do_read(){  
  
    _buffer = {};  
  
    _sock.async_read(_buffer,   
        boost::beast::bind_front_handler(&Connection::on_read, shared_from_this()));  
}  
  
  
void Connection::do_write(std::string msg){  
    _sock.async_write(boost::asio::buffer(msg),  
        boost::beast::bind_front_handler(&Connection::on_write, shared_from_this()));  
}  
```  
  
  
My idea was to have in void `Connection::on_ssl_handshake(boost::beast::error_code ec)` to call sock.async_handshake() multiple times   
(e.g.   
```  
_sock.async_handshake(_host, "/realtime?subscribe=instrument,orderBookL2_25:XBTUSD",  
        boost::beast::bind_front_handler(&Connection::on_handshake, shared_from_this()));  
_sock.async_handshake(_host, "/realtime?subscribe=instrument,orderBookL2_25:ETHUSD",  
        boost::beast::bind_front_handler(&Connection::on_handshake, shared_from_this()));  
```  
)  
  
so that in on_read() I could listen to different streams, but that doesn't work. (error: /usr/local/include/boost/beast/core/detail/stream_base.hpp:111: void boost::beast::detail::stream_base::pending_guard::assign(bool&): Assertion `! *b_' failed.  
Aborted (core dumped))  
  
Is there any way I could achieve that?  
  
Thank you for your help!

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-03-14 17:16:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2188#issuecomment-798943802  

You would only async_handshake the ssl stream once.  
  
Which exchange protocol are you talking to? The documentation should detail which frames you can send once you're connected to subscribe to each stream on the same connection.  
  
It looks like you're requesting the stream name in the handshake request. Is this a shorthand, or is this the only way to subscribe to this exchange?

---

## Comment 2

> Username: alezz94  
> Created at: 2021-03-14 17:58:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2188#issuecomment-798950279  

The documentation says the endpoint is : wss://www.bitmex.com/realtime  
 and that a basic command is sent following this format:  
  
{"op": "<command>", "args": ["arg1", "arg2", "arg3"]} (e.g.  
subscribe=orderBookL2_25:XBTUSD, orderBookL2_25:XBTUSD)  
  
I also tried to perform the handshake with "/realtime" and then try to  
write a message using async.write (i.e.  
_sock.async_write(boost::asio::buffer("subscribe=orderBookL2_25:XBTUSD,  
orderBookL2_25:XBTUSD"),boost::beast::bind_front_handler(&Connection::on_write,  
shared_from_this()));)  
  
but I get the following error:  
  
```  
_sock.async_write(boost::asio::buffer("subscribe=orderBookL2_25:XBTUSD,  
orderBookL2_25:XBTUSD"),boost::beast::bind_front_handler(&Connection::on_write,  
shared_from_this()));  
```  
  
Thanks  
  
  
On Sun, 14 Mar 2021 at 17:16, Richard Hodges ***@***.***>  
wrote:  
  
> You would only async_handshake the ssl stream once.  
>  
> Which exchange protocol are you talking to? The documentation should  
> detail which frames you can send once you're connected to subscribe to each  
> stream on the same connection.  
>  
> It looks like you're requesting the stream name in the handshake request.  
> Is this a shorthand, or is this the only way to subscribe to this exchange?  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/issues/2188#issuecomment-798943802>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AEJ7WN7OZJQBXDBKQNYJEVLTDTVOLANCNFSM4ZFFNLBA>  
> .  
>

---

## Comment 3

> Username: madmongo1  
> Created at: 2021-03-14 19:43:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2188#issuecomment-798967459  

> "subscribe=orderBookL2_25:XBTUSD,orderBookL2_25:XBTUSD"  
  
This is not JSON encoded.  
  
The bitmex protocol is JSON.

---

## Comment 4

> Username: alezz94  
> Created at: 2021-03-14 19:46:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2188#issuecomment-798967925  

Yes sorry - I pasted the wrong line!  
This is the string I sent: "{\"op\": \"subscribe\" , \"args\" : [  
\"orderBookL2:XBTUSD\" ] } "  
  
I just tried using _sock.write instead of async_write and that seems to  
work for me with multiple messages:  
```  
std::string message{"{\"op\": \"subscribe\" , \"args\" : [  
\"orderBookL2:XBTUSD\" ] } "};  
std::string message1{"{\"op\": \"subscribe\" , \"args\" : [  
\"orderBookL2:ETHUSD\" ] } "};  
  
_sock.write(boost::asio::buffer(message));  
_sock.write(boost::asio::buffer(message1));  
```  
  
Thanks a lot for the help!  
  
  
On Sun, 14 Mar 2021 at 19:43, Richard Hodges ***@***.***>  
wrote:  
  
> "subscribe=orderBookL2_25:XBTUSD,orderBookL2_25:XBTUSD"  
>  
> This is not JSON encoded.  
>  
> The bitmex protocol is JSON.  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/issues/2188#issuecomment-798967459>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AEJ7WN77Q2UNRNDRXJSAQL3TDUGXZANCNFSM4ZFFNLBA>  
> .  
>

---

## Comment 5

> Username: madmongo1  
> Created at: 2021-03-14 20:08:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2188#issuecomment-798971200  

You must choose weither sync or async. You should not mix async reads with sync writes.  
However if you're doing async writes you must remember to manage a queue of writes. Only one must be in progress at a time.  
Here is some example code.  
(this was come code I wrote to help someone else who was connecting to a different crypto exchange)  
[https://github.com/test-scenarios/boost_beast_websocket_echo/blob/master/pre-cxx20/fmex_client/connection_base.cpp](https://github.com/test-scenarios/boost_beast_websocket_echo/blob/master/pre-cxx20/fmex_client/connection_base.cpp)

---

## Comment 6

> Username: stale[bot]  
> Created at: 2021-05-29 16:10:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2188#issuecomment-850857855  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 7

> Username: stale[bot]  
> Created at: 2022-01-09 05:16:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2188#issuecomment-1008232410  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
