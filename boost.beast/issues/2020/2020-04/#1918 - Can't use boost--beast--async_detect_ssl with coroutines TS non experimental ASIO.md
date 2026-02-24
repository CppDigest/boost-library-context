# #1918 - Can't use boost::beast::async_detect_ssl with coroutines TS non experimental ASIO [Closed]

> Username: do-m-en  
> Created at: 2020-04-26 16:04:43 UTC  
> Updated at: 2020-04-28 18:56:01 UTC  
> Closed at: 2020-04-28 18:56:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1918  

Beast built from f5064e0c460664fa34fdad44e2379a2309d39685 (Beast version 290).  
  
With experimental Boost.asio coroutines TS support I used the function like this:  
```  
  auto token = co_await this_coro::token();  
  boost::system::error_code ec;  
  
  // This buffer is required to persist across reads  
  boost::beast::flat_buffer buffer;  
  
  using boost::asio::experimental::redirect_error;  
  
  boost::logic::tribool result =  
    co_await  
      boost::beast::async_detect_ssl(  
        socket,  
        buffer,  
        redirect_error(token, ec));  
```  
Now that Boost.asio (from Boost version 1.71 on) moved experimental implementation to finalized version I expected the function to be called like this:  
```  
bool result = // perhaps still tribool?  
    co_await  
      boost::beast::async_detect_ssl(  
        socket,  
        buffer,  
        boost::asio::use_awaitable);  
```  
but this fails to compile.  
  
Am I doing something wrong/what would be a temporary workaround in case this requires substantial code changes?  
  
Thanks.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-04-26 16:08:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1918#issuecomment-619576279  

What is the output of the compiler?

---

## Comment 2

> Username: do-m-en  
> Created at: 2020-04-26 16:12:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1918#issuecomment-619577124  

```  
In file included from ... /include/boost/asio/co_spawn.hpp:98:  
In file included from ... /include/boost/asio/impl/co_spawn.hpp:22:  
In file included from ... /include/boost/asio/use_awaitable.hpp:108:  
... /include/boost/asio/impl/use_awaitable.hpp:260:9: error: no matching function for call to object of type 'typename remove_reference<run_detect_ssl_op &>::type' (aka 'boost::beast::detail::run_detect_ssl_op')  
        std::move(initiation)(std::move(handler), std::move(args)...);  
        ^~~~~~~~~~~~~~~~~~~~~  
... /include/boost/asio/async_result.hpp:365:17: note: in instantiation of function template specialization 'boost::asio::async_result<boost::asio::use_awaitable_t<boost::asio::executor>, void (boost::system::error_code, bool)>::initiate<boost::beast::detail::run_detect_ssl_op, boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::executor> *, boost::beast::basic_flat_buffer<std::__1::allocator<char> > >' requested here  
    Signature>::initiate(BOOST_ASIO_MOVE_CAST(Initiation)(initiation),  
                ^  
... /include/boost/beast/core/detect_ssl.hpp:436:17: note: in instantiation of function template specialization 'boost::asio::async_initiate<const boost::asio::use_awaitable_t<boost::asio::executor> &, void (boost::system::error_code, bool), boost::beast::detail::run_detect_ssl_op, boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::executor> *, boost::beast::basic_flat_buffer<std::__1::allocator<char> > &>' requested here  
    return net::async_initiate<  
                ^  
... /session_type_detect.hpp:34:21: note: in instantiation of function template specialization 'boost::beast::async_detect_ssl<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::executor>, boost::beast::basic_flat_buffer<std::__1::allocator<char> >, const boost::asio::use_awaitable_t<boost::asio::executor> &>' requested here  
      boost::beast::async_detect_ssl(  
                    ^  
... /include/boost/beast/core/detect_ssl.hpp:374:10: note: candidate function [with DetectHandler = boost::asio::detail::awaitable_handler<boost::asio::executor, boost::system::error_code, bool>, AsyncReadStream = boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::executor>, DynamicBuffer = boost::beast::basic_flat_buffer<std::__1::allocator<char> >] not viable: expects an l-value for 3rd argument  
    void operator()(  
         ^  
1 error generated.  
```

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-04-26 16:19:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1918#issuecomment-619578575  

This is probably a bug in Beast, on this line:  
https://github.com/boostorg/beast/blob/f5064e0c460664fa34fdad44e2379a2309d39685/include/boost/beast/core/detect_ssl.hpp#L375  
  
If correct, we need to audit all internal calls to `net::async_initiate`.

---

## Comment 4

> Username: madmongo1  
> Created at: 2020-04-27 08:09:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1918#issuecomment-619810586  

I'll take a look

---

## Comment 5

> Username: madmongo1  
> Created at: 2020-04-27 12:17:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1918#issuecomment-619949206  

ok, I've found the bug.   
  
Internally ASIO expects mutable references to be passed as pointers. Since it expects this, the specialisation of `async_result<use_awaitable<Executor>, R(Args...)>`  uses `std::move` to move any passed arguments, rather than `std::forward`.  
  
The problem is not with our handler detection, it's actually because Beast's DynamicBuffers are passed by mutable reference, and we don't convert those references into pointers in the initiating function.  
  
This is _probably_ a bug in asio, since `std::forward<>` would work for all cases.  
  
Here's the code causing the break:  
  
```  
  template <typename Initiation, typename... InitArgs>  
  static return_type initiate(Initiation initiation,  
      use_awaitable_t<Executor>, InitArgs... args)  
  {  
    co_await [&](auto* frame)  
      {  
        handler_type handler(frame->detach_thread());  
        std::move(initiation)(std::move(handler), std::move(args)...);   // *** HERE ***  
        return static_cast<handler_type*>(nullptr);  
      };  
  
    for (;;) {} // Never reached.  
#if defined(_MSC_VER)  
    co_return dummy_return<typename return_type::value_type>();  
#endif // defined(_MSC_VER)  
  }  
```  
  
The fix is for us to convert references to DynamicBuffer_v0 buffers to pointers in our initiating function.

---

## Comment 6

> Username: madmongo1  
> Created at: 2020-04-27 12:22:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1918#issuecomment-619952070  

re workarounds, I'll have a think once I've fixed the bug.

---

## Comment 7

> Username: madmongo1  
> Created at: 2020-04-28 05:47:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1918#issuecomment-620394548  

@do-m-en I have a fix here:  
https://github.com/madmongo1/beast/commit/c8bf6fd98b54bd0e1df2f9c39f113b0101188c40  
  
If it's urgent, you can apply this as a patch to your source immediately. Otherwise I expect to be given approval to merge to develop shortly.
