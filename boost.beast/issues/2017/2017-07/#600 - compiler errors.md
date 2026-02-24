# #600 - compiler errors [Closed]

> Username: octopus-prime  
> Created at: 2017-07-05 10:53:28 UTC  
> Updated at: 2017-07-06 12:51:53 UTC  
> Closed at: 2017-07-06 12:51:53 UTC  
> Url: https://github.com/boostorg/beast/issues/600  

Beast/72  
OSX  
Apple LLVM version 7.3.0  
  
```  
/usr/local/include/boost/optional/optional.hpp:350:36: error: call to implicitly-deleted copy constructor of 'value_type' (aka  
      'beast::http::message<false, file_body, beast::http::basic_fields<std::__1::allocator<char> > >')  
       ::new (m_storage.address()) value_type(val) ;  
                                   ^          ~~~  
/usr/local/include/boost/optional/optional.hpp:131:9: note: in instantiation of member function  
      'boost::optional_detail::optional_base<beast::http::message<false, file_body, beast::http::basic_fields<std::__1::allocator<char> > > >::construct'  
      requested here  
        construct(val);  
        ^  
/usr/local/include/boost/optional/optional.hpp:800:38: note: in instantiation of member function  
      'boost::optional_detail::optional_base<beast::http::message<false, file_body, beast::http::basic_fields<std::__1::allocator<char> > >  
      >::optional_base' requested here  
    optional ( argument_type val ) : base(val) {}  
                                     ^  
/Users/mgresens/workspace_cevelop/Beast/example/http-server-threaded/http_server_threaded.cpp:117:16: note: in instantiation of member function  
      'boost::optional<beast::http::message<false, file_body, beast::http::basic_fields<std::__1::allocator<char> > > >::optional' requested here  
        return res;  
               ^  
/Users/mgresens/workspace_cevelop/Beast/include/beast/http/message.hpp:413:5: note: explicitly defaulted function was implicitly deleted here  
    message(message const&) = default;  
    ^  
/Users/mgresens/workspace_cevelop/Beast/include/beast/http/message.hpp:404:31: note: copy constructor of 'message<false, file_body,  
      beast::http::basic_fields<std::__1::allocator<char> > >' is implicitly deleted because field 'body' has a deleted copy constructor  
    typename Body::value_type body;  
                              ^  
/Users/mgresens/workspace_cevelop/Beast/example/http-server-threaded/../common/file_body.hpp:90:5: note: copy constructor is implicitly deleted because  
      'value_type' has a user-declared move constructor  
    value_type(value_type&& other)  
```  
  
```  
/usr/local/include/boost/optional/optional.hpp:350:36: error: call to implicitly-deleted copy constructor of 'value_type' (aka  
      'beast::http::message<false, file_body, beast::http::basic_fields<std::__1::allocator<char> > >')  
       ::new (m_storage.address()) value_type(val) ;  
                                   ^          ~~~  
/usr/local/include/boost/optional/optional.hpp:131:9: note: in instantiation of member function  
      'boost::optional_detail::optional_base<beast::http::message<false, file_body, beast::http::basic_fields<std::__1::allocator<char> > > >::construct'  
      requested here  
        construct(val);  
        ^  
/usr/local/include/boost/optional/optional.hpp:800:38: note: in instantiation of member function  
      'boost::optional_detail::optional_base<beast::http::message<false, file_body, beast::http::basic_fields<std::__1::allocator<char> > >  
      >::optional_base' requested here  
    optional ( argument_type val ) : base(val) {}  
                                     ^  
/Users/mgresens/workspace_cevelop/Beast/example/server-framework/file_service.hpp:250:16: note: in instantiation of member function  
      'boost::optional<beast::http::message<false, file_body, beast::http::basic_fields<std::__1::allocator<char> > > >::optional' requested here  
        return res;  
               ^  
/Users/mgresens/workspace_cevelop/Beast/example/server-framework/file_service.hpp:133:24: note: in instantiation of function template specialization  
      'framework::file_service::get<beast::http::basic_dynamic_body<beast::basic_multi_buffer<std::__1::allocator<char> > >,  
      beast::http::basic_fields<std::__1::allocator<char> > >' requested here  
            auto res = get(req, full_path, ec);  
                       ^  
/Users/mgresens/workspace_cevelop/Beast/example/server-framework/service_list.hpp:171:32: note: in instantiation of function template specialization  
      'framework::file_service::respond<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::stream_socket_service<boost::asio::ip::tcp> >,  
      beast::http::basic_dynamic_body<beast::basic_multi_buffer<std::__1::allocator<char> > >, beast::http::basic_fields<std::__1::allocator<char> >,  
      framework::sync_http_con_base<framework::sync_http_con<framework::ws_upgrade_service<framework::ws_sync_port>, framework::file_service>,  
      framework::ws_upgrade_service<framework::ws_sync_port>, framework::file_service>::send_lambda>' requested here  
        if(std::get<I>(list_)->respond(  
                               ^  
/Users/mgresens/workspace_cevelop/Beast/example/server-framework/service_list.hpp:181:16: note: (skipping 4 contexts in backtrace; use  
      -ftemplate-backtrace-limit=0 to see all)  
        return try_respond(  
               ^  
/Users/mgresens/workspace_cevelop/Beast/example/server-framework/http_sync_port.hpp:469:18: note: in instantiation of member function  
      'framework::sync_http_con_base<framework::sync_http_con<framework::ws_upgrade_service<framework::ws_sync_port>, framework::file_service>,  
      framework::ws_upgrade_service<framework::ws_sync_port>, framework::file_service>::run' requested here  
            ep)->run();  
                 ^  
/Users/mgresens/workspace_cevelop/Beast/example/server-framework/server.hpp:238:22: note: in instantiation of member function  
      'framework::http_sync_port<framework::ws_upgrade_service<framework::ws_sync_port>, framework::file_service>::on_accept' requested here  
            handler_.on_accept(std::move(sock_), ep_);  
                     ^  
/Users/mgresens/workspace_cevelop/Beast/example/server-framework/server.hpp:220:30: note: in instantiation of member function  
      'framework::port<framework::http_sync_port<framework::ws_upgrade_service<framework::ws_sync_port>, framework::file_service> >::on_accept' requested  
      here  
            std::bind(&port::on_accept, this->shared_from_this(),  
                             ^  
/Users/mgresens/workspace_cevelop/Beast/example/server-framework/server.hpp:258:9: note: in instantiation of member function  
      'framework::port<framework::http_sync_port<framework::ws_upgrade_service<framework::ws_sync_port>, framework::file_service> >::open' requested here  
    sp->open(ep, ec);  
        ^  
/Users/mgresens/workspace_cevelop/Beast/example/server-framework/main.cpp:165:28: note: in instantiation of function template specialization  
      'framework::server::make_port<framework::http_sync_port<framework::ws_upgrade_service<framework::ws_sync_port>, framework::file_service>,  
      framework::server &, std::__1::basic_ostream<char> &>' requested here  
        auto sp = instance.make_port<http_sync_port<  
                           ^  
/Users/mgresens/workspace_cevelop/Beast/include/beast/http/message.hpp:413:5: note: explicitly defaulted function was implicitly deleted here  
    message(message const&) = default;  
    ^  
/Users/mgresens/workspace_cevelop/Beast/include/beast/http/message.hpp:404:31: note: copy constructor of 'message<false, file_body,  
      beast::http::basic_fields<std::__1::allocator<char> > >' is implicitly deleted because field 'body' has a deleted copy constructor  
    typename Body::value_type body;  
                              ^  
/Users/mgresens/workspace_cevelop/Beast/example/server-framework/../common/file_body.hpp:90:5: note: copy constructor is implicitly deleted because  
      'value_type' has a user-declared move constructor  
    value_type(value_type&& other)  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-07-05 12:39:12 UTC  
> Url: https://github.com/boostorg/beast/issues/600#issuecomment-313090735  

Uhh.... that's bizarre. Unfortunately Travis doesn't build Apple/LLVM sorry about this.  
  
It looks like `auto res = get(req, full_path, ec);` is trying to invoke the copy constructor?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-07-05 12:41:53 UTC  
> Url: https://github.com/boostorg/beast/issues/600#issuecomment-313091302  

It looks like there's a way to build on OS X but I have no idea how to get it working  
https://docs.travis-ci.com/user/osx-ci-environment/

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-07-05 13:54:32 UTC  
> Updated at: 2017-07-05 13:54:37 UTC  
> Url: https://github.com/boostorg/beast/issues/600#issuecomment-313109844  

Try this? https://github.com/vinniefalco/Beast/commit/3cbf273484b88cedff884354ce8a2e3f4b1a0ea7

---

## Comment 4

> Username: octopus-prime  
> Created at: 2017-07-05 15:24:16 UTC  
> Url: https://github.com/boostorg/beast/issues/600#issuecomment-313136758  

Tried 5130cd9  
```  
Scanning dependencies of target websocket-server-async  
[ 96%] Building CXX object example/websocket-server-async/CMakeFiles/websocket-server-async.dir/websocket_server_async.cpp.o  
In file included from /Users/mgresens/workspace_cevelop/Beast/example/http-server-threaded/http_server_threaded.cpp:10:  
In file included from /Users/mgresens/workspace_cevelop/Beast/include/beast/core.hpp:19:  
In file included from /Users/mgresens/workspace_cevelop/Beast/include/beast/core/consuming_buffers.hpp:14:  
In file included from /usr/local/include/boost/optional.hpp:15:  
/usr/local/include/boost/optional/optional.hpp:350:36: error: call to implicitly-deleted copy constructor of 'value_type' (aka  
      'beast::http::message<false, beast::http::file_body_stdc, beast::http::basic_fields<std::__1::allocator<char> > >')  
       ::new (m_storage.address()) value_type(val) ;  
                                   ^          ~~~  
/usr/local/include/boost/optional/optional.hpp:131:9: note: in instantiation of member function  
      'boost::optional_detail::optional_base<beast::http::message<false, beast::http::file_body_stdc, beast::http::basic_fields<std::__1::allocator<char>  
      > > >::construct' requested here  
        construct(val);  
        ^  
/usr/local/include/boost/optional/optional.hpp:800:38: note: in instantiation of member function  
      'boost::optional_detail::optional_base<beast::http::message<false, beast::http::file_body_stdc, beast::http::basic_fields<std::__1::allocator<char>  
      > > >::optional_base' requested here  
    optional ( argument_type val ) : base(val) {}  
                                     ^  
/Users/mgresens/workspace_cevelop/Beast/example/http-server-threaded/http_server_threaded.cpp:113:16: note: in instantiation of member function  
      'boost::optional<beast::http::message<false, beast::http::file_body_stdc, beast::http::basic_fields<std::__1::allocator<char> > > >::optional'  
      requested here  
        return res;  
               ^  
/Users/mgresens/workspace_cevelop/Beast/include/beast/http/message.hpp:442:5: note: explicitly defaulted function was implicitly deleted here  
    message(message const&) = default;  
    ^  
/Users/mgresens/workspace_cevelop/Beast/include/beast/http/message.hpp:433:31: note: copy constructor of 'message<false, beast::http::file_body_stdc,  
      beast::http::basic_fields<std::__1::allocator<char> > >' is implicitly deleted because field 'body' has a deleted copy constructor  
    typename Body::value_type body;  
                              ^  
/Users/mgresens/workspace_cevelop/Beast/include/beast/http/file_body_stdc.hpp:89:5: note: copy constructor is implicitly deleted because 'value_type' has  
      a user-declared move constructor  
    value_type(value_type&& other)  
    ^  
1 error generated.  
make[2]: *** [example/http-server-threaded/CMakeFiles/http-server-threaded.dir/http_server_threaded.cpp.o] Error 1  
make[1]: *** [example/http-server-threaded/CMakeFiles/http-server-threaded.dir/all] Error 2  
make[1]: *** Waiting for unfinished jobs....  
[ 97%] Linking CXX executable websocket-client  
Running ld for x86_64 ...  
[ 97%] Built target websocket-client  
[ 97%] Linking CXX executable websocket-server-async  
Running ld for x86_64 ...  
In file included from /Users/mgresens/workspace_cevelop/Beast/example/server-framework/main.cpp:8:  
In file included from /Users/mgresens/workspace_cevelop/Beast/example/server-framework/server.hpp:13:  
In file included from /usr/local/include/boost/optional.hpp:15:  
/usr/local/include/boost/optional/optional.hpp:350:36: error: call to implicitly-deleted copy constructor of 'value_type' (aka  
      'beast::http::message<false, beast::http::file_body_stdc, beast::http::basic_fields<std::__1::allocator<char> > >')  
       ::new (m_storage.address()) value_type(val) ;  
                                   ^          ~~~  
/usr/local/include/boost/optional/optional.hpp:131:9: note: in instantiation of member function  
      'boost::optional_detail::optional_base<beast::http::message<false, beast::http::file_body_stdc, beast::http::basic_fields<std::__1::allocator<char>  
      > > >::construct' requested here  
        construct(val);  
        ^  
/usr/local/include/boost/optional/optional.hpp:800:38: note: in instantiation of member function  
      'boost::optional_detail::optional_base<beast::http::message<false, beast::http::file_body_stdc, beast::http::basic_fields<std::__1::allocator<char>  
      > > >::optional_base' requested here  
    optional ( argument_type val ) : base(val) {}  
                                     ^  
/Users/mgresens/workspace_cevelop/Beast/example/server-framework/file_service.hpp:250:16: note: in instantiation of member function  
      'boost::optional<beast::http::message<false, beast::http::file_body_stdc, beast::http::basic_fields<std::__1::allocator<char> > > >::optional'  
      requested here  
        return res;  
               ^  
/Users/mgresens/workspace_cevelop/Beast/example/server-framework/file_service.hpp:133:22: note: in instantiation of function template specialization  
      'framework::file_service::get<beast::http::basic_dynamic_body<beast::basic_multi_buffer<std::__1::allocator<char> > >,  
      beast::http::basic_fields<std::__1::allocator<char> > >' requested here  
            auto res{get(req, full_path, ec)};  
                     ^  
/Users/mgresens/workspace_cevelop/Beast/example/server-framework/service_list.hpp:171:32: note: in instantiation of function template specialization  
      'framework::file_service::respond<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::stream_socket_service<boost::asio::ip::tcp> >,  
      beast::http::basic_dynamic_body<beast::basic_multi_buffer<std::__1::allocator<char> > >, beast::http::basic_fields<std::__1::allocator<char> >,  
      framework::sync_http_con_base<framework::sync_http_con<framework::ws_upgrade_service<framework::ws_sync_port>, framework::file_service>,  
      framework::ws_upgrade_service<framework::ws_sync_port>, framework::file_service>::send_lambda>' requested here  
        if(std::get<I>(list_)->respond(  
                               ^  
/Users/mgresens/workspace_cevelop/Beast/example/server-framework/service_list.hpp:181:16: note: (skipping 4 contexts in backtrace; use  
      -ftemplate-backtrace-limit=0 to see all)  
        return try_respond(  
               ^  
/Users/mgresens/workspace_cevelop/Beast/example/server-framework/http_sync_port.hpp:470:18: note: in instantiation of member function  
      'framework::sync_http_con_base<framework::sync_http_con<framework::ws_upgrade_service<framework::ws_sync_port>, framework::file_service>,  
      framework::ws_upgrade_service<framework::ws_sync_port>, framework::file_service>::run' requested here  
            ep)->run();  
                 ^  
/Users/mgresens/workspace_cevelop/Beast/example/server-framework/server.hpp:238:22: note: in instantiation of member function  
      'framework::http_sync_port<framework::ws_upgrade_service<framework::ws_sync_port>, framework::file_service>::on_accept' requested here  
            handler_.on_accept(std::move(sock_), ep_);  
                     ^  
/Users/mgresens/workspace_cevelop/Beast/example/server-framework/server.hpp:220:30: note: in instantiation of member function  
      'framework::port<framework::http_sync_port<framework::ws_upgrade_service<framework::ws_sync_port>, framework::file_service> >::on_accept' requested  
      here  
            std::bind(&port::on_accept, this->shared_from_this(),  
                             ^  
/Users/mgresens/workspace_cevelop/Beast/example/server-framework/server.hpp:258:9: note: in instantiation of member function  
      'framework::port<framework::http_sync_port<framework::ws_upgrade_service<framework::ws_sync_port>, framework::file_service> >::open' requested here  
    sp->open(ep, ec);  
        ^  
/Users/mgresens/workspace_cevelop/Beast/example/server-framework/main.cpp:165:28: note: in instantiation of function template specialization  
      'framework::server::make_port<framework::http_sync_port<framework::ws_upgrade_service<framework::ws_sync_port>, framework::file_service>,  
      framework::server &, std::__1::basic_ostream<char> &>' requested here  
        auto sp = instance.make_port<http_sync_port<  
                           ^  
/Users/mgresens/workspace_cevelop/Beast/include/beast/http/message.hpp:442:5: note: explicitly defaulted function was implicitly deleted here  
    message(message const&) = default;  
    ^  
/Users/mgresens/workspace_cevelop/Beast/include/beast/http/message.hpp:433:31: note: copy constructor of 'message<false, beast::http::file_body_stdc,  
      beast::http::basic_fields<std::__1::allocator<char> > >' is implicitly deleted because field 'body' has a deleted copy constructor  
    typename Body::value_type body;  
                              ^  
/Users/mgresens/workspace_cevelop/Beast/include/beast/http/file_body_stdc.hpp:89:5: note: copy constructor is implicitly deleted because 'value_type' has  
      a user-declared move constructor  
    value_type(value_type&& other)  
    ^  
[ 97%] Built target websocket-server-async  
1 error generated.  
make[2]: *** [example/server-framework/CMakeFiles/server-framework.dir/main.cpp.o] Error 1  
make[1]: *** [example/server-framework/CMakeFiles/server-framework.dir/all] Error 2  
make: *** [all] Error 2  
```

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-07-05 15:40:09 UTC  
> Url: https://github.com/boostorg/beast/issues/600#issuecomment-313141565  

Try this? https://github.com/vinniefalco/Beast/commit/fe78d30a4b32141e23f77d90fe94e9e1e9e34666

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-07-05 16:58:50 UTC  
> Url: https://github.com/boostorg/beast/issues/600#issuecomment-313163657  

Is this the same error? https://travis-ci.org/vinniefalco/Beast/jobs/250421336

---

## Comment 7

> Username: octopus-prime  
> Created at: 2017-07-06 12:51:52 UTC  
> Url: https://github.com/boostorg/beast/issues/600#issuecomment-313387030  

Can't reproduce with v73 -> Close issue. See #599.
