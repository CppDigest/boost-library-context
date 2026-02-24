# #333 Fix compile error when use with asio ssl stream. [Closed]

> Username: Aleksei-Badyaev  
> Created at: 2017-05-02 12:00:04 UTC  
> Updated at: 2017-05-04 11:37:28 UTC  
> Closed at: 2017-05-04 09:30:10 UTC  
> Url: https://github.com/boostorg/beast/pull/333  

Without this fix, I got compile error when using Beast with Boost.asio ssl stream, because `asio::ssl::stream` is not copyable:  
  
`In file included from /home/alexey/projects/dinect/Beast/include/beast/core/dynabuf_readstream.hpp:361:0,  
                 from /home/alexey/projects/dinect/Beast/include/beast/websocket/stream.hpp:16,  
                 from /home/alexey/projects/dinect/Beast/include/beast/websocket.hpp:16,  
                 from src/subscription.hpp:8,  
                 from src/subscription.cpp:5:  
/home/alexey/projects/dinect/Beast/include/beast/core/impl/dynabuf_readstream.ipp: In instantiation of ‘beast::dynabuf_readstream<Stream, DynamicBuffer>::dynabuf_readstream(Args&& ...) [with Args = {boost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::stream_socket_service<boost::asio::ip::tcp> > >&}; Stream = boost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp> >; DynamicBuffer = beast::basic_streambuf<std::allocator<char> >]’:  
/home/alexey/projects/dinect/Beast/include/beast/websocket/impl/stream.ipp:39:42:   required from ‘beast::websocket::stream<NextLayer>::stream(Args&& ...) [with Args = {boost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::stream_socket_service<boost::asio::ip::tcp> > >&}; NextLayer = boost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp> >]’  
src/subscription.cpp:38:55:   required from here  
/home/alexey/projects/dinect/Beast/include/beast/core/impl/dynabuf_readstream.ipp:155:46: error: use of deleted function ‘boost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp> >::stream(const boost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp> >&)’  
     : next_layer_(std::forward<Args>(args)...)  
                                              ^  
In file included from src/subscription.hpp:10:0,  
                 from src/subscription.cpp:5:  
/home/alexey/projects/ms/boost_1_64_0/boost/asio/ssl/stream.hpp:74:7: note: ‘boost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp> >::stream(const boost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp> >&)’ is implicitly deleted because the default definition would be ill-formed:  
 class stream :  
       ^  
In file included from /home/alexey/projects/ms/boost_1_64_0/boost/asio/detail/call_stack.hpp:19:0,  
                 from /home/alexey/projects/ms/boost_1_64_0/boost/asio/impl/handler_alloc_hook.ipp:19,  
                 from /home/alexey/projects/ms/boost_1_64_0/boost/asio/handler_alloc_hook.hpp:80,  
                 from /home/alexey/projects/dinect/Beast/include/beast/core/handler_helpers.hpp:12,  
                 from /home/alexey/projects/dinect/Beast/include/beast/core/impl/handler_ptr.ipp:11,  
                 from /home/alexey/projects/dinect/Beast/include/beast/core/handler_ptr.hpp:202,  
                 from /home/alexey/projects/dinect/Beast/include/beast/websocket/detail/invokable.hpp:11,  
                 from /home/alexey/projects/dinect/Beast/include/beast/websocket/detail/stream_base.hpp:16,  
                 from /home/alexey/projects/dinect/Beast/include/beast/websocket/stream.hpp:13,  
                 from /home/alexey/projects/dinect/Beast/include/beast/websocket.hpp:16,  
                 from src/subscription.hpp:8,  
                 from src/subscription.cpp:5:  
/home/alexey/projects/ms/boost_1_64_0/boost/asio/detail/noncopyable.hpp:32:3: error: ‘boost::asio::detail::noncopyable::noncopyable(const boost::asio::detail::noncopyable&)’ is private  
   noncopyable(const noncopyable&);  
   ^  
In file included from src/subscription.hpp:10:0,  
                 from src/subscription.cpp:5:  
/home/alexey/projects/ms/boost_1_64_0/boost/asio/ssl/stream.hpp:74:7: error: within this context  
 class stream :  
       ^  
/home/alexey/projects/ms/boost_1_64_0/boost/asio/ssl/stream.hpp:74:7: error: use of deleted function ‘boost::asio::basic_stream_socket<boost::asio::ip::tcp>::basic_stream_socket(const boost::asio::basic_stream_socket<boost::asio::ip::tcp>&)’  
In file included from /home/alexey/projects/ms/boost_1_64_0/boost/asio.hpp:31:0,  
                 from /home/alexey/projects/dinect/Beast/include/beast/websocket/stream.hpp:19,  
                 from /home/alexey/projects/dinect/Beast/include/beast/websocket.hpp:16,  
                 from src/subscription.hpp:8,  
                 from src/subscription.cpp:5:  
/home/alexey/projects/ms/boost_1_64_0/boost/asio/basic_stream_socket.hpp:46:7: note: ‘boost::asio::basic_stream_socket<boost::asio::ip::tcp>::basic_stream_socket(const boost::asio::basic_stream_socket<boost::asio::ip::tcp>&)’ is implicitly declared as deleted because ‘boost::asio::basic_stream_socket<boost::asio::ip::tcp>’ declares a move constructor or move assignment operator  
 class basic_stream_socket  
       ^  
In file included from src/subscription.hpp:10:0,  
                 from src/subscription.cpp:5:  
/home/alexey/projects/ms/boost_1_64_0/boost/asio/ssl/stream.hpp:74:7: error: use of deleted function ‘boost::asio::ssl::detail::stream_core::stream_core(const boost::asio::ssl::detail::stream_core&)’  
 class stream :  
       ^  
In file included from /home/alexey/projects/ms/boost_1_64_0/boost/asio/ssl/detail/io.hpp:22:0,  
                 from /home/alexey/projects/ms/boost_1_64_0/boost/asio/ssl/stream.hpp:31,  
                 from src/subscription.hpp:10,  
                 from src/subscription.cpp:5:  
/home/alexey/projects/ms/boost_1_64_0/boost/asio/ssl/detail/stream_core.hpp:39:8: note: ‘boost::asio::ssl::detail::stream_core::stream_core(const boost::asio::ssl::detail::stream_core&)’ is implicitly deleted because the default definition would be ill-formed:  
 struct stream_core  
        ^  
In file included from /home/alexey/projects/ms/boost_1_64_0/boost/asio/ssl/detail/buffered_handshake_op.hpp:21:0,  
                 from /home/alexey/projects/ms/boost_1_64_0/boost/asio/ssl/stream.hpp:29,  
                 from src/subscription.hpp:10,  
                 from src/subscription.cpp:5:  
/home/alexey/projects/ms/boost_1_64_0/boost/asio/ssl/detail/engine.hpp:116:3: error: ‘boost::asio::ssl::detail::engine::engine(const boost::asio::ssl::detail::engine&)’ is private  
   engine(const engine&);  
   ^  
In file included from /home/alexey/projects/ms/boost_1_64_0/boost/asio/ssl/detail/io.hpp:22:0,  
                 from /home/alexey/projects/ms/boost_1_64_0/boost/asio/ssl/stream.hpp:31,  
                 from src/subscription.hpp:10,  
                 from src/subscription.cpp:5:  
/home/alexey/projects/ms/boost_1_64_0/boost/asio/ssl/detail/stream_core.hpp:39:8: error: within this context  
 struct stream_core  
        ^  
/home/alexey/projects/ms/boost_1_64_0/boost/asio/ssl/detail/stream_core.hpp:39:8: error: use of deleted function ‘boost::asio::basic_deadline_timer<boost::posix_time::ptime>::basic_deadline_timer(const boost::asio::basic_deadline_timer<boost::posix_time::ptime>&)’  
In file included from /home/alexey/projects/ms/boost_1_64_0/boost/asio.hpp:22:0,  
                 from /home/alexey/projects/dinect/Beast/include/beast/websocket/stream.hpp:19,  
                 from /home/alexey/projects/dinect/Beast/include/beast/websocket.hpp:16,  
                 from src/subscription.hpp:8,  
                 from src/subscription.cpp:5:  
/home/alexey/projects/ms/boost_1_64_0/boost/asio/basic_deadline_timer.hpp:126:7: note: ‘boost::asio::basic_deadline_timer<boost::posix_time::ptime>::basic_deadline_timer(const boost::asio::basic_deadline_timer<boost::posix_time::ptime>&)’ is implicitly deleted because the default definition would be ill-formed:  
 class basic_deadline_timer  
       ^  
In file included from /home/alexey/projects/ms/boost_1_64_0/boost/asio/basic_socket.hpp:20:0,  
                 from /home/alexey/projects/ms/boost_1_64_0/boost/asio/basic_datagram_socket.hpp:20,  
                 from /home/alexey/projects/ms/boost_1_64_0/boost/asio.hpp:21,  
                 from /home/alexey/projects/dinect/Beast/include/beast/websocket/stream.hpp:19,  
                 from /home/alexey/projects/dinect/Beast/include/beast/websocket.hpp:16,  
                 from src/subscription.hpp:8,  
                 from src/subscription.cpp:5:  
/home/alexey/projects/ms/boost_1_64_0/boost/asio/basic_io_object.hpp:169:3: error: ‘boost::asio::basic_io_object<IoObjectService, Movable>::basic_io_object(const boost::asio::basic_io_object<IoObjectService, Movable>&) [with IoObjectService = boost::asio::deadline_timer_service<boost::posix_time::ptime, boost::asio::time_traits<boost::posix_time::ptime> >; bool Movable = false]’ is private  
   basic_io_object(const basic_io_object&);  
   ^  
In file included from /home/alexey/projects/ms/boost_1_64_0/boost/asio.hpp:22:0,  
                 from /home/alexey/projects/dinect/Beast/include/beast/websocket/stream.hpp:19,  
                 from /home/alexey/projects/dinect/Beast/include/beast/websocket.hpp:16,  
                 from src/subscription.hpp:8,  
                 from src/subscription.cpp:5:  
/home/alexey/projects/ms/boost_1_64_0/boost/asio/basic_deadline_timer.hpp:126:7: error: within this context  
 class basic_deadline_timer  
       ^  
In file included from /home/alexey/projects/ms/boost_1_64_0/boost/asio/ssl/detail/io.hpp:22:0,  
                 from /home/alexey/projects/ms/boost_1_64_0/boost/asio/ssl/stream.hpp:31,  
                 from src/subscription.hpp:10,  
                 from src/subscription.cpp:5:  
/home/alexey/projects/ms/boost_1_64_0/boost/asio/ssl/detail/stream_core.hpp:39:8: error: use of deleted function ‘boost::asio::basic_deadline_timer<boost::posix_time::ptime>::basic_deadline_timer(const boost::asio::basic_deadline_timer<boost::posix_time::ptime>&)’  
 struct stream_core  
        ^`

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2017-05-02 22:38:23 UTC  
> Url: https://github.com/boostorg/beast/pull/333#issuecomment-298781034  

Thank you for your interest in Beast, and for submitting a pull request!  
  
However, I'm not so sure that we should change the representation of the next layer to `Stream&`. Can you please show me your code which is having a problem with non-copyable `boost::asio::ssl_stream`? Maybe something isn't being declared right.

---

## Comment 2

> Username: Aleksei-Badyaev  
> Created_at: 2017-05-03 07:12:17 UTC  
> Url: https://github.com/boostorg/beast/pull/333#issuecomment-298838088  

Thank you for your question! Yeas, I can. Below is code snippet.  
Line of code `websocketPtr_.reset( new WebSocket_t( *socketPtr_ ) );` causes a compilation error.  
```typedef boost::asio::ip::tcp::socket Socket_t;  
typedef boost::asio::ssl::stream< Socket_t > SSL_Socket_t;  
typedef beast::websocket::stream< SSL_Socket_t > WebSocket_t;  
  
Subscription::Subscription( const std::string & host ,  
                            const std::string & service ,  
                            Callback_t callback ,  
                            const const_buffer & ca )  
                : ioservicePtr_( new boost::asio::io_service ),  
                  callback_( callback )  
{  
    using boost::asio::ip::tcp;  
    ssl::context context( ssl::context::sslv23_client );  
    context.set_verify_mode( ssl::verify_peer );  
    context.set_default_verify_paths();  
    if( boost::asio::buffer_size( ca ) != 0 )  
        context.add_certificate_authority( ca );  
    socketPtr_.reset( new SSL_Socket_t( *ioservicePtr_ , context ) );  
    tcp::resolver r( *ioservicePtr_ );  
    tcp::resolver::query q( host , service );  
    boost::asio::connect( socketPtr_->lowest_layer() , r.resolve( q ) );  
    socketPtr_->lowest_layer().set_option( tcp::no_delay( true ) );  
    socketPtr_->set_verify_callback( ssl::rfc2818_verification( host ) );  
    socketPtr_->handshake( ssl::stream_base::client );  
    websocketPtr_.reset( new WebSocket_t( *socketPtr_ ) );  
    websocketPtr_->handshake( host , "/" );  
    websocketPtr_->async_read(  
                    opcode_ ,  
                    buffer_ ,  
                    boost::bind( &Subscription::read_handler ,  
                                 boost::ref( *this ) , _1 ) );  
} // Subscription::Subscription```

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2017-05-03 13:32:58 UTC  
> Updated_at: 2017-05-03 13:33:59 UTC  
> Url: https://github.com/boostorg/beast/pull/333#issuecomment-298911690  

The problem is here:  
```  
websocketPtr_.reset( new WebSocket_t( *socketPtr_ ) );  
```  
  
`boost::asio::ssl_stream` is not **MoveConstructible**. So the compiler is trying to invoke the copy constructor. I'm not sure why you are using so many `std::unique_ptr` in your implementation, rather than simply making those objects data members of your class. But here are some declarations that should work for you:  
  
```  
using resolver = boost::asio::ip::tcp::resolver;  
io_service ios;  
resolver r{ios};  
ssl::context ctx{ssl::context::sslv23};  
beast::websocket::stream<boost::asio::ssl::stream<boost::asio::ip::tcp::socket>> ws{ios, ctx};  
boost::asio::connect(ws.next_layer(), r.resolve(resolver::query{"echo.websocket.org", "https"}));  
```  
  
Hope this helps!  
  
See also:  
https://github.com/chriskohlhoff/asio/issues/124

---

## Comment 4

> Username: Aleksei-Badyaev  
> Created_at: 2017-05-04 09:30:10 UTC  
> Url: https://github.com/boostorg/beast/pull/333#issuecomment-299137449  

Thank you! It help!  
But, my mistake was in that typedef:  
`typedef beast::websocket::stream< SSL_Socket_t > WebSocket_t;`  
After I fix:  
`typedef beast::websocket::stream< SSL_Socket_t& > WebSocket_t;`  
compile error disappear.

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2017-05-04 11:37:28 UTC  
> Url: https://github.com/boostorg/beast/pull/333#issuecomment-299161059  

That also works!

---
