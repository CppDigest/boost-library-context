# #2642 - beast.async_connect() compilation error [Closed]

> Username: chivas1000  
> Created at: 2023-02-20 03:18:23 UTC  
> Updated at: 2023-02-21 09:50:50 UTC  
> Closed at: 2023-02-21 09:50:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2642  

Hi everyone,  
I'm rewriting websocket.h with the objective of specifying the local endpoint with minimal modification,  
the original websocket.h have the same structure with this:  
https://www.boost.org/doc/libs/master/libs/beast/example/websocket/client/async/websocket_client_async.cpp  
  
basically I dropped the on_resolve() but move those functions to run(),  
and then directly use the ep = tcp::endpoint other than resolver in async_connect()  
no error in IDE but error in compilation  
  
Am I messed up with beast::get_lowest_layer(ws_).async_connect?  
  
Or is there any solution or reference for using beast to specify the local endpoint?  
  
much grateful if anyone could help  
  
Thanks.  
  
version: beast 1.70.0  
  
code:  
```  
    // Start the asynchronous operation  
    void  
    run(  
            char const *host,  
//            char const *port,  
            unsigned int *port,  
            __attribute__((unused)) char const *text) {  
        // Save these for later, pass in host and port to construct ep  
        host_ = host;  
        std::stringstream str_port_value;  
        str_port_value << port;  
        str_port_value >> port_;  
  
//dropped  
        // Look up the domain name  
//        resolver_.async_resolve(  
//                host,  
//                port,  
//                beast::bind_front_handler(  
//                        &session::on_resolve,  
//                        shared_from_this()));  
  
  
        auto ep = tcp::endpoint (boost::asio::ip::make_address_v4(host_.c_str()), port_);  
  
        beast::get_lowest_layer(ws_).expires_after(std::chrono::seconds(30));  
  
        // here comes the error  
        beast::get_lowest_layer(ws_).async_connect(  
                ep,  
                beast::bind_front_handler(  
                        &session::on_connect,  
                        shared_from_this()));  
  
    }  
  
  
//    for more information here are the on_connect  
//    on_connect(beast::error_code ec, tcp::resolver::results_type::endpoint_type ep) {  
        on_connect(beast::error_code ec, tcp::endpoint ep) {  
        if (ec)  
            return fail(ec, "connect");  
  
        // Turn off the timeout on the tcp_stream, because  
        // the websocket stream has its own timeout system.  
        beast::get_lowest_layer(ws_).expires_never();  
  
        // Set suggested timeout settings for the websocket  
        ws_.set_option(  
                websocket::stream_base::timeout::suggested(  
                        beast::role_type::client));  
  
        // Set a decorator to change the User-Agent of the handshake  
        ws_.set_option(websocket::stream_base::decorator(  
                [](websocket::request_type &req) {  
                    req.set(http::field::user_agent,  
                            std::string(BOOST_BEAST_VERSION_STRING) +  
                            " websocket-client-async");  
                }));  
  
        // update the host string. This will provide the value of the  
        // host HTTP header during the websocket handshake  
        // the guide references: https://tools.ietf.org/html/rfc7230#section-5.4  
        host_ += ':' + std::to_string(ep.port());  
  
  
        // Perform the websocket handshake  
        ws_.async_handshake(host_, "/",  
                            beast::bind_front_handler(  
                                    &session::on_handshake,  
                                    shared_from_this()));  
    }  
  
  
  
```  
  
  
error logs:  
====================[ Compile | code/include/WebSocket.h | Debug ]==============  
Compiling 'code/Examples/Monocular/client.cc' which includes requested 'code/include/WebSocket.h' ...  
/usr/bin/c++ -DCOMPILEDWITHC11 -DSPDLOG_COMPILED_LIB -I/usr/local/cuda/include -I/home/ubuntu/SwarmMap/code -I/home/ubuntu/SwarmMap/code/include -I/home/ubuntu/SwarmMap/code/Thirdparty/popl -I/usr/include/eigen3 -isystem/usr/local/include/opencv -Wall -Wextra -DUSE_NVTX -Wno-deprecated-declarations -ftemplate-backtrace-limit=1 -std=c++11 -g -fdiagnostics-color=always -fsyntax-only /home/ubuntu/SwarmMap/code/Examples/Monocular/client.cc  
/home/ubuntu/SwarmMap/code/Examples/Monocular/client.cc: In function ‘void RegisterRemote(ORB_SLAM2::System*, const string&, const unsigned int&)’:  
/home/ubuntu/SwarmMap/code/Examples/Monocular/client.cc:75:12: warning: comparison between signed and unsigned integer expressions [-Wsign-compare]  
     if (id == -1 || port == -1) {  
         ~~~^~~~~  
/home/ubuntu/SwarmMap/code/Examples/Monocular/client.cc:75:26: warning: comparison between signed and unsigned integer expressions [-Wsign-compare]  
     if (id == -1 || port == -1) {  
                     ~~~~~^~~~~  
In file included from /usr/local/include/boost/beast/core/bind_handler.hpp:14:0,  
                 from /usr/local/include/boost/beast/core/async_base.hpp:14,  
                 from /usr/local/include/boost/beast/core.hpp:15,  
                 from /home/ubuntu/SwarmMap/code/include/WebSocket.h:8,  
                 from /home/ubuntu/SwarmMap/code/include/ClientService.h:11,  
                 from /home/ubuntu/SwarmMap/code/include/Map.h:32,  
                 from /home/ubuntu/SwarmMap/code/include/MapPoint.h:25,  
                 from /home/ubuntu/SwarmMap/code/include/FrameDrawer.h:25,  
                 from /home/ubuntu/SwarmMap/code/include/Viewer.h:25,  
                 from /home/ubuntu/SwarmMap/code/include/Tracking.h:29,  
                 from /home/ubuntu/SwarmMap/code/include/System.h:30,  
                 from /home/ubuntu/SwarmMap/code/Examples/Monocular/client.cc:31:  
/usr/local/include/boost/beast/core/detail/bind_handler.hpp: In instantiation of ‘void boost::beast::detail::bind_front_wrapper<Handler, Args>::invoke(std::true_type, boost::mp11::index_sequence<I ...>, Ts&& ...) [with long unsigned int ...I = {0}; Ts = {boost::system::error_code&}; Handler = void (ORB_SLAM2::WS::Client::session::*)(boost::system::error_code, boost::asio::ip::basic_endpoint<boost::asio::ip::tcp>); Args = {std::shared_ptr<ORB_SLAM2::WS::Client::session>}; std::true_type = std::integral_constant<bool, true>; boost::mp11::index_sequence<I ...> = boost::mp11::integer_sequence<long unsigned int, 0>]’:  
/usr/local/include/boost/beast/core/detail/bind_handler.hpp:258:15:   [ skipping 20 instantiation contexts, use -ftemplate-backtrace-limit=0 to disable ]  
/usr/local/include/boost/beast/core/impl/basic_stream.hpp:812:26:   required from ‘typename boost::asio::async_result<typename std::decay<_U1>::type, void(boost::system::error_code)>::return_type boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(const endpoint_type&, ConnectHandler&&) [with ConnectHandler = boost::beast::detail::bind_front_wrapper<void (ORB_SLAM2::WS::Client::session::*)(boost::system::error_code, boost::asio::ip::basic_endpoint<boost::asio::ip::tcp>), std::shared_ptr<ORB_SLAM2::WS::Client::session> >; Protocol = boost::asio::ip::tcp; Executor = boost::asio::executor; RatePolicy = boost::beast::unlimited_rate_policy; typename boost::asio::async_result<typename std::decay<_U1>::type, void(boost::system::error_code)>::return_type = void; boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::endpoint_type = boost::asio::ip::basic_endpoint<boost::asio::ip::tcp>]’  
/home/ubuntu/SwarmMap/code/include/WebSocket.h:129:44:   required from here  
/usr/local/include/boost/beast/core/detail/bind_handler.hpp:235:24: error: no match for call to ‘(std::_Mem_fn<void (ORB_SLAM2::WS::Client::session::*)(boost::system::error_code, boost::asio::ip::basic_endpoint<boost::asio::ip::tcp>)>) (std::shared_ptr<ORB_SLAM2::WS::Client::session>, boost::system::error_code&)’  
         std::mem_fn(h_)(  
         ~~~~~~~~~~~~~~~^  
             detail::get<I>(std::move(args_))...,  
             ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
             std::forward<Ts>(ts)...);  
             ~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /usr/local/include/opencv2/core/utility.hpp:60:0,  
                 from /usr/local/include/opencv2/core.hpp:3281,  
                 from /usr/local/include/opencv2/core/core.hpp:48,  
                 from /home/ubuntu/SwarmMap/code/Examples/Monocular/client.cc:29:  
/usr/include/c++/7/functional:171:2: note: candidate: template<class ... _Args> decltype (std::__invoke(((const std::_Mem_fn_base<_MemFunPtr, __is_mem_fn>*)this)->std::_Mem_fn_base<_MemFunPtr, __is_mem_fn>::_M_pmf, (forward<_Args>)(std::_Mem_fn_base::operator()::__args)...)) std::_Mem_fn_base<_MemFunPtr, __is_mem_fn>::operator()(_Args&& ...) const [with _Args = {_Args ...}; _MemFunPtr = void (ORB_SLAM2::WS::Client::session::*)(boost::system::error_code, boost::asio::ip::basic_endpoint<boost::asio::ip::tcp>); bool __is_mem_fn = true]  
  operator()(_Args&&... __args) const  
  ^~~~~~~~  
/usr/include/c++/7/functional:171:2: note:   template argument deduction/substitution failed:  
/usr/include/c++/7/functional: In substitution of ‘template<class ... _Args> decltype (std::__invoke(((const std::_Mem_fn_base<void (ORB_SLAM2::WS::Client::session::*)(boost::system::error_code, boost::asio::ip::basic_endpoint<boost::asio::ip::tcp>), true>*)this)->std::_Mem_fn_base<void (ORB_SLAM2::WS::Client::session::*)(boost::system::error_code, boost::asio::ip::basic_endpoint<boost::asio::ip::tcp>), true>::_M_pmf, (forward<_Args>)(std::_Mem_fn_base::operator()::__args)...)) std::_Mem_fn_base<void (ORB_SLAM2::WS::Client::session::*)(boost::system::error_code, boost::asio::ip::basic_endpoint<boost::asio::ip::tcp>), true>::operator()<_Args ...>(_Args&& ...) const [with _Args = {std::shared_ptr<ORB_SLAM2::WS::Client::session>, boost::system::error_code&}]’:  
/usr/local/include/boost/beast/core/detail/bind_handler.hpp:235:24:   [ skipping 21 instantiation contexts, use -ftemplate-backtrace-limit=0 to disable ]  
/usr/local/include/boost/beast/core/impl/basic_stream.hpp:812:26:   required from ‘typename boost::asio::async_result<typename std::decay<_U1>::type, void(boost::system::error_code)>::return_type boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(const endpoint_type&, ConnectHandler&&) [with ConnectHandler = boost::beast::detail::bind_front_wrapper<void (ORB_SLAM2::WS::Client::session::*)(boost::system::error_code, boost::asio::ip::basic_endpoint<boost::asio::ip::tcp>), std::shared_ptr<ORB_SLAM2::WS::Client::session> >; Protocol = boost::asio::ip::tcp; Executor = boost::asio::executor; RatePolicy = boost::beast::unlimited_rate_policy; typename boost::asio::async_result<typename std::decay<_U1>::type, void(boost::system::error_code)>::return_type = void; boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::endpoint_type = boost::asio::ip::basic_endpoint<boost::asio::ip::tcp>]’  
/home/ubuntu/SwarmMap/code/include/WebSocket.h:129:44:   required from here  
/usr/include/c++/7/functional:174:27: error: no matching function for call to ‘__invoke(void (ORB_SLAM2::WS::Client::session::* const&)(boost::system::error_code, boost::asio::ip::basic_endpoint<boost::asio::ip::tcp>), std::shared_ptr<ORB_SLAM2::WS::Client::session>, boost::system::error_code&)’  
  -> decltype(std::__invoke(_M_pmf, std::forward<_Args>(__args)...))  
              ~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /usr/include/c++/7/tuple:41:0,  
                 from /usr/include/c++/7/bits/unique_ptr.h:37,  
                 from /usr/include/c++/7/bits/locale_conv.h:41,  
                 from /usr/include/c++/7/locale:43,  
                 from /usr/include/c++/7/iomanip:43,  
                 from /home/ubuntu/SwarmMap/code/Examples/Monocular/client.cc:25:  
/usr/include/c++/7/bits/invoke.h:89:5: note: candidate: template<class _Callable, class ... _Args> constexpr typename std::__invoke_result<_Functor, _ArgTypes>::type std::__invoke(_Callable&&, _Args&& ...)  
     __invoke(_Callable&& __fn, _Args&&... __args)  
     ^~~~~~~~  
/usr/include/c++/7/bits/invoke.h:89:5: note:   template argument deduction/substitution failed:  
/usr/include/c++/7/bits/invoke.h: In substitution of ‘template<class _Callable, class ... _Args> constexpr typename std::__invoke_result<_Functor, _ArgTypes>::type std::__invoke(_Callable&&, _Args&& ...) [with _Callable = void (ORB_SLAM2::WS::Client::session::* const&)(boost::system::error_code, boost::asio::ip::basic_endpoint<boost::asio::ip::tcp>); _Args = {std::shared_ptr<ORB_SLAM2::WS::Client::session>, boost::system::error_code&}]’:  
/usr/include/c++/7/functional:174:27:   [ skipping 22 instantiation contexts, use -ftemplate-backtrace-limit=0 to disable ]  
/usr/local/include/boost/beast/core/impl/basic_stream.hpp:812:26:   required from ‘typename boost::asio::async_result<typename std::decay<_U1>::type, void(boost::system::error_code)>::return_type boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(const endpoint_type&, ConnectHandler&&) [with ConnectHandler = boost::beast::detail::bind_front_wrapper<void (ORB_SLAM2::WS::Client::session::*)(boost::system::error_code, boost::asio::ip::basic_endpoint<boost::asio::ip::tcp>), std::shared_ptr<ORB_SLAM2::WS::Client::session> >; Protocol = boost::asio::ip::tcp; Executor = boost::asio::executor; RatePolicy = boost::beast::unlimited_rate_policy; typename boost::asio::async_result<typename std::decay<_U1>::type, void(boost::system::error_code)>::return_type = void; boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::endpoint_type = boost::asio::ip::basic_endpoint<boost::asio::ip::tcp>]’  
/home/ubuntu/SwarmMap/code/include/WebSocket.h:129:44:   required from here  
/usr/include/c++/7/bits/invoke.h:89:5: error: no type named ‘type’ in ‘struct std::__invoke_result<void (ORB_SLAM2::WS::Client::session::* const&)(boost::system::error_code, boost::asio::ip::basic_endpoint<boost::asio::ip::tcp>), std::shared_ptr<ORB_SLAM2::WS::Client::session>, boost::system::error_code&>’

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-02-20 03:45:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2642#issuecomment-1436279462  

The async_connect completion handler doesn't deliver an endpoint to the completion handler, but only the error_code. Hence your signature doesn't match.

---

## Comment 2

> Username: chivas1000  
> Created at: 2023-02-20 04:01:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2642#issuecomment-1436288560  

> The async_connect completion handler doesn't deliver an endpoint to the completion handler, but only the error_code. Hence your signature doesn't match.  
  
so by using beast async_connect(), it has to use a resolver which could query endpoints and pass error code?   
but is it the resolver would start another socket so the previous binding of local endpoint are not valid?  
now I don't know if binding a local endpoint needs manually operate all with lowest layer or not.  
would it be possible to give some directions?  
Thanks for your useful information!

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2023-02-20 05:18:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2642#issuecomment-1436346695  

Sure:  
  
```cpp  
 on_connect(beast::error_code ec, tcp::endpoint ep) {  
```  
  
this is wrong, and should be  
  
```cpp  
 on_connect(beast::error_code ec) {  
```

---

## Comment 4

> Username: chivas1000  
> Created at: 2023-02-21 09:50:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2642#issuecomment-1438174543  

> Sure:  
>   
> ```c++  
>  on_connect(beast::error_code ec, tcp::endpoint ep) {  
> ```  
>   
> this is wrong, and should be  
>   
> ```c++  
>  on_connect(beast::error_code ec) {  
> ```  
  
It works, thanks!
