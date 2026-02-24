# #790 - async websocket server example [Closed]

> Username: final-turn  
> Created at: 2017-09-21 15:40:35 UTC  
> Updated at: 2017-09-25 18:35:35 UTC  
> Closed at: 2017-09-25 18:35:35 UTC  
> Url: https://github.com/boostorg/beast/issues/790  

I am unable to compile the example program located here:  
>beast/example/websocket/server/async/websocket_server_async.cpp  
  
I am using version 117  
  
I am using the g++ compiler(version 6.4.0) and running this command:  
`g++ -std=c++11 -o server server.cpp -lpthread -lboost_system`  
  
For what its worth, I _am_ able to compile and run the synchronous example with no problems  
  
<details>  
<summary>compiler output ( it is massive )</summary>  
  
<code>  
In file included from /usr/include/boost/asio/detail/wrapped_handler.hpp:18:0,  
                 from /usr/include/boost/asio/io_service.hpp:24,  
                 from /usr/include/boost/beast/core/detail/type_traits.hpp:15,  
                 from /usr/include/boost/beast/core/type_traits.hpp:15,  
                 from /usr/include/boost/beast/core/async_result.hpp:14,  
                 from /usr/include/boost/beast/core.hpp:15,  
                 from server.cpp:16:  
  
/usr/include/boost/asio/detail/bind_handler.hpp: In instantiation of ‘void boost::asio::detail::binder1<Handler, Arg1>::operator()() [with Handler = std::_Bind<std::_Mem_fn<void (session::*)(boost::system::error_code, long unsigned int)>(std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>)>; Arg1 = boost::system::error_code]’:  
/usr/include/boost/asio/handler_invoke_hook.hpp:69:11:   required from ‘void boost::asio::asio_handler_invoke(Function&, ...) [with Function = boost::asio::detail::binder1<std::_Bind<std::_Mem_fn<void (session::*)(boost::system::error_code, long unsigned int)>(std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>)>, boost::system::error_code>]’  
/usr/include/boost/asio/detail/handler_invoke_helpers.hpp:37:22:   required from ‘void boost_asio_handler_invoke_helpers::invoke(Function&, Context&) [with Function = boost::asio::detail::binder1<std::_Bind<std::_Mem_fn<void (session::*)(boost::system::error_code, long unsigned int)>(std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>)>, boost::system::error_code>; Context = std::_Bind<std::_Mem_fn<void (session::*)(boost::system::error_code, long unsigned int)>(std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>)>]’  
/usr/include/boost/asio/detail/bind_handler.hpp:88:44:   required from ‘void boost::asio::detail::asio_handler_invoke(Function&, boost::asio::detail::binder1<Handler, Arg1>*) [with Function = boost::asio::detail::binder1<std::_Bind<std::_Mem_fn<void (session::*)(boost::system::error_code, long unsigned int)>(std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>)>, boost::system::error_code>; Handler = std::_Bind<std::_Mem_fn<void (session::*)(boost::system::error_code, long unsigned int)>(std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>)>; Arg1 = boost::system::error_code]’  
/usr/include/boost/asio/detail/handler_invoke_helpers.hpp:37:22:   required from ‘void boost_asio_handler_invoke_helpers::invoke(Function&, Context&) [with Function = boost::asio::detail::binder1<std::_Bind<std::_Mem_fn<void (session::*)(boost::system::error_code, long unsigned int)>(std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>)>, boost::system::error_code>; Context = boost::asio::detail::binder1<std::_Bind<std::_Mem_fn<void (session::*)(boost::system::error_code, long unsigned int)>(std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>)>, boost::system::error_code>]’  
/usr/include/boost/asio/detail/impl/strand_service.hpp:62:46:   required from ‘void boost::asio::detail::strand_service::dispatch(boost::asio::detail::strand_service::strand_impl*&, Handler&) [with Handler = boost::asio::detail::binder1<std::_Bind<std::_Mem_fn<void (session::*)(boost::system::error_code, long unsigned int)>(std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>)>, boost::system::error_code>; boost::asio::detail::strand_service::implementation_type = boost::asio::detail::strand_service::strand_impl*]’  
/usr/include/boost/asio/strand.hpp:158:5:   required from ‘typename boost::asio::async_result<typename boost::asio::handler_type<CompletionHandler, void()>::type>::type boost::asio::io_service::strand::dispatch(CompletionHandler&&) [with CompletionHandler = boost::asio::detail::binder1<std::_Bind<std::_Mem_fn<void (session::*)(boost::system::error_code, long unsigned int)>(std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>)>, boost::system::error_code>; typename boost::asio::async_result<typename boost::asio::handler_type<CompletionHandler, void()>::type>::type = void]’  
/usr/include/boost/asio/detail/wrapped_handler.hpp:87:5:   required from ‘void boost::asio::detail::wrapped_handler<Dispatcher, Handler, IsContinuation>::operator()(const Arg1&) [with Arg1 = boost::system::error_code; Dispatcher = boost::asio::io_service::strand; Handler = std::_Bind<std::_Mem_fn<void (session::*)(boost::system::error_code, long unsigned int)>(std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>)>; IsContinuation = boost::asio::detail::is_continuation_if_running]’  
/usr/include/boost/beast/websocket/impl/write.ipp:486:9:   required from ‘void boost::beast::websocket::stream<NextLayer>::write_some_op< <template-parameter-2-1>, <template-parameter-2-2> >::operator()(boost::beast::error_code, std::size_t, bool) [with Buffers = boost::beast::basic_multi_buffer<std::allocator<char> >::const_buffers_type; Handler = boost::asio::detail::wrapped_handler<boost::asio::io_service::strand, std::_Bind<std::_Mem_fn<void (session::*)(boost::system::error_code, long unsigned int)>(std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>)>, boost::asio::detail::is_continuation_if_running>; NextLayer = boost::asio::basic_stream_socket<boost::asio::ip::tcp>; boost::beast::error_code = boost::system::error_code; std::size_t = long unsigned int]’  
/usr/include/boost/beast/websocket/impl/write.ipp:786:29:   required from ‘boost::beast::async_return_type<ReadHandler, void(boost::system::error_code)> boost::beast::websocket::stream<NextLayer>::async_write(const ConstBufferSequence&, WriteHandler&&) [with ConstBufferSequence = boost::beast::basic_multi_buffer<std::allocator<char> >::const_buffers_type; WriteHandler = boost::asio::detail::wrapped_handler<boost::asio::io_service::strand, std::_Bind<std::_Mem_fn<void (session::*)(boost::system::error_code, long unsigned int)>(std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>)>, boost::asio::detail::is_continuation_if_running>; NextLayer = boost::asio::basic_stream_socket<boost::asio::ip::tcp>; boost::beast::async_return_type<ReadHandler, void(boost::system::error_code)> = void]’  
server.cpp:114:40:   required from here  
/usr/include/boost/asio/detail/bind_handler.hpp:47:5: error: no match for call to ‘(std::_Bind<std::_Mem_fn<void (session::*)(boost::system::error_code, long unsigned int)>(std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>)>) (const boost::system::error_code&)’  
     handler_(static_cast<const Arg1&>(arg1_));  
     ^~~~~~~~  
In file included from /usr/include/boost/system/error_code.hpp:22:0,  
                 from /usr/include/boost/beast/core/error.hpp:14,  
                 from /usr/include/boost/beast/core/detail/type_traits.hpp:13,  
                 from /usr/include/boost/beast/core/type_traits.hpp:15,  
                 from /usr/include/boost/beast/core/async_result.hpp:14,  
                 from /usr/include/boost/beast/core.hpp:15,  
                 from server.cpp:16:  
/usr/lib/gcc/x86_64-pc-cygwin/6.4.0/include/c++/functional:989:2: note: candidate: template<class ... _Args, class _Result> _Result std::_Bind<_Functor(_Bound_args ...)>::operator()(_Args&& ...) [with _Args = {_Args ...}; _Result = _Result; _Functor = std::_Mem_fn<void (session::*)(boost::system::error_code, long unsigned int)>; _Bound_args = {std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>}]  
  operator()(_Args&&... __args)  
  ^~~~~~~~  
/usr/lib/gcc/x86_64-pc-cygwin/6.4.0/include/c++/functional:989:2: note:   template argument deduction/substitution failed:  
/usr/lib/gcc/x86_64-pc-cygwin/6.4.0/include/c++/functional:986:26: error: no match for call to ‘(std::_Mu<std::_Placeholder<2>, false, true>) (std::_Placeholder<2>&, std::tuple<const boost::system::error_code&>&)’  
        _Mu<_Bound_args>()( std::declval<_Bound_args&>(),  
        ~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
       std::declval<tuple<_Args...>&>() )... ) )>  
       ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/lib/gcc/x86_64-pc-cygwin/6.4.0/include/c++/functional:831:2: note: candidate: template<class _Tuple> std::_Safe_tuple_element_t<(std::is_placeholder<_Tp>::value - 1), _Tuple>&& std::_Mu<_Arg, false, true>::operator()(const volatile _Arg&, _Tuple&) const volatile [with _Tuple = _Tuple; _Arg = std::_Placeholder<2>]  
  operator()(const volatile _Arg&, _Tuple& __tuple) const volatile  
  ^~~~~~~~  
/usr/lib/gcc/x86_64-pc-cygwin/6.4.0/include/c++/functional:831:2: note:   template argument deduction/substitution failed:  
/usr/lib/gcc/x86_64-pc-cygwin/6.4.0/include/c++/functional:1003:2: note: candidate: template<class ... _Args, class _Result> _Result std::_Bind<_Functor(_Bound_args ...)>::operator()(_Args&& ...) const [with _Args = {_Args ...}; _Result = _Result; _Functor = std::_Mem_fn<void (session::*)(boost::system::error_code, long unsigned int)>; _Bound_args = {std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>}]  
  operator()(_Args&&... __args) const  
  ^~~~~~~~  
/usr/lib/gcc/x86_64-pc-cygwin/6.4.0/include/c++/functional:1003:2: note:   template argument deduction/substitution failed:  
/usr/lib/gcc/x86_64-pc-cygwin/6.4.0/include/c++/functional:1000:26: error: no match for call to ‘(std::_Mu<std::_Placeholder<2>, false, true>) (const std::_Placeholder<2>&, std::tuple<const boost::system::error_code&>&)’  
        _Mu<_Bound_args>()( std::declval<const _Bound_args&>(),  
        ~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
       std::declval<tuple<_Args...>&>() )... ) )>  
       ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/lib/gcc/x86_64-pc-cygwin/6.4.0/include/c++/functional:831:2: note: candidate: template<class _Tuple> std::_Safe_tuple_element_t<(std::is_placeholder<_Tp>::value - 1), _Tuple>&& std::_Mu<_Arg, false, true>::operator()(const volatile _Arg&, _Tuple&) const volatile [with _Tuple = _Tuple; _Arg = std::_Placeholder<2>]  
  operator()(const volatile _Arg&, _Tuple& __tuple) const volatile  
  ^~~~~~~~  
/usr/lib/gcc/x86_64-pc-cygwin/6.4.0/include/c++/functional:831:2: note:   template argument deduction/substitution failed:  
/usr/lib/gcc/x86_64-pc-cygwin/6.4.0/include/c++/functional:1017:2: note: candidate: template<class ... _Args, class _Result> _Result std::_Bind<_Functor(_Bound_args ...)>::operator()(_Args&& ...) volatile [with _Args = {_Args ...}; _Result = _Result; _Functor = std::_Mem_fn<void (session::*)(boost::system::error_code, long unsigned int)>; _Bound_args = {std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>}]  
  operator()(_Args&&... __args) volatile  
  ^~~~~~~~  
/usr/lib/gcc/x86_64-pc-cygwin/6.4.0/include/c++/functional:1017:2: note:   template argument deduction/substitution failed:  
/usr/lib/gcc/x86_64-pc-cygwin/6.4.0/include/c++/functional:1014:26: error: no match for call to ‘(std::_Mu<std::_Placeholder<2>, false, true>) (volatile std::_Placeholder<2>&, std::tuple<const boost::system::error_code&>&)’  
        _Mu<_Bound_args>()( std::declval<volatile _Bound_args&>(),  
        ~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
       std::declval<tuple<_Args...>&>() )... ) )>  
       ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/lib/gcc/x86_64-pc-cygwin/6.4.0/include/c++/functional:831:2: note: candidate: template<class _Tuple> std::_Safe_tuple_element_t<(std::is_placeholder<_Tp>::value - 1), _Tuple>&& std::_Mu<_Arg, false, true>::operator()(const volatile _Arg&, _Tuple&) const volatile [with _Tuple = _Tuple; _Arg = std::_Placeholder<2>]  
  operator()(const volatile _Arg&, _Tuple& __tuple) const volatile  
  ^~~~~~~~  
/usr/lib/gcc/x86_64-pc-cygwin/6.4.0/include/c++/functional:831:2: note:   template argument deduction/substitution failed:  
/usr/lib/gcc/x86_64-pc-cygwin/6.4.0/include/c++/functional:1031:2: note: candidate: template<class ... _Args, class _Result> _Result std::_Bind<_Functor(_Bound_args ...)>::operator()(_Args&& ...) const volatile [with _Args = {_Args ...}; _Result = _Result; _Functor = std::_Mem_fn<void (session::*)(boost::system::error_code, long unsigned int)>; _Bound_args = {std::shared_ptr<session>, std::_Placeholder<1>, std::_Placeholder<2>}]  
  operator()(_Args&&... __args) const volatile  
  ^~~~~~~~  
/usr/lib/gcc/x86_64-pc-cygwin/6.4.0/include/c++/functional:1031:2: note:   template argument deduction/substitution failed:  
/usr/lib/gcc/x86_64-pc-cygwin/6.4.0/include/c++/functional:1028:26: error: no match for call to ‘(std::_Mu<std::_Placeholder<2>, false, true>) (const volatile std::_Placeholder<2>&, std::tuple<const boost::system::error_code&>&)’  
        _Mu<_Bound_args>()( std::declval<const volatile _Bound_args&>(),  
        ~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
       std::declval<tuple<_Args...>&>() )... ) )>  
       ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/lib/gcc/x86_64-pc-cygwin/6.4.0/include/c++/functional:831:2: note: candidate: template<class _Tuple> std::_Safe_tuple_element_t<(std::is_placeholder<_Tp>::value - 1), _Tuple>&& std::_Mu<_Arg, false, true>::operator()(const volatile _Arg&, _Tuple&) const volatile [with _Tuple = _Tuple; _Arg = std::_Placeholder<2>]  
  operator()(const volatile _Arg&, _Tuple& __tuple) const volatile  
  ^~~~~~~~  
/usr/lib/gcc/x86_64-pc-cygwin/6.4.0/include/c++/functional:831:2: note:   template argument deduction/substitution failed:  
</code>  
</details>

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-09-21 15:58:03 UTC  
> Url: https://github.com/boostorg/beast/issues/790#issuecomment-331201570  

What is Cygwin?

---

## Comment 2

> Username: final-turn  
> Created at: 2017-09-21 16:07:01 UTC  
> Url: https://github.com/boostorg/beast/issues/790#issuecomment-331204324  

> Cygwin is a large collection of GNU and Open Source tools which provide functionality similar to a Linux distribution on Windows.  
  
Although, I get a similar compile error when I try compiling on a Debian VM so I dont know if Cygwin plays a significant role in why I'm having issues

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-09-21 17:44:08 UTC  
> Url: https://github.com/boostorg/beast/issues/790#issuecomment-331230283  

What version of Boost are you using?

---

## Comment 4

> Username: final-turn  
> Created at: 2017-09-21 18:35:03 UTC  
> Url: https://github.com/boostorg/beast/issues/790#issuecomment-331243929  

It looks like I'm using version 1.6.2

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-09-21 18:49:07 UTC  
> Url: https://github.com/boostorg/beast/issues/790#issuecomment-331247599  

Hmm... there is no Boost 1.6.2. Do you mean 1.62.0?

---

## Comment 6

> Username: final-turn  
> Created at: 2017-09-21 18:50:44 UTC  
> Url: https://github.com/boostorg/beast/issues/790#issuecomment-331247996  

Yeah my bad!

---

## Comment 7

> Username: keith-bennett-gbg  
> Created at: 2017-09-21 21:30:44 UTC  
> Url: https://github.com/boostorg/beast/issues/790#issuecomment-331287765  

```  
server.cpp:114:40: required from here /usr/include/boost/asio/detail/bind_handler.hpp:47:5:   
error: no match for call to ‘(std::_Bind<std::_Mem_fn<void (session::)(boost::system::error_code, long unsigned int)>(std::shared_ptr, std::Placeholder<1>, std::Placeholder<2>)>) (const boost::system::error_code&)’ handler(static_cast<const Arg1&>(arg1));  
```  
looks like your `std::bind` doesn't match a handler's function signature requirement

---

## Comment 8

> Username: keith-bennett-gbg  
> Created at: 2017-09-21 21:39:24 UTC  
> Url: https://github.com/boostorg/beast/issues/790#issuecomment-331289604  

> I am unable to compile the example program located here:  
> > beast/example/websocket/server/async/websocket_server_async.cpp  
>  
> I am using version 117  
>  
> I am using the g++ compiler(version 6.4.0) and running this command:  
> `g++ -std=c++11 -o server server.cpp -lpthread -lboost_system`  
  
You appear to be compiling `server.cpp`. I do not see any such file in the repository named `server.cpp`. I can compile `websocket_server_async.cpp` just fine on Ubuntu 16.04 (Debian-derived, of course) with GCC 6.3.1 on commit 0f5ea371c18a6797f4623a61f159326428c01400 (117).

---

## Comment 9

> Username: vinniefalco  
> Created at: 2017-09-21 21:45:56 UTC  
> Url: https://github.com/boostorg/beast/issues/790#issuecomment-331290972  

Your command line says you are compiling "server.cpp", but that is not one of the example sources in Beast. Where is the file server.cpp? Can you provide it?

---

## Comment 10

> Username: final-turn  
> Created at: 2017-09-21 21:51:30 UTC  
> Url: https://github.com/boostorg/beast/issues/790#issuecomment-331292114  

That is just what I called it - it's the same file though.  
  
Is the version of boost I am using incompatible with the async example? Should I look into updating it?  
  
I used apt-get install libboost-all-dev and thats how i got 1.62.0 and I was able to run the synchronous example off of that just fine so I feel like the version isn't really a problem but I'm relatively new to boost as a whole so I'm not sure.

---

## Comment 11

> Username: vinniefalco  
> Created at: 2017-09-21 22:51:45 UTC  
> Url: https://github.com/boostorg/beast/issues/790#issuecomment-331303328  

At this point I am wondering if it is a problem with your version of the standard library. Does your `std::bind` work with `std::shared_ptr`? Try compiling this program:  
```  
#include <functional>  
#include <memory>  
struct S  
{  
    void foo() { }  
};  
  
int main()  
{  
    auto sp = std::make_shared<S>();  
    std::bind(&S::foo, sp);  
}  
```  
  
Here's a working version: https://wandbox.org/permlink/rDDSxvcvuHwSQjlr

---

## Comment 12

> Username: final-turn  
> Created at: 2017-09-21 23:05:00 UTC  
> Url: https://github.com/boostorg/beast/issues/790#issuecomment-331305505  

It compiles without error

---

## Comment 13

> Username: final-turn  
> Created at: 2017-09-21 23:44:33 UTC  
> Url: https://github.com/boostorg/beast/issues/790#issuecomment-331311372  

1. Okay so I uninstalled boost version 1.62.0 from my remove Debian VM.  
2. Then I followed the instructions here to get the latest version of boost:  
`http://www.boost.org/doc/libs/1_65_1/more/getting_started/unix-variants.html`  
and was able to compile the example program successfully.  
3. Then I copied over version 117 beast into boost and when I tried to compile I got this error  
 `/usr/bin/ld: cannot find -lboost_system`  
  
4. So then I used `apt-get` to install that single library and now the example compiles and runs as expected  
  
I don't really know why it wasn't working from a complete installation of 1.62.0 but I might have over looked something 😅

---

## Comment 14

> Username: vinniefalco  
> Created at: 2017-09-22 00:53:20 UTC  
> Updated at: 2017-09-22 00:53:37 UTC  
> Url: https://github.com/boostorg/beast/issues/790#issuecomment-331320727  

phew...! glad to hear it :) I have no idea why it wasn't working. That error message sure was hard to read...
