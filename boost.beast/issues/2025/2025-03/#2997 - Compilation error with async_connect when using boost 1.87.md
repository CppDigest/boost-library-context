# #2997 - Compilation error with async_connect when using boost 1.87 [Closed]

> Username: a-soll  
> Created at: 2025-03-27 23:32:50 UTC  
> Updated at: 2025-04-15 12:55:48 UTC  
> Closed at: 2025-04-15 12:55:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2997  

```cpp  
 beast::get_lowest_layer(this->_socket)  
.async_connect(  
    results, beast::bind_front_handler(&client::_on_connected, shared_from_this())  
);  
```  
When using boost 1.87, the above function call fails with the below compilation error:  
  
```  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__functional/mem_fn.h:43:12: error: attempt to use a deleted function  
   43 |     return std::__invoke(__f_, std::forward<_ArgTypes>(__args)...);  
      |            ^  
/Users/adam/Library/Caches/CocoaPods/Pods/Release/boost-iosx/1.87.0.4-9f252/frameworks/Headers/boost/beast/core/detail/bind_handler.hpp:204:9: note: in instantiation of function template specialization 'std::__mem_fn<void (kv::chat::irc::client::*)(const boost::system::error_code &)>::operator()<std::shared_ptr<kv::chat::irc::client>, boost::system::error_code &, const boost::asio::ip::basic_endpoint<boost::asio::ip::tcp> &>' requested here  
  204 |         std::mem_fn(h_)(  
      |         ^  
/Users/adam/Library/Caches/CocoaPods/Pods/Release/boost-iosx/1.87.0.4-9f252/frameworks/Headers/boost/beast/core/detail/bind_handler.hpp:227:9: note: in instantiation of function template specialization 'boost::beast::detail::bind_front_wrapper<void (kv::chat::irc::client::*)(const boost::system::error_code &), std::shared_ptr<kv::chat::irc::client>>::invoke<0UL, boost::system::error_code &, const boost::asio::ip::basic_endpoint<boost::asio::ip::tcp> &>' requested here  
  227 |         invoke(  
      |         ^  
/Users/adam/Library/Caches/CocoaPods/Pods/Release/boost-iosx/1.87.0.4-9f252/frameworks/Headers/boost/beast/core/async_base.hpp:458:9: note: in instantiation of function template specialization 'boost::beast::detail::bind_front_wrapper<void (kv::chat::irc::client::*)(const boost::system::error_code &), std::shared_ptr<kv::chat::irc::client>>::operator()<boost::system::error_code &, const boost::asio::ip::basic_endpoint<boost::asio::ip::tcp> &>' requested here  
  458 |         h_(std::forward<Args>(args)...);  
      |         ^  
/Users/adam/Library/Caches/CocoaPods/Pods/Release/boost-iosx/1.87.0.4-9f252/frameworks/Headers/boost/beast/core/impl/basic_stream.hpp:604:15: note: in instantiation of function template specialization 'boost::beast::async_base<boost::beast::detail::bind_front_wrapper<void (kv::chat::irc::client::*)(const boost::system::error_code &), std::shared_ptr<kv::chat::irc::client>>, boost::asio::any_io_executor>::complete_now<boost::system::error_code &, const boost::asio::ip::basic_endpoint<boost::asio::ip::tcp> &>' requested here  
  604 |         this->complete_now(ec, std::forward<Args>(args)...);  
      |               ^  
/Users/adam/Library/Caches/CocoaPods/Pods/Release/boost-iosx/1.87.0.4-9f252/frameworks/Headers/boost/asio/impl/connect.hpp:354:9: note: in instantiation of function template specialization 'boost::beast::basic_stream<boost::asio::ip::tcp>::ops::connect_op<boost::beast::detail::bind_front_wrapper<void (kv::chat::irc::client::*)(const boost::system::error_code &), std::shared_ptr<kv::chat::irc::client>>>::operator()<const boost::asio::ip::basic_endpoint<boost::asio::ip::tcp> &>' requested here  
  354 |         static_cast<RangeConnectHandler&&>(handler_)(  
      |         ^  
/Users/adam/Library/Caches/CocoaPods/Pods/Release/boost-iosx/1.87.0.4-9f252/frameworks/Headers/boost/asio/impl/connect.hpp:290:13: note: (skipping 5 contexts in backtrace; use -ftemplate-backtrace-limit=0 to see all)  
  290 |       this->process(ec, start,  
      |             ^  
/Users/adam/Library/Caches/CocoaPods/Pods/Release/boost-iosx/1.87.0.4-9f252/frameworks/Headers/boost/beast/core/impl/basic_stream.hpp:531:14: note: in instantiation of function template specialization 'boost::asio::async_connect<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>, boost::beast::detail::any_endpoint, boost::beast::basic_stream<boost::asio::ip::tcp>::ops::connect_op<boost::beast::detail::bind_front_wrapper<void (kv::chat::irc::client::*)(const boost::system::error_code &), std::shared_ptr<kv::chat::irc::client>>>>' requested here  
  531 |         net::async_connect(impl_->socket,  
      |              ^  
/Users/adam/Library/Caches/CocoaPods/Pods/Release/boost-iosx/1.87.0.4-9f252/frameworks/Headers/boost/beast/core/impl/basic_stream.hpp:741:9: note: in instantiation of function template specialization 'boost::beast::basic_stream<boost::asio::ip::tcp>::ops::connect_op<boost::beast::detail::bind_front_wrapper<void (kv::chat::irc::client::*)(const boost::system::error_code &), std::shared_ptr<kv::chat::irc::client>>>::connect_op<boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>, boost::beast::detail::any_endpoint, boost::beast::detail::bind_front_wrapper<void (kv::chat::irc::client::*)(const boost::system::error_code &), std::shared_ptr<kv::chat::irc::client>>>' requested here  
  741 |         connect_op<typename std::decay<RangeConnectHandler>::type>(  
      |         ^  
/Users/adam/Library/Caches/CocoaPods/Pods/Release/boost-iosx/1.87.0.4-9f252/frameworks/Headers/boost/asio/async_result.hpp:329:5: note: in instantiation of function template specialization 'boost::beast::basic_stream<boost::asio::ip::tcp>::ops::run_connect_range_op::operator()<boost::beast::detail::bind_front_wrapper<void (kv::chat::irc::client::*)(const boost::system::error_code &), std::shared_ptr<kv::chat::irc::client>>, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>, boost::beast::detail::any_endpoint>' requested here  
  329 |     static_cast<Initiation&&>(initiation)(  
      |     ^  
/Users/adam/Library/Caches/CocoaPods/Pods/Release/boost-iosx/1.87.0.4-9f252/frameworks/Headers/boost/asio/async_result.hpp:629:65: note: in instantiation of function template specialization 'boost::asio::detail::completion_handler_async_result<boost::beast::detail::bind_front_wrapper<void (kv::chat::irc::client::*)(const boost::system::error_code &), std::shared_ptr<kv::chat::irc::client>>, void (boost::system::error_code, boost::asio::ip::basic_endpoint<boost::asio::ip::tcp>)>::initiate<boost::beast::basic_stream<boost::asio::ip::tcp>::ops::run_connect_range_op, boost::beast::detail::bind_front_wrapper<void (kv::chat::irc::client::*)(const boost::system::error_code &), std::shared_ptr<kv::chat::irc::client>>, const boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp> &, boost::beast::detail::any_endpoint>' requested here  
  629 |   return async_result<decay_t<CompletionToken>, Signatures...>::initiate(  
      |                                                                 ^  
/Users/adam/Library/Caches/CocoaPods/Pods/Release/boost-iosx/1.87.0.4-9f252/frameworks/Headers/boost/beast/core/impl/basic_stream.hpp:961:17: note: in instantiation of function template specialization 'boost::asio::async_initiate<boost::beast::detail::bind_front_wrapper<void (kv::chat::irc::client::*)(const boost::system::error_code &), std::shared_ptr<kv::chat::irc::client>>, void (boost::system::error_code, boost::asio::ip::basic_endpoint<boost::asio::ip::tcp>), boost::beast::basic_stream<boost::asio::ip::tcp>::ops::run_connect_range_op, const boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp> &, boost::beast::detail::any_endpoint>' requested here  
  961 |     return net::async_initiate<  
      |                 ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/__type_traits/nat.h:25:3: note: '~__nat' has been explicitly marked deleted here  
   25 |   ~__nat()                       = delete;  
```  
  
has anything changed related to `async_connect` from boost 1.86 to 1.87?

---

## Comment 1

> Username: sehe  
> Created at: 2025-03-27 23:45:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2997#issuecomment-2759823399  

The notable difference is that `deferred_t` [became the default completion token in Asio 1.31.0](https://www.boost.org/doc/libs/1_87_0/doc/html/boost_asio/history.html#boost_asio.history.asio_1_31_0), which implicates a few SFINAE overloads. There have been other conflicts where the overload sets became ambiguous for previously compiling call sites, IIRC.  
  
Especially this change bullet seems related:  
  
> - Added the is_connect_condition trait and used it disambiguate overloads of async_connect when the completion token is defaulted.

---

## Comment 2

> Username: a-soll  
> Created at: 2025-03-28 00:46:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2997#issuecomment-2759899765  

Is there anything I can do to resolve? I don't really know if downgrading to 1.86 is feasible with the way I'm building, but this is a big blocker.

---

## Comment 3

> Username: a-soll  
> Created at: 2025-03-28 01:02:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2997#issuecomment-2759914296  

```  
/Users/adam/Library/Caches/CocoaPods/Pods/Release/boost-iosx/1.87.0.4-9f252/frameworks/Headers/boost/bind.hpp:36:1: warning: The practice of declaring the Bind placeholders (_1, _2, ...) in the global namespace is deprecated. Please use <boost/bind/bind.hpp> + using namespace boost::placeholders, or define BOOST_BIND_GLOBAL_PLACEHOLDERS to retain the current behavior. [-W#pragma-messages]  
   36 | BOOST_PRAGMA_MESSAGE(  
      | ^  
/Users/adam/Library/Caches/CocoaPods/Pods/Release/boost-iosx/1.87.0.4-9f252/frameworks/Headers/boost/config/pragma_message.hpp:24:34: note: expanded from macro 'BOOST_PRAGMA_MESSAGE'  
   24 | # define BOOST_PRAGMA_MESSAGE(x) _Pragma(BOOST_STRINGIZE(message(x)))  
      |                                  ^  
<scratch space>:36:2: note: expanded from here  
   36 |  message("The practice of declaring the Bind placeholders (_1, _2, ...) " "in the global namespace is deprecated. Please use " "<boost/bind/bind.hpp> + using namespace boost::placeholders, " "or define BOOST_BIND_GLOBAL_PLACEHOLDERS to retain the current behavior.")  
```  
I get this warning as well, not sure if this is related and not sure what it wants me to do as I'm not including those headers directly.

---

## Comment 4

> Username: ashtum  
> Created at: 2025-03-28 07:01:58 UTC  
> Updated at: 2025-03-28 07:14:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2997#issuecomment-2760393153  

@a-soll Could you provide a reproducible example? The signature of `_on_connect` and the type of `results` are unclear.

---

## Comment 5

> Username: a-soll  
> Created at: 2025-03-28 14:25:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2997#issuecomment-2761518728  

hey, here are more details about the code:  
  
```cpp  
void client::_on_connected(boost::system::error_code const &error) {  
    if (error) {  
        fail(error, "on_connect");  
        return;  
    }  
    this->_identify();  
    this->_await_new_line(error);  
}  
  
void client::_await_new_line(beast::error_code ec) {  
    if (ec) {  
        fail(ec, "await_new_line");  
        this->_connect();  
        return;  
    }  
    asio::async_read_until(  
        this->_socket,  
        this->_in_buf,  
        "\r\n",  
        beast::bind_front_handler(&client::_on_read, shared_from_this())  
    );  
}  
```  
  
```cpp  
void client::_on_hostname_resolved(  
    boost::system::error_code const &error,  
    tcp::resolver::results_type results  
) {  
    if (error) {  
        fail(error, "on_resolve");  
        // this->_connect();  
        return;  
    }  
    if (!results.size()) {  
        std::stringstream msg;  
        msg << "Failed to resolve '" << this->_host << "'";  
        throw std::runtime_error(msg.str());  
    }  
  
    this->_socket.async_connect(  
        *results, beast::bind_front_handler(&client::_on_connected, shared_from_this())  
    );  
}  
```  
  
I did end up having to revert to 1.86 which is why this code doesn't use the `beast::get_lowest_layer()` call and instead calls `_socket.async_connect()`, but neither will compile for me with boost 1.87. I did isolate the problem to specifically the `async_connect` function call though.

---

## Comment 6

> Username: ashtum  
> Created at: 2025-03-28 15:05:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2997#issuecomment-2761638548  

Looks like this is because of the new changes in Asio, dereferencing `results` directly is not possible anymore: https://github.com/chriskohlhoff/asio/commit/e916bdfb1ab64d68ba556e0040a3e86a747b74a7  
  
If you change your code to the following, it will compile without issues:  
```C++  
beast::get_lowest_layer(stream_).async_connect(  
    *results.begin(),  
    beast::bind_front_handler(  
        &session::on_connect,  
        shared_from_this()));  
```  
By the way, this attempts to connect to the first endpoint returned by the resolver, if that’s what you intended.

---

## Comment 7

> Username: a-soll  
> Created at: 2025-03-28 15:25:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2997#issuecomment-2761688458  

Just to be clear, in the 1.87 snippet where I did:  
```cpp  
 beast::get_lowest_layer(this->_socket)  
.async_connect(  
    results, beast::bind_front_handler(&client::_on_connected, shared_from_this())  
);  
```  
would you expect that to have an issue even though it's not dereferencing? or is the `*results.begin()` code you just posted the direct 1:1 with how I'm currently doing it in 1.86? it may take some time for me to be able to verify.

---

## Comment 8

> Username: ashtum  
> Created at: 2025-03-28 15:29:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2997#issuecomment-2761698413  

> Just to be clear, in the 1.87 snippet where I did:  
>   
>  beast::get_lowest_layer(this->_socket)  
> .async_connect(  
>     results, beast::bind_front_handler(&client::_on_connected, shared_from_this())  
> );  
> would you expect that to have an issue even though it's not dereferencing? or is the `*results.begin()` code you just posted the direct 1:1 with how I'm currently doing it in 1.86? it may take some time for me to be able to verify.  
  
Passing `results‍‍‍‍‍‍‍` works fine. Passing `*results‍‍‍‍‍‍‍` is not possible in Boost 1.87. You can replace it with `*results.begin()`.
