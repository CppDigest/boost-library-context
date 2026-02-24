# #2200 - Boost Beast http occupy 100% cpu while sending big file [Closed]

> Username: ginoblue  
> Created at: 2021-03-18 08:18:58 UTC  
> Updated at: 2024-06-06 05:34:22 UTC  
> Closed at: 2024-01-05 10:08:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2200  

### Steps necessary to reproduce the problem  
i write a http server which wrapping the beast-http, but when i try to send a file,  
it run normally, but too slowly and cpu's usage is too high: cpu 100%, net 30-40mb/s, using 'top' and 'sar -n DEV |grep lo'   
Am i doing somthing wrong while using this framwork?   
  
the code include 3 parts, one is the main.cpp , two is the http-lib , three is the server-session-hander  
  
in server, i read the request and change the string_body to file_body,   
i am just simply to using the async_send() [ impl by http::async_send ] in client.  
  
code:  
main.cpp :  
BOOST_AUTO_TEST_CASE( file_sender ) {  // Simple receive/send  
    spdlog::set_level( spdlog::level::debug );  
    HttpNet net;  
    net.run();  
  
    std::shared_ptr<ServerContext> con( new ServerContext );  
    HttpServerHandler<ServerContext>    handler( con );  
    con->_name = "server";  
  
    std::shared_ptr<ClientContext> con2( new ClientContext );  
    HttpClientHandler<ClientContext> handler2( con2 );  
    con2->_name = "client";  
      
    net.listen_for("0.0.0.0", 9090, handler);  
    auto client = net.connect_to("0.0.0.0", 9090, handler2);  
  
    error_code_type ec; //wrapping the boost err code  
    path_type path("./http_send"); //a file more then 1GB  
    http::request<http::file_body> req;  
    req.method(http::verb::post);  
    req.target("/file");  
    req.version(11);  
    req.set(http::field::host, "0.0.0.0");  
    req.set(http::field::user_agent, "test");  
    req.set(http::field::content_type, "multipart/form-data");  
    req.set(http::field::content_disposition, fmt::format("filename={}", path.utf8_generic_string()));  
    req.set(http::field::transfer_encoding, "chunked");  
    req.body().open(path.c_str(), beast::file_mode::scan, ec);  
    client->async_send(std::move(req));   
    sleep(100);  
  
    std::cout << "///////////////////////////////////////////////////////" << std::endl;  
}  
  
my http lib wrap from beast :  
  
struct Session<HttpServer>::Impl: BasicSessionImpl<HttpServer> {  
    // start listen from socket  
    template <class Handler> void start( Handler &&h, session_ptr s_ptr ) {  
        _request_deadline.expires_after(std::chrono::seconds(60));  
        _parser.emplace(); //clear  
  
        _request_deadline.async_wait([this](const boost_error_code_type &ec){  
            if (!ec){  
                close(); //close will raise handle_read  
            }  
        });  
          
        _buffer.clear();  
          
        http::async_read_header(_socket, _buffer, *_parser,  
            [this, s_ptr = std::move( s_ptr ), h = std::forward<Handler>( h )](  
                const boost_error_code_type &ec, std::size_t bytes_transferred ) mutable {  
                handle_read_header<Handler>( ec, bytes_transferred, std::move( h ), std::move( s_ptr ) );  
            });  
    }  
  
    template <class Handler>  
    void handle_read_header( const boost_error_code_type &e, std::size_t bytes_transferred, Handler &&h,  
                      session_ptr s_ptr ) {  
  
        http_context_type ctx;  
        if ( e ) {  
            // must be disconnected.  
            if ( e != boost::asio::error::would_block ) {  
                base_type::_alive = false;  
     
                h->handle_receive_header( e, _parser->release(), s_ptr, ctx );  
                socket().close();  
            }  
            return;  
        }  
          
        auto ret = h->handle_receive_header( e, _parser->get(), s_ptr, ctx );  
        if ( !ret ){ // not 'continue' to read the body  
            start( std::forward<Handler>( h ), std::move( s_ptr ) );  
            return ;  
        }  
        if (ctx._body_type != http_context_type::string_body){  
            switch(ctx._body_type){  
                case http_context_type::file_body:{  
                    std::shared_ptr<http::request_parser<http::file_body>> parser  
                        (new http::request_parser<http::file_body>(std::move(*_parser)));  
                    parser->get().body() = std::move(*ctx._file_body);  
                    parser->body_limit( boost::none );  
                    http::async_read(_socket, _buffer, *parser,  
                        [this, s_ptr = std::move( s_ptr ), h = std::forward<Handler>( h ), p_ptr = parser](  
                            const boost_error_code_type &ec, std::size_t bytes_transferred ) mutable {  
                            handle_read<Handler>( ec, bytes_transferred, std::move( h ), std::move( s_ptr ), p_ptr);  
                        });  
                    return;  
                }  
            }  
        }  
          
        http::async_read(_socket, _buffer, *_parser,  
            [this, s_ptr = std::move( s_ptr ), h = std::forward<Handler>( h )](  
                const boost_error_code_type &ec, std::size_t bytes_transferred ) mutable {  
                handle_read<Handler>( ec, bytes_transferred, std::move( h ), std::move( s_ptr ), _parser );  
            });  
    }  
      
    template <class Handler, class Parser>  
    void handle_read( const boost_error_code_type &e, std::size_t bytes_transferred, Handler &&h,  
                      session_ptr s_ptr, Parser &parser) {  
  
        http_context_type ctx;  
        if ( e ) {  
            // must be disconnected.  
            if ( e != boost::asio::error::would_block ) {  
                base_type::_alive = false;  
     
                h->handle_receive( e, parser->release(), s_ptr, ctx );  
                socket().close();  
            }  
            return;  
        }  
  
        _request_deadline.expires_at(std::chrono::steady_clock::time_point::max());  
          
        typename base_type::change_handler_gurad change_guard( this );  
        h->handle_receive(e, parser->release(), s_ptr, ctx);  
        if ( ctx._changed_handler ) {  
            spdlog::debug( "handler changed outside" );  
        } else {  
            start( std::forward<Handler>( h ), std::move( s_ptr ) );  
        }  
    }  
      
    //template<bool isRequest, class Body, class Fields>  
    void handle_write( const boost_error_code_type &e, std::size_t bytes_transferred, session_ptr s_ptr ) {  
        if ( e ) {  
            // must be disconeted  
            if ( e != boost::asio::error::would_block ) {  
                // socket().cancel(); //winxp not support  
                base_type::_alive = false;  
                socket().close();  
                return;  
            }  
        }  
          
    }  
    // send the http request  
    template<bool isRequest, class Body, class Fields>  
    void async_send(http::message<isRequest, Body, Fields>&& msg){  
        auto m_ptr = std::make_shared<  
                http::message<isRequest, Body, Fields>>(std::move(msg));  
                  
        http::async_write(_socket, *m_ptr, [  
                this, m_ptr, s_ptr = base_type::this_session_ptr()  
            ]( const boost_error_code_type &ec, std::size_t bytes_transferred ) mutable {  
                handle_write( ec, bytes_transferred, std::move( s_ptr ) );  
            } );  
    }  
  
    boost::optional<http::request_parser<http::string_body>> _parser;  
   
    socket_type                               _socket;  
    Scheduler                                 _sche;  
    Scheduler::timer_ptr                      _write_timer;  
    boost::asio::steady_timer _request_deadline;  
    beast::flat_buffer _buffer;  
  
};  
  
the server hander:  
struct ServerContext {  
        void handle_accept( const boost::system::error_code &      e,  
                            std::shared_ptr<Session<HttpServer>> new_session ) {  
            std::cout << "new_session, err: "  
                      << " " << e << std::endl;  
            std::cout << "new_session, local endpoint: " << new_session->socket().local_endpoint()  
                      << std::endl;  
            std::cout << "new_session, remove endpoint: " << new_session->socket().remote_endpoint()  
                      << std::endl;  
        }  
        template <class Protocol, class Body, class Fields>  
        bool handle_receive_header( const error_code_type &e, const http::request<Body, Fields>& req,  
                             std::shared_ptr<Session<Protocol>> session, http_context_type &ctx ) {  
  
            if (!e){  
                std::cout<<req<<"\n";  
            }else {  
                std::cout<<e<< "\n";  
                return true;  
            }  
            error_code_type ec;  
            if (req.target() == "/file"){  
                if(req.method() == http::verb::post){  
                    //path_type path(req.)  
                    ctx._body_type = http_context_type::file_body; // i change the body here  
                    ctx._file_body.emplace();  
                    ctx._file_body->open( "./tmpxxx", beast::file_mode::write, ec );  
                }  
                  
            }  
            return true;  
        }  
        template <class Body, class Fields>  
        bool handle_receive( const error_code_type &e, http::message<true, Body, Fields>&& msg,  
                             std::shared_ptr<Session<HttpServer>> session, http_context_type &ctx ) {  
  
            if (!e){  
                std::cout << msg.base() << "\n";  
            }else {  
                std::cout<<e<< "\n";  
                return true;  
            }  
            if (req.target() == "/file"){  
                if(req.method() == http::verb::post){  
                    //path_type path(req.)  
                }  
                  
            }  
            return true;  
        }  
### Version of Beast 1.75  
  
You can find the version number in the file `<boost/beast/version.hpp>`,  
or by using the command "git log -1" in the local Beast repository  
directory.  
  
### All relevant compiler information  
  
gcc9, centos7.5, local send to local

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-03-18 09:34:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2200#issuecomment-801772737  

It's difficult to comment without running a copy locally.  
Do you have this in a github repo that I can fork?  
(Ideally with a functioning CMakeLists.txt)

---

## Comment 2

> Username: ginoblue  
> Created at: 2021-03-18 10:37:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2200#issuecomment-801815299  

> It's difficult to comment without running a copy locally.  
> Do you have this in a github repo that I can fork?  
> (Ideally with a functioning CMakeLists.txt)

---

## Comment 3

> Username: ginoblue  
> Created at: 2021-03-18 10:44:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2200#issuecomment-801819182  

> It's difficult to comment without running a copy locally.  
> Do you have this in a github repo that I can fork?  
> (Ideally with a functioning CMakeLists.txt)  
  
figure out!!  
May be a bug ??  
http server read only 512 bytes per read_op, in file beast/core/read_size.hpp:53, func read_size(...) , buffer.capacity() - size always less than 512, but actually, my buffer actually beast::flat_buffer has set to 8MB size when contruct it.  
i change the 512 to 512000, and the cpu usage has down!!  
pls help !!

---

## Comment 4

> Username: ginoblue  
> Created at: 2021-03-19 02:35:51 UTC  
> Updated at: 2021-03-19 09:28:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2200#issuecomment-802493681  

finally, i do this in read buffer in server side:  
_buffer.reserve(8 * 1024 * 1024); // i think the buffer should auto use its max size, and not need to reserve it..  
  
but in the client side which is send a file 4k/per time, i do this at basic_file_body.hpp:  
change char buf_[4096] to buf_[4096 * 1024];  
fortunately， the buf is alloc in heap, and beast is header only lib  
unfortunately, the class basic_file_body has nothing interface for me to change it, why beast's author not using the beast/xxx_buffer in file_body's wirter/reader, and it's not a good idea to change the source code.  
But , it fix!! Too small read/write buf size make the asio run so frequently, which make cpu so busy just only handle sending a file...

---

## Comment 5

> Username: vinniefalco  
> Created at: 2021-03-19 04:55:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2200#issuecomment-802556508  

Which version of Beast?

---

## Comment 6

> Username: ginoblue  
> Created at: 2021-03-19 05:36:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2200#issuecomment-802571101  

> Which version of Beast?  
  
boost1.75

---

## Comment 7

> Username: madmongo1  
> Created at: 2021-03-19 08:39:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2200#issuecomment-802654865  

This looks interesting

---

## Comment 8

> Username: jade2k11598  
> Created at: 2021-03-19 17:11:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2200#issuecomment-802984561  

Any chance that the fix to this could also include the [option to setting the basic_file_body buffer size requested here](https://github.com/boostorg/beast/issues/1094#issuecomment-388255645)?  Would be very helpful in optimizing the sending of large files.  As @ginoblue notes, it's really not desirable to have to modify the boost beast code to get a larger buffer size.

---

## Comment 9

> Username: ginoblue  
> Created at: 2021-03-22 06:35:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2200#issuecomment-803801589  

> Any chance that the fix to this could also include the [option to setting the basic_file_body buffer size requested here](https://github.com/boostorg/beast/issues/1094#issuecomment-388255645)? Would be very helpful in optimizing the sending of large files. As @ginoblue notes, it's really not desirable to have to modify the boost beast code to get a larger buffer size.  
  
Thank you for advice, the way i use only to find out the reason, but not the real production environment.  
The essence of this problem is the implementation of asynchronous transmission, not the size of buffer, 4K buffer In fact, there is no problem, because the hard disk and the file system have their own caches. Sequential reading should be very fast, and the processing logic of the serializer is no problem. The problem is that every small piece of processing goes through a whole ASIO. In fact, it can be handled in a ios.run thread with a blocking read/send mode ,it is easy to implement at present. Of course, the situation discussed here is hard disk reading Normally, if the hard disk slows down, blocking read may occupy the resources of  threads, but in fact, the user logic can control the number of related threads, which will not lead to the collapse of the whole system. Finally, in fact, both methods are provided, allowing users to decide which one to use.

---

## Comment 10

> Username: upsilona  
> Created at: 2021-12-21 17:33:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2200#issuecomment-998967287  

Hello everyone,  
If increasing the size of the buffer is not possible / desirable, how should we improve the performance (bandwith) of basic_file_body ?

---

## Comment 11

> Username: madmongo1  
> Created at: 2021-12-21 17:52:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2200#issuecomment-998979150  

Good question, where have you measured the bottleneck to be?

---

## Comment 12

> Username: vinniefalco  
> Created at: 2021-12-22 00:02:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2200#issuecomment-999175652  

Can't we just increase the default buffer size?

---

## Comment 13

> Username: madmongo1  
> Created at: 2021-12-22 10:41:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2200#issuecomment-999471973  

We can, but at the moment I have no evidence that this will solve the OP's CPU use issue.

---

## Comment 14

> Username: vinniefalco  
> Created at: 2021-12-22 15:20:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2200#issuecomment-999653811  

What happens when the OP runs the http-server-async and tries to serve the same file?

---

## Comment 15

> Username: wangpy1204  
> Created at: 2023-05-08 12:23:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2200#issuecomment-1538272941  

Has the problem been solved?

---

## Comment 16

> Username: ashtum  
> Created at: 2024-01-05 10:08:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2200#issuecomment-1878422892  

Addressed in https://github.com/boostorg/beast/commit/4ff4c79d5b3df7a56e692f6112de48bdc33a549f, by adding `BOOST_BEAST_FILE_BUFFER_SIZE` preprocessor definition that allows setting buffer size for the file_body.  
There is an open issue for making the `buffer_size` dynamically reconfigurable: https://github.com/boostorg/beast/issues/1094.
