# #2892 - any_executor_base::destroy_object and any_executor_base::copy_object high CPU usage [Closed]

> Username: gunzino  
> Created at: 2024-06-23 10:21:40 UTC  
> Updated at: 2024-08-07 15:42:24 UTC  
> Closed at: 2024-08-07 15:42:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2892  

Hello,  
  
### Boost version: 1.74  
### Beast Version: 300  
  
I've developed WebSocket server based on Beast which accepts a lot of messages of small size.  The program needs quite a lot of CPU resources to work, perf shows it's mostly those functions:  
boost::asio::execution::detail::any_executor_base::copy_object<boost::asio::io_context::basic_executor_type<std::allocator<void>, 4u> >  
boost::asio::execution::detail::any_executor_base::destroy_object<boost::asio::io_context::basic_executor_type<std::allocator<void>, 4u> >  
  
Server has about 250 open connections, accepting about 20 messages/second per connection. Message size between 100-5000 bytes.  
  
Is it indended behavior?  
  
![image](https://github.com/boostorg/beast/assets/6863683/74bbed76-cb41-4af8-9d56-732584b17ebb)

---

## Comment 1

> Username: ashtum  
> Created at: 2024-06-23 11:06:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2892#issuecomment-2184945575  

Please make sure you are building the application in release mode. Boost.Beast and Boost.Asio have a lot of indirection and generic functions, which result in a very slow debug executable.  
  
You can eliminate the need for `any_executor` by using a concrete executor type, for example:  
```C++  
using tcp_stream = beast::basic_stream<  
    asio::ip::tcp,  
    asio::io_context::executor_type,  
    beast::unlimited_rate_policy>;  
```  
or if you are using a strand:  
```C++  
using tcp_stream = beast::basic_stream<  
    asio::ip::tcp,  
    asio::strand<asio::io_context::executor_type>,  
    beast::unlimited_rate_policy>;  
```  
  
However, given your load (250 connections, 20 req/s each), this seems an unnecessary optimization. What is your total CPU usage for running your application?

---

## Comment 2

> Username: gunzino  
> Created at: 2024-06-24 07:51:54 UTC  
> Updated at: 2024-06-24 08:08:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2892#issuecomment-2185844028  

@ashtum   
My CPU usage just became 100%, that's how I've noticed it. It's single core cheap VPS but still.  
Currently using -Ofast, will try to compile also without debug symbols (-DNDEBUG).  
  
Your code suggestion is not friend with my code.  
  
```  
using tcp_stream = boost::beast::basic_stream<  
boost::asio::ip::tcp,  
boost::asio::io_context::executor_type,  
boost::beast::unlimited_rate_policy>;  
  
// http  
tcp_stream  stream_;  
boost::beast::flat_buffer buffer_;  
std::shared_ptr<std::string const> doc_root_;  
boost::beast::http::request<boost::beast::http::string_body> req_;  
  
// websocket  
std::optional<boost::beast::websocket::stream<tcp_stream>> ws_;  
```  
  
```  
 [9/9] RUN g++ main.cpp -O2 -s -std=c++17 -DNDEBUG server.cpp proxy_websocket.cpp -lboost_system -lpthread -o server:  
4.340 In file included from /usr/include/boost/beast/core/basic_stream.hpp:1458,  
4.340                  from /usr/include/boost/beast/core.hpp:16,  
4.340                  from proxy_websocket.h:2,  
4.340                  from server.cpp:5:  
4.340 /usr/include/boost/beast/core/impl/basic_stream.hpp: In instantiation of 'boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::impl_type::impl_  
type(std::false_type, Args&& ...) [with Args = {boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::execution::any_executor<boost::asio::execution::context_as_t<boost::asio::execution_cont  
ext&>, boost::asio::execution::detail::blocking::never_t<0>, boost::asio::execution::prefer_only<boost::asio::execution::detail::blocking::possibly_t<0> >, boost::asio::execution::prefer_only<boost::asio:  
:execution::detail::outstanding_work::tracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::untracked_t<0> >, boost::asio::execution::prefer_only<boost::asi  
o::execution::detail::relationship::fork_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::continuation_t<0> > > >}; Protocol = boost::asio::ip::tcp; Executor = boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>; RatePolicy = boost::beast::unlimited_rate_policy; std::false_type = std::integral_constant<bool, false>]':  
4.340 /usr/include/boost/smart_ptr/make_shared_object.hpp:256:5:   required from 'typename boost::detail::sp_if_not_array<T>::type boost::make_shared(Args&& ...) [with T = boost::beast::basic_stream<boost  
::asio::ip::tcp, boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>, boost::beast::unlimited_rate_policy>::impl_type; Args = {std::integral_constant<bool, false>, boost::asio::basic_str  
eam_socket<boost::asio::ip::tcp, boost::asio::execution::any_executor<boost::asio::execution::context_as_t<boost::asio::execution_context&>, boost::asio::execution::detail::blocking::never_t<0>, boost::as  
io::execution::prefer_only<boost::asio::execution::detail::blocking::possibly_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::tracked_t<0> >, boost::asio::exe  
cution::prefer_only<boost::asio::execution::detail::outstanding_work::untracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::fork_t<0> >, boost::asio::executio  
n::prefer_only<boost::asio::execution::detail::relationship::continuation_t<0> > > >}; typename boost::detail::sp_if_not_array<T>::type = boost::shared_ptr<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>, boost::beast::unlimited_rate_policy>::impl_type>]'  
4.340 /usr/include/boost/beast/core/impl/basic_stream.hpp:658:42:   required from 'boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::basic_stream(  
Arg0&&, Args&& ...) [with Arg0 = boost::asio::basic_stream_socket<boost::asio::ip::tcp>; Args = {}; <template-parameter-2-3> = void; Protocol = boost::asio::ip::tcp; Executor = boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>; RatePolicy = boost::beast::unlimited_rate_policy]'  
4.340 proxy_websocket.h:34:158:   required from here  
4.340 /usr/include/boost/beast/core/impl/basic_stream.hpp:38:17: error: no matching function for call to 'boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::io_context::basic_executor_type<std::allocator<void>, 0> >::basic_stream_socket(boost::asio::basic_stream_socket<boost::asio::ip::tcp>)'  
4.340    38 |     , timer(ex())  
4.340       |                 ^  
4.340 In file included from /usr/include/boost/asio/basic_socket_streambuf.hpp:25,  
4.340                  from /usr/include/boost/asio/basic_socket_iostream.hpp:24,  
4.340                  from /usr/include/boost/asio.hpp:33,  
4.340                  from server.h:4,  
4.340                  from server.cpp:4:  
4.340 /usr/include/boost/asio/basic_stream_socket.hpp:292:3: note: candidate: 'template<class Protocol1, class Executor1> boost::asio::basic_stream_socket<Protocol, Executor>::basic_stream_socket(boost::a  
sio::basic_stream_socket<Protocol1, Executor1>&&, typename std::enable_if<(std::is_convertible<Protocol1, Protocol>::value && std::is_convertible<_Ep, _Dp>::value)>::type*) [with Protocol1 = Protocol1; Executor1 = Executor1; Protocol = boost::asio::ip::tcp; Executor = boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>]'  
4.340   292 |   basic_stream_socket(basic_stream_socket<Protocol1, Executor1>&& other,  
4.340       |   ^~~~~~~~~~~~~~~~~~~  
4.340 /usr/include/boost/asio/basic_stream_socket.hpp:292:3: note:   template argument deduction/substitution failed:  
4.340 /usr/include/boost/asio/basic_stream_socket.hpp: In substitution of 'template<class Protocol1, class Executor1> boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::io_context::basic_  
executor_type<std::allocator<void>, 0> >::basic_stream_socket(boost::asio::basic_stream_socket<Protocol1, Executor1>&&, typename std::enable_if<(std::is_convertible<Protocol1, boost::asio::ip::tcp>::value  
 && std::is_convertible<_Ep, boost::asio::io_context::basic_executor_type<std::allocator<void>, 0> >::value), void>::type*) [with Protocol1 = boost::asio::ip::tcp; Executor1 = boost::asio::execution::any_  
executor<boost::asio::execution::context_as_t<boost::asio::execution_context&>, boost::asio::execution::detail::blocking::never_t<0>, boost::asio::execution::prefer_only<boost::asio::execution::detail::bl  
ocking::possibly_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::tracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstandi  
ng_work::untracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::fork_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::continuation_t<0> > >]':  
4.340 /usr/include/boost/beast/core/impl/basic_stream.hpp:38:17:   required from 'boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::impl_type::imp  
l_type(std::false_type, Args&& ...) [with Args = {boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::execution::any_executor<boost::asio::execution::context_as_t<boost::asio::execution_co  
ntext&>, boost::asio::execution::detail::blocking::never_t<0>, boost::asio::execution::prefer_only<boost::asio::execution::detail::blocking::possibly_t<0> >, boost::asio::execution::prefer_only<boost::asi  
o::execution::detail::outstanding_work::tracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::untracked_t<0> >, boost::asio::execution::prefer_only<boost::a  
sio::execution::detail::relationship::fork_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::continuation_t<0> > > >}; Protocol = boost::asio::ip::tcp; Executor = boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>; RatePolicy = boost::beast::unlimited_rate_policy; std::false_type = std::integral_constant<bool, false>]'  
4.340 /usr/include/boost/smart_ptr/make_shared_object.hpp:256:5:   required from 'typename boost::detail::sp_if_not_array<T>::type boost::make_shared(Args&& ...) [with T = boost::beast::basic_stream<boost  
::asio::ip::tcp, boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>, boost::beast::unlimited_rate_policy>::impl_type; Args = {std::integral_constant<bool, false>, boost::asio::basic_str  
eam_socket<boost::asio::ip::tcp, boost::asio::execution::any_executor<boost::asio::execution::context_as_t<boost::asio::execution_context&>, boost::asio::execution::detail::blocking::never_t<0>, boost::as  
io::execution::prefer_only<boost::asio::execution::detail::blocking::possibly_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::tracked_t<0> >, boost::asio::exe  
cution::prefer_only<boost::asio::execution::detail::outstanding_work::untracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::fork_t<0> >, boost::asio::executio  
n::prefer_only<boost::asio::execution::detail::relationship::continuation_t<0> > > >}; typename boost::detail::sp_if_not_array<T>::type = boost::shared_ptr<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>, boost::beast::unlimited_rate_policy>::impl_type>]'  
4.340 /usr/include/boost/beast/core/impl/basic_stream.hpp:658:42:   required from 'boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::basic_stream(  
Arg0&&, Args&& ...) [with Arg0 = boost::asio::basic_stream_socket<boost::asio::ip::tcp>; Args = {}; <template-parameter-2-3> = void; Protocol = boost::asio::ip::tcp; Executor = boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>; RatePolicy = boost::beast::unlimited_rate_policy]'  
4.340 proxy_websocket.h:34:158:   required from here  
4.340 /usr/include/boost/asio/basic_stream_socket.hpp:292:3: error: no type named 'type' in 'struct std::enable_if<false, void>'  
4.340 /usr/include/boost/beast/core/impl/basic_stream.hpp: In instantiation of 'boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::impl_type::impl_  
type(std::false_type, Args&& ...) [with Args = {boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::execution::any_executor<boost::asio::execution::context_as_t<boost::asio::execution_cont  
ext&>, boost::asio::execution::detail::blocking::never_t<0>, boost::asio::execution::prefer_only<boost::asio::execution::detail::blocking::possibly_t<0> >, boost::asio::execution::prefer_only<boost::asio:  
:execution::detail::outstanding_work::tracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::untracked_t<0> >, boost::asio::execution::prefer_only<boost::asi  
o::execution::detail::relationship::fork_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::continuation_t<0> > > >}; Protocol = boost::asio::ip::tcp; Executor = boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>; RatePolicy = boost::beast::unlimited_rate_policy; std::false_type = std::integral_constant<bool, false>]':  
4.340 /usr/include/boost/smart_ptr/make_shared_object.hpp:256:5:   required from 'typename boost::detail::sp_if_not_array<T>::type boost::make_shared(Args&& ...) [with T = boost::beast::basic_stream<boost  
::asio::ip::tcp, boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>, boost::beast::unlimited_rate_policy>::impl_type; Args = {std::integral_constant<bool, false>, boost::asio::basic_str  
eam_socket<boost::asio::ip::tcp, boost::asio::execution::any_executor<boost::asio::execution::context_as_t<boost::asio::execution_context&>, boost::asio::execution::detail::blocking::never_t<0>, boost::as  
io::execution::prefer_only<boost::asio::execution::detail::blocking::possibly_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::tracked_t<0> >, boost::asio::exe  
cution::prefer_only<boost::asio::execution::detail::outstanding_work::untracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::fork_t<0> >, boost::asio::executio  
n::prefer_only<boost::asio::execution::detail::relationship::continuation_t<0> > > >}; typename boost::detail::sp_if_not_array<T>::type = boost::shared_ptr<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>, boost::beast::unlimited_rate_policy>::impl_type>]'  
4.340 /usr/include/boost/beast/core/impl/basic_stream.hpp:658:42:   required from 'boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::basic_stream(  
Arg0&&, Args&& ...) [with Arg0 = boost::asio::basic_stream_socket<boost::asio::ip::tcp>; Args = {}; <template-parameter-2-3> = void; Protocol = boost::asio::ip::tcp; Executor = boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>; RatePolicy = boost::beast::unlimited_rate_policy]'  
4.340 proxy_websocket.h:34:158:   required from here  
4.340 /usr/include/boost/asio/basic_stream_socket.hpp:257:3: note: candidate: 'boost::asio::basic_stream_socket<Protocol, Executor>::basic_stream_socket(boost::asio::basic_stream_socket<Protocol, Executor>&&) [with Protocol = boost::asio::ip::tcp; Executor = boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>]'  
4.340   257 |   basic_stream_socket(basic_stream_socket&& other) BOOST_ASIO_NOEXCEPT  
4.340       |   ^~~~~~~~~~~~~~~~~~~  
4.340 /usr/include/boost/asio/basic_stream_socket.hpp:257:45: note:   no known conversion for argument 1 from 'boost::asio::basic_stream_socket<boost::asio::ip::tcp>' to 'boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::io_context::basic_executor_type<std::allocator<void>, 0> >&&'  
4.340   257 |   basic_stream_socket(basic_stream_socket&& other) BOOST_ASIO_NOEXCEPT  
4.340       |                       ~~~~~~~~~~~~~~~~~~~~~~^~~~~  
4.340 /usr/include/boost/asio/basic_stream_socket.hpp:236:3: note: candidate: 'template<class ExecutionContext> boost::asio::basic_stream_socket<Protocol, Executor>::basic_stream_socket(ExecutionContext&,  
 const protocol_type&, const native_handle_type&, typename std::enable_if<std::is_convertible<ExecutionContext&, boost::asio::execution_context&>::value>::type*) [with ExecutionContext = ExecutionContext; Protocol = boost::asio::ip::tcp; Executor = boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>]'  
4.340   236 |   basic_stream_socket(ExecutionContext& context,  
4.340       |   ^~~~~~~~~~~~~~~~~~~  
4.340 /usr/include/boost/asio/basic_stream_socket.hpp:236:3: note:   template argument deduction/substitution failed:  
4.340 In file included from /usr/include/boost/beast/core/basic_stream.hpp:1458,  
4.340                  from /usr/include/boost/beast/core.hpp:16,  
4.340                  from proxy_websocket.h:2,  
4.340                  from server.cpp:5:  
4.340 /usr/include/boost/beast/core/impl/basic_stream.hpp:38:17: note:   candidate expects 4 arguments, 1 provided  
4.340    38 |     , timer(ex())  
4.340       |                 ^  
4.340 In file included from /usr/include/boost/asio/basic_socket_streambuf.hpp:25,  
4.340                  from /usr/include/boost/asio/basic_socket_iostream.hpp:24,  
4.340                  from /usr/include/boost/asio.hpp:33,  
4.340                  from server.h:4,  
4.340                  from server.cpp:4:  
4.340 /usr/include/boost/asio/basic_stream_socket.hpp:214:3: note: candidate: 'boost::asio::basic_stream_socket<Protocol, Executor>::basic_stream_socket(const executor_type&, const protocol_type&, const n  
ative_handle_type&) [with Protocol = boost::asio::ip::tcp; Executor = boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>; boost::asio::basic_stream_socket<Protocol, Executor>::executor_  
type = boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>; boost::asio::basic_stream_socket<Protocol, Executor>::protocol_type = boost::asio::ip::tcp; boost::asio::basic_stream_socket<Protocol, Executor>::native_handle_type = int]'  
4.340   214 |   basic_stream_socket(const executor_type& ex,  
4.340       |   ^~~~~~~~~~~~~~~~~~~  
4.340 /usr/include/boost/asio/basic_stream_socket.hpp:214:3: note:   candidate expects 3 arguments, 1 provided  
4.340 /usr/include/boost/asio/basic_stream_socket.hpp:192:3: note: candidate: 'template<class ExecutionContext> boost::asio::basic_stream_socket<Protocol, Executor>::basic_stream_socket(ExecutionContext&,  
 const endpoint_type&, typename std::enable_if<std::is_convertible<ExecutionContext&, boost::asio::execution_context&>::value>::type*) [with ExecutionContext = ExecutionContext; Protocol = boost::asio::ip::tcp; Executor = boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>]'  
4.340   192 |   basic_stream_socket(ExecutionContext& context, const endpoint_type& endpoint,  
4.340       |   ^~~~~~~~~~~~~~~~~~~  
4.340 /usr/include/boost/asio/basic_stream_socket.hpp:192:3: note:   template argument deduction/substitution failed:  
4.340 In file included from /usr/include/boost/beast/core/basic_stream.hpp:1458,  
4.340                  from /usr/include/boost/beast/core.hpp:16,  
4.340                  from proxy_websocket.h:2,  
4.340                  from server.cpp:5:  
4.340 /usr/include/boost/beast/core/impl/basic_stream.hpp:38:17: note:   candidate expects 3 arguments, 1 provided  
4.340    38 |     , timer(ex())  
4.340       |                 ^  
4.340 In file included from /usr/include/boost/asio/basic_socket_streambuf.hpp:25,  
4.340                  from /usr/include/boost/asio/basic_socket_iostream.hpp:24,  
4.340                  from /usr/include/boost/asio.hpp:33,  
4.340                  from server.h:4,  
4.340                  from server.cpp:4:  
4.340 /usr/include/boost/asio/basic_stream_socket.hpp:170:3: note: candidate: 'boost::asio::basic_stream_socket<Protocol, Executor>::basic_stream_socket(const executor_type&, const endpoint_type&) [with P  
rotocol = boost::asio::ip::tcp; Executor = boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>; boost::asio::basic_stream_socket<Protocol, Executor>::executor_type = boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>; boost::asio::basic_stream_socket<Protocol, Executor>::endpoint_type = boost::asio::ip::basic_endpoint<boost::asio::ip::tcp>]'  
4.340   170 |   basic_stream_socket(const executor_type& ex, const endpoint_type& endpoint)  
4.340       |   ^~~~~~~~~~~~~~~~~~~  
4.340 /usr/include/boost/asio/basic_stream_socket.hpp:170:3: note:   candidate expects 2 arguments, 1 provided  
4.340 /usr/include/boost/asio/basic_stream_socket.hpp:147:3: note: candidate: 'template<class ExecutionContext> boost::asio::basic_stream_socket<Protocol, Executor>::basic_stream_socket(ExecutionContext&,  
 const protocol_type&, typename std::enable_if<std::is_convertible<ExecutionContext&, boost::asio::execution_context&>::value>::type*) [with ExecutionContext = ExecutionContext; Protocol = boost::asio::ip::tcp; Executor = boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>]'  
4.340   147 |   basic_stream_socket(ExecutionContext& context, const protocol_type& protocol,  
4.340       |   ^~~~~~~~~~~~~~~~~~~  
4.340 /usr/include/boost/asio/basic_stream_socket.hpp:147:3: note:   template argument deduction/substitution failed:  
4.340 In file included from /usr/include/boost/beast/core/basic_stream.hpp:1458,  
4.340                  from /usr/include/boost/beast/core.hpp:16,  
4.340                  from proxy_websocket.h:2,  
4.340                  from server.cpp:5:  
4.340 /usr/include/boost/beast/core/impl/basic_stream.hpp:38:17: note:   candidate expects 3 arguments, 1 provided  
4.340    38 |     , timer(ex())  
4.340       |                 ^  
4.341 In file included from /usr/include/boost/asio/basic_socket_streambuf.hpp:25,  
4.341                  from /usr/include/boost/asio/basic_socket_iostream.hpp:24,  
4.341                  from /usr/include/boost/asio.hpp:33,  
4.341                  from server.h:4,  
4.341                  from server.cpp:4:  
4.341 /usr/include/boost/asio/basic_stream_socket.hpp:128:3: note: candidate: 'boost::asio::basic_stream_socket<Protocol, Executor>::basic_stream_socket(const executor_type&, const protocol_type&) [with P  
rotocol = boost::asio::ip::tcp; Executor = boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>; boost::asio::basic_stream_socket<Protocol, Executor>::executor_type = boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>; boost::asio::basic_stream_socket<Protocol, Executor>::protocol_type = boost::asio::ip::tcp]'  
4.341   128 |   basic_stream_socket(const executor_type& ex, const protocol_type& protocol)  
4.341       |   ^~~~~~~~~~~~~~~~~~~  
4.341 /usr/include/boost/asio/basic_stream_socket.hpp:128:3: note:   candidate expects 2 arguments, 1 provided  
4.341 /usr/include/boost/asio/basic_stream_socket.hpp:108:12: note: candidate: 'template<class ExecutionContext> boost::asio::basic_stream_socket<Protocol, Executor>::basic_stream_socket(ExecutionContext&  
, typename std::enable_if<std::is_convertible<ExecutionContext&, boost::asio::execution_context&>::value>::type*) [with ExecutionContext = ExecutionContext; Protocol = boost::asio::ip::tcp; Executor = boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>]'  
4.341   108 |   explicit basic_stream_socket(ExecutionContext& context,  
4.341       |            ^~~~~~~~~~~~~~~~~~~  
4.341 /usr/include/boost/asio/basic_stream_socket.hpp:108:12: note:   template argument deduction/substitution failed:  
4.341 /usr/include/boost/asio/basic_stream_socket.hpp: In substitution of 'template<class ExecutionContext> boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::io_context::basic_executor_t  
ype<std::allocator<void>, 0> >::basic_stream_socket(ExecutionContext&, typename std::enable_if<std::is_convertible<ExecutionContext&, boost::asio::execution_context&>::value>::type*) [with ExecutionContext = boost::asio::basic_stream_socket<boost::asio::ip::tcp>]':  
4.341 /usr/include/boost/beast/core/impl/basic_stream.hpp:38:17:   required from 'boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::impl_type::imp  
l_type(std::false_type, Args&& ...) [with Args = {boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::execution::any_executor<boost::asio::execution::context_as_t<boost::asio::execution_co  
ntext&>, boost::asio::execution::detail::blocking::never_t<0>, boost::asio::execution::prefer_only<boost::asio::execution::detail::blocking::possibly_t<0> >, boost::asio::execution::prefer_only<boost::asi  
o::execution::detail::outstanding_work::tracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::untracked_t<0> >, boost::asio::execution::prefer_only<boost::a  
sio::execution::detail::relationship::fork_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::continuation_t<0> > > >}; Protocol = boost::asio::ip::tcp; Executor = boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>; RatePolicy = boost::beast::unlimited_rate_policy; std::false_type = std::integral_constant<bool, false>]'  
4.341 /usr/include/boost/smart_ptr/make_shared_object.hpp:256:5:   required from 'typename boost::detail::sp_if_not_array<T>::type boost::make_shared(Args&& ...) [with T = boost::beast::basic_stream<boost  
::asio::ip::tcp, boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>, boost::beast::unlimited_rate_policy>::impl_type; Args = {std::integral_constant<bool, false>, boost::asio::basic_str  
eam_socket<boost::asio::ip::tcp, boost::asio::execution::any_executor<boost::asio::execution::context_as_t<boost::asio::execution_context&>, boost::asio::execution::detail::blocking::never_t<0>, boost::as  
io::execution::prefer_only<boost::asio::execution::detail::blocking::possibly_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::tracked_t<0> >, boost::asio::exe  
cution::prefer_only<boost::asio::execution::detail::outstanding_work::untracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::fork_t<0> >, boost::asio::executio  
n::prefer_only<boost::asio::execution::detail::relationship::continuation_t<0> > > >}; typename boost::detail::sp_if_not_array<T>::type = boost::shared_ptr<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>, boost::beast::unlimited_rate_policy>::impl_type>]'  
4.341 /usr/include/boost/beast/core/impl/basic_stream.hpp:658:42:   required from 'boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::basic_stream(  
Arg0&&, Args&& ...) [with Arg0 = boost::asio::basic_stream_socket<boost::asio::ip::tcp>; Args = {}; <template-parameter-2-3> = void; Protocol = boost::asio::ip::tcp; Executor = boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>; RatePolicy = boost::beast::unlimited_rate_policy]'  
4.341 proxy_websocket.h:34:158:   required from here  
4.341 /usr/include/boost/asio/basic_stream_socket.hpp:108:12: error: no type named 'type' in 'struct std::enable_if<false, void>'  
4.341 /usr/include/boost/beast/core/impl/basic_stream.hpp: In instantiation of 'boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::impl_type::impl_  
type(std::false_type, Args&& ...) [with Args = {boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::execution::any_executor<boost::asio::execution::context_as_t<boost::asio::execution_cont  
ext&>, boost::asio::execution::detail::blocking::never_t<0>, boost::asio::execution::prefer_only<boost::asio::execution::detail::blocking::possibly_t<0> >, boost::asio::execution::prefer_only<boost::asio:  
:execution::detail::outstanding_work::tracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::untracked_t<0> >, boost::asio::execution::prefer_only<boost::asi  
o::execution::detail::relationship::fork_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::continuation_t<0> > > >}; Protocol = boost::asio::ip::tcp; Executor = boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>; RatePolicy = boost::beast::unlimited_rate_policy; std::false_type = std::integral_constant<bool, false>]':  
4.341 /usr/include/boost/smart_ptr/make_shared_object.hpp:256:5:   required from 'typename boost::detail::sp_if_not_array<T>::type boost::make_shared(Args&& ...) [with T = boost::beast::basic_stream<boost  
::asio::ip::tcp, boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>, boost::beast::unlimited_rate_policy>::impl_type; Args = {std::integral_constant<bool, false>, boost::asio::basic_str  
eam_socket<boost::asio::ip::tcp, boost::asio::execution::any_executor<boost::asio::execution::context_as_t<boost::asio::execution_context&>, boost::asio::execution::detail::blocking::never_t<0>, boost::as  
io::execution::prefer_only<boost::asio::execution::detail::blocking::possibly_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::tracked_t<0> >, boost::asio::exe  
cution::prefer_only<boost::asio::execution::detail::outstanding_work::untracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::fork_t<0> >, boost::asio::executio  
n::prefer_only<boost::asio::execution::detail::relationship::continuation_t<0> > > >}; typename boost::detail::sp_if_not_array<T>::type = boost::shared_ptr<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>, boost::beast::unlimited_rate_policy>::impl_type>]'  
4.341 /usr/include/boost/beast/core/impl/basic_stream.hpp:658:42:   required from 'boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::basic_stream(  
Arg0&&, Args&& ...) [with Arg0 = boost::asio::basic_stream_socket<boost::asio::ip::tcp>; Args = {}; <template-parameter-2-3> = void; Protocol = boost::asio::ip::tcp; Executor = boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>; RatePolicy = boost::beast::unlimited_rate_policy]'  
4.341 proxy_websocket.h:34:158:   required from here  
4.341 /usr/include/boost/asio/basic_stream_socket.hpp:92:12: note: candidate: 'boost::asio::basic_stream_socket<Protocol, Executor>::basic_stream_socket(const executor_type&) [with Protocol = boost::asio:  
:ip::tcp; Executor = boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>; boost::asio::basic_stream_socket<Protocol, Executor>::executor_type = boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>]'  
4.341    92 |   explicit basic_stream_socket(const executor_type& ex)  
4.341       |            ^~~~~~~~~~~~~~~~~~~  
4.341 /usr/include/boost/asio/basic_stream_socket.hpp:92:53: note:   no known conversion for argument 1 from 'boost::asio::basic_stream_socket<boost::asio::ip::tcp>' to 'const executor_type&' {aka 'const boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>&'}  
4.341    92 |   explicit basic_stream_socket(const executor_type& ex)  
4.341       |                                ~~~~~~~~~~~~~~~~~~~~~^~  
6.792 In file included from /usr/include/boost/beast/core/basic_stream.hpp:1458,  
6.792                  from /usr/include/boost/beast/core.hpp:16,  
6.792                  from proxy_websocket.h:2,  
6.792                  from proxy_websocket.cpp:1:  
6.792 /usr/include/boost/beast/core/impl/basic_stream.hpp: In instantiation of 'boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::impl_type::impl_  
type(std::false_type, Args&& ...) [with Args = {boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::execution::any_executor<boost::asio::execution::context_as_t<boost::asio::execution_cont  
ext&>, boost::asio::execution::detail::blocking::never_t<0>, boost::asio::execution::prefer_only<boost::asio::execution::detail::blocking::possibly_t<0> >, boost::asio::execution::prefer_only<boost::asio:  
:execution::detail::outstanding_work::tracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::untracked_t<0> >, boost::asio::execution::prefer_only<boost::asi  
o::execution::detail::relationship::fork_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::continuation_t<0> > > >}; Protocol = boost::asio::ip::tcp; Executor = boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>; RatePolicy = boost::beast::unlimited_rate_policy; std::false_type = std::integral_constant<bool, false>]':  
6.792 /usr/include/boost/smart_ptr/make_shared_object.hpp:256:5:   required from 'typename boost::detail::sp_if_not_array<T>::type boost::make_shared(Args&& ...) [with T = boost::beast::basic_stream<boost  
::asio::ip::tcp, boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>, boost::beast::unlimited_rate_policy>::impl_type; Args = {std::integral_constant<bool, false>, boost::asio::basic_str  
eam_socket<boost::asio::ip::tcp, boost::asio::execution::any_executor<boost::asio::execution::context_as_t<boost::asio::execution_context&>, boost::asio::execution::detail::blocking::never_t<0>, boost::as  
io::execution::prefer_only<boost::asio::execution::detail::blocking::possibly_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::tracked_t<0> >, boost::asio::exe  
cution::prefer_only<boost::asio::execution::detail::outstanding_work::untracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::fork_t<0> >, boost::asio::executio  
n::prefer_only<boost::asio::execution::detail::relationship::continuation_t<0> > > >}; typename boost::detail::sp_if_not_array<T>::type = boost::shared_ptr<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>, boost::beast::unlimited_rate_policy>::impl_type>]'  
6.792 /usr/include/boost/beast/core/impl/basic_stream.hpp:658:42:   required from 'boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::basic_stream(  
Arg0&&, Args&& ...) [with Arg0 = boost::asio::basic_stream_socket<boost::asio::ip::tcp>; Args = {}; <template-parameter-2-3> = void; Protocol = boost::asio::ip::tcp; Executor = boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>; RatePolicy = boost::beast::unlimited_rate_policy]'  
6.792 proxy_websocket.h:34:158:   required from here  
6.792 /usr/include/boost/beast/core/impl/basic_stream.hpp:38:17: error: no matching function for call to 'boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::io_context::basic_executor_type<std::allocator<void>, 0> >::basic_stream_socket(boost::asio::basic_stream_socket<boost::asio::ip::tcp>)'  
6.792    38 |     , timer(ex())  
6.792       |                 ^  
6.792 In file included from /usr/include/boost/asio/basic_socket_streambuf.hpp:25,  
6.792                  from /usr/include/boost/asio/basic_socket_iostream.hpp:24,  
6.792                  from /usr/include/boost/asio.hpp:33,  
6.792                  from server.h:4,  
6.792                  from proxy_websocket.h:1,  
6.792                  from proxy_websocket.cpp:1:  
6.792 /usr/include/boost/asio/basic_stream_socket.hpp:292:3: note: candidate: 'template<class Protocol1, class Executor1> boost::asio::basic_stream_socket<Protocol, Executor>::basic_stream_socket(boost::a  
sio::basic_stream_socket<Protocol1, Executor1>&&, typename std::enable_if<(std::is_convertible<Protocol1, Protocol>::value && std::is_convertible<_Ep, _Dp>::value)>::type*) [with Protocol1 = Protocol1; Executor1 = Executor1; Protocol = boost::asio::ip::tcp; Executor = boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>]'  
6.792   292 |   basic_stream_socket(basic_stream_socket<Protocol1, Executor1>&& other,  
6.792       |   ^~~~~~~~~~~~~~~~~~~  
6.792 /usr/include/boost/asio/basic_stream_socket.hpp:292:3: note:   template argument deduction/substitution failed:  
6.792 /usr/include/boost/asio/basic_stream_socket.hpp: In substitution of 'template<class Protocol1, class Executor1> boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::io_context::basic_  
executor_type<std::allocator<void>, 0> >::basic_stream_socket(boost::asio::basic_stream_socket<Protocol1, Executor1>&&, typename std::enable_if<(std::is_convertible<Protocol1, boost::asio::ip::tcp>::value  
 && std::is_convertible<_Ep, boost::asio::io_context::basic_executor_type<std::allocator<void>, 0> >::value), void>::type*) [with Protocol1 = boost::asio::ip::tcp; Executor1 = boost::asio::execution::any_  
executor<boost::asio::execution::context_as_t<boost::asio::execution_context&>, boost::asio::execution::detail::blocking::never_t<0>, boost::asio::execution::prefer_only<boost::asio::execution::detail::bl  
ocking::possibly_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::tracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstandi  
ng_work::untracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::fork_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::continuation_t<0> > >]':  
6.792 /usr/include/boost/beast/core/impl/basic_stream.hpp:38:17:   required from 'boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::impl_type::imp  
l_type(std::false_type, Args&& ...) [with Args = {boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::execution::any_executor<boost::asio::execution::context_as_t<boost::asio::execution_co  
ntext&>, boost::asio::execution::detail::blocking::never_t<0>, boost::asio::execution::prefer_only<boost::asio::execution::detail::blocking::possibly_t<0> >, boost::asio::execution::prefer_only<boost::asi  
o::execution::detail::outstanding_work::tracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::untracked_t<0> >, boost::asio::execution::prefer_only<boost::a  
sio::execution::detail::relationship::fork_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::continuation_t<0> > > >}; Protocol = boost::asio::ip::tcp; Executor = boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>; RatePolicy = boost::beast::unlimited_rate_policy; std::false_type = std::integral_constant<bool, false>]'  
6.792 /usr/include/boost/smart_ptr/make_shared_object.hpp:256:5:   required from 'typename boost::detail::sp_if_not_array<T>::type boost::make_shared(Args&& ...) [with T = boost::beast::basic_stream<boost  
::asio::ip::tcp, boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>, boost::beast::unlimited_rate_policy>::impl_type; Args = {std::integral_constant<bool, false>, boost::asio::basic_str  
eam_socket<boost::asio::ip::tcp, boost::asio::execution::any_executor<boost::asio::execution::context_as_t<boost::asio::execution_context&>, boost::asio::execution::detail::blocking::never_t<0>, boost::as  
io::execution::prefer_only<boost::asio::execution::detail::blocking::possibly_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::tracked_t<0> >, boost::asio::exe  
cution::prefer_only<boost::asio::execution::detail::outstanding_work::untracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::fork_t<0> >, boost::asio::executio  
n::prefer_only<boost::asio::execution::detail::relationship::continuation_t<0> > > >}; typename boost::detail::sp_if_not_array<T>::type = boost::shared_ptr<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>, boost::beast::unlimited_rate_policy>::impl_type>]'  
6.792 /usr/include/boost/beast/core/impl/basic_stream.hpp:658:42:   required from 'boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::basic_stream(  
Arg0&&, Args&& ...) [with Arg0 = boost::asio::basic_stream_socket<boost::asio::ip::tcp>; Args = {}; <template-parameter-2-3> = void; Protocol = boost::asio::ip::tcp; Executor = boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>; RatePolicy = boost::beast::unlimited_rate_policy]'  
6.792 proxy_websocket.h:34:158:   required from here  
6.792 /usr/include/boost/asio/basic_stream_socket.hpp:292:3: error: no type named 'type' in 'struct std::enable_if<false, void>'  
6.792 /usr/include/boost/beast/core/impl/basic_stream.hpp: In instantiation of 'boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::impl_type::impl_  
type(std::false_type, Args&& ...) [with Args = {boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::execution::any_executor<boost::asio::execution::context_as_t<boost::asio::execution_cont  
ext&>, boost::asio::execution::detail::blocking::never_t<0>, boost::asio::execution::prefer_only<boost::asio::execution::detail::blocking::possibly_t<0> >, boost::asio::execution::prefer_only<boost::asio:  
:execution::detail::outstanding_work::tracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::untracked_t<0> >, boost::asio::execution::prefer_only<boost::asi  
o::execution::detail::relationship::fork_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::continuation_t<0> > > >}; Protocol = boost::asio::ip::tcp; Executor = boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>; RatePolicy = boost::beast::unlimited_rate_policy; std::false_type = std::integral_constant<bool, false>]':  
6.792 /usr/include/boost/smart_ptr/make_shared_object.hpp:256:5:   required from 'typename boost::detail::sp_if_not_array<T>::type boost::make_shared(Args&& ...) [with T = boost::beast::basic_stream<boost  
::asio::ip::tcp, boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>, boost::beast::unlimited_rate_policy>::impl_type; Args = {std::integral_constant<bool, false>, boost::asio::basic_str  
eam_socket<boost::asio::ip::tcp, boost::asio::execution::any_executor<boost::asio::execution::context_as_t<boost::asio::execution_context&>, boost::asio::execution::detail::blocking::never_t<0>, boost::as  
io::execution::prefer_only<boost::asio::execution::detail::blocking::possibly_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::tracked_t<0> >, boost::asio::exe  
cution::prefer_only<boost::asio::execution::detail::outstanding_work::untracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::fork_t<0> >, boost::asio::executio  
n::prefer_only<boost::asio::execution::detail::relationship::continuation_t<0> > > >}; typename boost::detail::sp_if_not_array<T>::type = boost::shared_ptr<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>, boost::beast::unlimited_rate_policy>::impl_type>]'  
6.792 /usr/include/boost/beast/core/impl/basic_stream.hpp:658:42:   required from 'boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::basic_stream(  
Arg0&&, Args&& ...) [with Arg0 = boost::asio::basic_stream_socket<boost::asio::ip::tcp>; Args = {}; <template-parameter-2-3> = void; Protocol = boost::asio::ip::tcp; Executor = boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>; RatePolicy = boost::beast::unlimited_rate_policy]'  
6.792 proxy_websocket.h:34:158:   required from here  
6.792 /usr/include/boost/asio/basic_stream_socket.hpp:257:3: note: candidate: 'boost::asio::basic_stream_socket<Protocol, Executor>::basic_stream_socket(boost::asio::basic_stream_socket<Protocol, Executor>&&) [with Protocol = boost::asio::ip::tcp; Executor = boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>]'  
6.792   257 |   basic_stream_socket(basic_stream_socket&& other) BOOST_ASIO_NOEXCEPT  
6.792       |   ^~~~~~~~~~~~~~~~~~~  
6.792 /usr/include/boost/asio/basic_stream_socket.hpp:257:45: note:   no known conversion for argument 1 from 'boost::asio::basic_stream_socket<boost::asio::ip::tcp>' to 'boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::io_context::basic_executor_type<std::allocator<void>, 0> >&&'  
6.792   257 |   basic_stream_socket(basic_stream_socket&& other) BOOST_ASIO_NOEXCEPT  
6.792       |                       ~~~~~~~~~~~~~~~~~~~~~~^~~~~  
6.793 /usr/include/boost/asio/basic_stream_socket.hpp:236:3: note: candidate: 'template<class ExecutionContext> boost::asio::basic_stream_socket<Protocol, Executor>::basic_stream_socket(ExecutionContext&,  
 const protocol_type&, const native_handle_type&, typename std::enable_if<std::is_convertible<ExecutionContext&, boost::asio::execution_context&>::value>::type*) [with ExecutionContext = ExecutionContext; Protocol = boost::asio::ip::tcp; Executor = boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>]'  
6.793   236 |   basic_stream_socket(ExecutionContext& context,  
6.793       |   ^~~~~~~~~~~~~~~~~~~  
6.793 /usr/include/boost/asio/basic_stream_socket.hpp:236:3: note:   template argument deduction/substitution failed:  
6.793 In file included from /usr/include/boost/beast/core/basic_stream.hpp:1458,  
6.793                  from /usr/include/boost/beast/core.hpp:16,  
6.793                  from proxy_websocket.h:2,  
6.793                  from proxy_websocket.cpp:1:  
6.793 /usr/include/boost/beast/core/impl/basic_stream.hpp:38:17: note:   candidate expects 4 arguments, 1 provided  
6.793    38 |     , timer(ex())  
6.793       |                 ^  
6.793 In file included from /usr/include/boost/asio/basic_socket_streambuf.hpp:25,  
6.793                  from /usr/include/boost/asio/basic_socket_iostream.hpp:24,  
6.793                  from /usr/include/boost/asio.hpp:33,  
6.793                  from server.h:4,  
6.793                  from proxy_websocket.h:1,  
6.793                  from proxy_websocket.cpp:1:  
6.793 /usr/include/boost/asio/basic_stream_socket.hpp:214:3: note: candidate: 'boost::asio::basic_stream_socket<Protocol, Executor>::basic_stream_socket(const executor_type&, const protocol_type&, const n  
ative_handle_type&) [with Protocol = boost::asio::ip::tcp; Executor = boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>; boost::asio::basic_stream_socket<Protocol, Executor>::executor_  
type = boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>; boost::asio::basic_stream_socket<Protocol, Executor>::protocol_type = boost::asio::ip::tcp; boost::asio::basic_stream_socket<Protocol, Executor>::native_handle_type = int]'  
6.793   214 |   basic_stream_socket(const executor_type& ex,  
6.793       |   ^~~~~~~~~~~~~~~~~~~  
6.793 /usr/include/boost/asio/basic_stream_socket.hpp:214:3: note:   candidate expects 3 arguments, 1 provided  
6.793 /usr/include/boost/asio/basic_stream_socket.hpp:192:3: note: candidate: 'template<class ExecutionContext> boost::asio::basic_stream_socket<Protocol, Executor>::basic_stream_socket(ExecutionContext&,  
 const endpoint_type&, typename std::enable_if<std::is_convertible<ExecutionContext&, boost::asio::execution_context&>::value>::type*) [with ExecutionContext = ExecutionContext; Protocol = boost::asio::ip::tcp; Executor = boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>]'  
6.793   192 |   basic_stream_socket(ExecutionContext& context, const endpoint_type& endpoint,  
6.793       |   ^~~~~~~~~~~~~~~~~~~  
6.793 /usr/include/boost/asio/basic_stream_socket.hpp:192:3: note:   template argument deduction/substitution failed:  
6.793 In file included from /usr/include/boost/beast/core/basic_stream.hpp:1458,  
6.793                  from /usr/include/boost/beast/core.hpp:16,  
6.793                  from proxy_websocket.h:2,  
6.793                  from proxy_websocket.cpp:1:  
6.793 /usr/include/boost/beast/core/impl/basic_stream.hpp:38:17: note:   candidate expects 3 arguments, 1 provided  
6.793    38 |     , timer(ex())  
6.793       |                 ^  
6.793 In file included from /usr/include/boost/asio/basic_socket_streambuf.hpp:25,  
6.793                  from /usr/include/boost/asio/basic_socket_iostream.hpp:24,  
6.793                  from /usr/include/boost/asio.hpp:33,  
6.793                  from server.h:4,  
6.793                  from proxy_websocket.h:1,  
6.793                  from proxy_websocket.cpp:1:  
6.793 /usr/include/boost/asio/basic_stream_socket.hpp:170:3: note: candidate: 'boost::asio::basic_stream_socket<Protocol, Executor>::basic_stream_socket(const executor_type&, const endpoint_type&) [with P  
rotocol = boost::asio::ip::tcp; Executor = boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>; boost::asio::basic_stream_socket<Protocol, Executor>::executor_type = boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>; boost::asio::basic_stream_socket<Protocol, Executor>::endpoint_type = boost::asio::ip::basic_endpoint<boost::asio::ip::tcp>]'  
6.793   170 |   basic_stream_socket(const executor_type& ex, const endpoint_type& endpoint)  
6.793       |   ^~~~~~~~~~~~~~~~~~~  
6.793 /usr/include/boost/asio/basic_stream_socket.hpp:170:3: note:   candidate expects 2 arguments, 1 provided  
6.793 /usr/include/boost/asio/basic_stream_socket.hpp:147:3: note: candidate: 'template<class ExecutionContext> boost::asio::basic_stream_socket<Protocol, Executor>::basic_stream_socket(ExecutionContext&,  
 const protocol_type&, typename std::enable_if<std::is_convertible<ExecutionContext&, boost::asio::execution_context&>::value>::type*) [with ExecutionContext = ExecutionContext; Protocol = boost::asio::ip::tcp; Executor = boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>]'  
6.793   147 |   basic_stream_socket(ExecutionContext& context, const protocol_type& protocol,  
6.793       |   ^~~~~~~~~~~~~~~~~~~  
6.793 /usr/include/boost/asio/basic_stream_socket.hpp:147:3: note:   template argument deduction/substitution failed:  
6.793 In file included from /usr/include/boost/beast/core/basic_stream.hpp:1458,  
6.793                  from /usr/include/boost/beast/core.hpp:16,  
6.793                  from proxy_websocket.h:2,  
6.793                  from proxy_websocket.cpp:1:  
6.793 /usr/include/boost/beast/core/impl/basic_stream.hpp:38:17: note:   candidate expects 3 arguments, 1 provided  
6.793    38 |     , timer(ex())  
6.793       |                 ^  
6.793 In file included from /usr/include/boost/asio/basic_socket_streambuf.hpp:25,  
6.793                  from /usr/include/boost/asio/basic_socket_iostream.hpp:24,  
6.793                  from /usr/include/boost/asio.hpp:33,  
6.793                  from server.h:4,  
6.793                  from proxy_websocket.h:1,  
6.793                  from proxy_websocket.cpp:1:  
6.793 /usr/include/boost/asio/basic_stream_socket.hpp:128:3: note: candidate: 'boost::asio::basic_stream_socket<Protocol, Executor>::basic_stream_socket(const executor_type&, const protocol_type&) [with P  
rotocol = boost::asio::ip::tcp; Executor = boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>; boost::asio::basic_stream_socket<Protocol, Executor>::executor_type = boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>; boost::asio::basic_stream_socket<Protocol, Executor>::protocol_type = boost::asio::ip::tcp]'  
6.793   128 |   basic_stream_socket(const executor_type& ex, const protocol_type& protocol)  
6.793       |   ^~~~~~~~~~~~~~~~~~~  
6.793 /usr/include/boost/asio/basic_stream_socket.hpp:128:3: note:   candidate expects 2 arguments, 1 provided  
6.793 /usr/include/boost/asio/basic_stream_socket.hpp:108:12: note: candidate: 'template<class ExecutionContext> boost::asio::basic_stream_socket<Protocol, Executor>::basic_stream_socket(ExecutionContext&  
, typename std::enable_if<std::is_convertible<ExecutionContext&, boost::asio::execution_context&>::value>::type*) [with ExecutionContext = ExecutionContext; Protocol = boost::asio::ip::tcp; Executor = boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>]'  
6.793   108 |   explicit basic_stream_socket(ExecutionContext& context,  
6.793       |            ^~~~~~~~~~~~~~~~~~~  
6.793 /usr/include/boost/asio/basic_stream_socket.hpp:108:12: note:   template argument deduction/substitution failed:  
6.793 /usr/include/boost/asio/basic_stream_socket.hpp: In substitution of 'template<class ExecutionContext> boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::io_context::basic_executor_t  
ype<std::allocator<void>, 0> >::basic_stream_socket(ExecutionContext&, typename std::enable_if<std::is_convertible<ExecutionContext&, boost::asio::execution_context&>::value>::type*) [with ExecutionContext = boost::asio::basic_stream_socket<boost::asio::ip::tcp>]':  
6.793 /usr/include/boost/beast/core/impl/basic_stream.hpp:38:17:   required from 'boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::impl_type::imp  
l_type(std::false_type, Args&& ...) [with Args = {boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::execution::any_executor<boost::asio::execution::context_as_t<boost::asio::execution_co  
ntext&>, boost::asio::execution::detail::blocking::never_t<0>, boost::asio::execution::prefer_only<boost::asio::execution::detail::blocking::possibly_t<0> >, boost::asio::execution::prefer_only<boost::asi  
o::execution::detail::outstanding_work::tracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::untracked_t<0> >, boost::asio::execution::prefer_only<boost::a  
sio::execution::detail::relationship::fork_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::continuation_t<0> > > >}; Protocol = boost::asio::ip::tcp; Executor = boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>; RatePolicy = boost::beast::unlimited_rate_policy; std::false_type = std::integral_constant<bool, false>]'  
6.793 /usr/include/boost/smart_ptr/make_shared_object.hpp:256:5:   required from 'typename boost::detail::sp_if_not_array<T>::type boost::make_shared(Args&& ...) [with T = boost::beast::basic_stream<boost  
::asio::ip::tcp, boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>, boost::beast::unlimited_rate_policy>::impl_type; Args = {std::integral_constant<bool, false>, boost::asio::basic_str  
eam_socket<boost::asio::ip::tcp, boost::asio::execution::any_executor<boost::asio::execution::context_as_t<boost::asio::execution_context&>, boost::asio::execution::detail::blocking::never_t<0>, boost::as  
io::execution::prefer_only<boost::asio::execution::detail::blocking::possibly_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::tracked_t<0> >, boost::asio::exe  
cution::prefer_only<boost::asio::execution::detail::outstanding_work::untracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::fork_t<0> >, boost::asio::executio  
n::prefer_only<boost::asio::execution::detail::relationship::continuation_t<0> > > >}; typename boost::detail::sp_if_not_array<T>::type = boost::shared_ptr<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>, boost::beast::unlimited_rate_policy>::impl_type>]'  
6.793 /usr/include/boost/beast/core/impl/basic_stream.hpp:658:42:   required from 'boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::basic_stream(  
Arg0&&, Args&& ...) [with Arg0 = boost::asio::basic_stream_socket<boost::asio::ip::tcp>; Args = {}; <template-parameter-2-3> = void; Protocol = boost::asio::ip::tcp; Executor = boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>; RatePolicy = boost::beast::unlimited_rate_policy]'  
6.793 proxy_websocket.h:34:158:   required from here  
6.793 /usr/include/boost/asio/basic_stream_socket.hpp:108:12: error: no type named 'type' in 'struct std::enable_if<false, void>'  
6.793 /usr/include/boost/beast/core/impl/basic_stream.hpp: In instantiation of 'boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::impl_type::impl_  
type(std::false_type, Args&& ...) [with Args = {boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::execution::any_executor<boost::asio::execution::context_as_t<boost::asio::execution_cont  
ext&>, boost::asio::execution::detail::blocking::never_t<0>, boost::asio::execution::prefer_only<boost::asio::execution::detail::blocking::possibly_t<0> >, boost::asio::execution::prefer_only<boost::asio:  
:execution::detail::outstanding_work::tracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::untracked_t<0> >, boost::asio::execution::prefer_only<boost::asi  
o::execution::detail::relationship::fork_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::continuation_t<0> > > >}; Protocol = boost::asio::ip::tcp; Executor = boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>; RatePolicy = boost::beast::unlimited_rate_policy; std::false_type = std::integral_constant<bool, false>]':  
6.793 /usr/include/boost/smart_ptr/make_shared_object.hpp:256:5:   required from 'typename boost::detail::sp_if_not_array<T>::type boost::make_shared(Args&& ...) [with T = boost::beast::basic_stream<boost  
::asio::ip::tcp, boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>, boost::beast::unlimited_rate_policy>::impl_type; Args = {std::integral_constant<bool, false>, boost::asio::basic_str  
eam_socket<boost::asio::ip::tcp, boost::asio::execution::any_executor<boost::asio::execution::context_as_t<boost::asio::execution_context&>, boost::asio::execution::detail::blocking::never_t<0>, boost::as  
io::execution::prefer_only<boost::asio::execution::detail::blocking::possibly_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::tracked_t<0> >, boost::asio::exe  
cution::prefer_only<boost::asio::execution::detail::outstanding_work::untracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::fork_t<0> >, boost::asio::executio  
n::prefer_only<boost::asio::execution::detail::relationship::continuation_t<0> > > >}; typename boost::detail::sp_if_not_array<T>::type = boost::shared_ptr<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>, boost::beast::unlimited_rate_policy>::impl_type>]'  
6.793 /usr/include/boost/beast/core/impl/basic_stream.hpp:658:42:   required from 'boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::basic_stream(  
Arg0&&, Args&& ...) [with Arg0 = boost::asio::basic_stream_socket<boost::asio::ip::tcp>; Args = {}; <template-parameter-2-3> = void; Protocol = boost::asio::ip::tcp; Executor = boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>; RatePolicy = boost::beast::unlimited_rate_policy]'  
6.793 proxy_websocket.h:34:158:   required from here  
6.793 /usr/include/boost/asio/basic_stream_socket.hpp:92:12: note: candidate: 'boost::asio::basic_stream_socket<Protocol, Executor>::basic_stream_socket(const executor_type&) [with Protocol = boost::asio:  
:ip::tcp; Executor = boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>; boost::asio::basic_stream_socket<Protocol, Executor>::executor_type = boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>]'  
6.793    92 |   explicit basic_stream_socket(const executor_type& ex)  
6.793       |            ^~~~~~~~~~~~~~~~~~~  
6.793 /usr/include/boost/asio/basic_stream_socket.hpp:92:53: note:   no known conversion for argument 1 from 'boost::asio::basic_stream_socket<boost::asio::ip::tcp>' to 'const executor_type&' {aka 'const boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>&'}  
6.793    92 |   explicit basic_stream_socket(const executor_type& ex)  
6.793       |                                ~~~~~~~~~~~~~~~~~~~~~^~  
```

---

## Comment 3

> Username: ashtum  
> Created at: 2024-06-24 09:10:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2892#issuecomment-2186000616  

> @ashtum My CPU usage just became 100%, that's how I've noticed it. It's single core cheap VPS but still. Currently using -Ofast, will try to compile also without debug symbols (-DNDEBUG).  
  
100% CPU usage for 5k messages/second is unusual; you can typically expect around 50k on a single core (assuming no other tasks are consuming significant processing power). I believe compiling without debug symbols won't help much. What CPU usage do you observe when running it on your desktop?  
  
> Your code suggestion is not friend with my code.  
  
You need to use concrete executor types for `tcp::acceptor` and `tcp::socket` as well:  
```  
using acceptor_t = tcp::acceptor::rebind_executor<asio::io_context::executor_type>::other;  
using socket_t = tcp::socket::rebind_executor<asio::io_context::executor_type>::other;  
```  
However, as I mentioned, this change won't make your code significantly faster, expect around a 5% improvement at most.

---

## Comment 4

> Username: gunzino  
> Created at: 2024-08-07 07:26:21 UTC  
> Updated at: 2024-08-07 07:28:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2892#issuecomment-2272802308  

@ashtum   
I've done some more tests. After I start the app and connections are created (no matter about amount) everything works normally, CPU usage is very low even with 500 connections.   
  
After about 12 hours of running the CPU usage of the executors goes to 100%. Also, I see that RAM usage has increased to about 10x.  Is it possible there is an issue somewhere in my code? In the callback functions?

---

## Comment 5

> Username: ashtum  
> Created at: 2024-08-07 07:32:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2892#issuecomment-2272812575  

Are you using a multi-threaded executor? I'm afraid you might have unintentionally created an infinite loop somewhere in your code, probably by not checking the error code of an operation and retrying it.

---

## Comment 6

> Username: gunzino  
> Created at: 2024-08-07 07:42:23 UTC  
> Updated at: 2024-08-07 15:42:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2892#issuecomment-2272828873  

@ashtum   
  
Thanks for the fast reply!! That may explain the case...I will post more info soon  
  
EDIT: Problem solved. You perfectly hit the spot.
