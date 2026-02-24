# #2867 - stream.async_connect() ambiguous overload with asio::as_tuple(asio::use_awaitable) [Closed]

> Username: Rhidian12  
> Created at: 2024-05-15 09:33:29 UTC  
> Updated at: 2024-06-27 13:03:11 UTC  
> Closed at: 2024-06-27 13:03:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2867  

### Version of Beast  
  
Boost: 1.83, Beast: 347  
  
### Steps necessary to reproduce the problem  
  
I encountered this problem at work while using Beast to write a simple http client.  
I;m trying to get the error code as a return value from `stream.async_connect()`, but I kept getting errors about ambigious overloads.  
After some investigation, I discovered that `asio::as_tuple(asio::use_awaitable)` is valid for both `RangeConnectHandler` and `ConnectCondition`.  
The overload with `ConnectCondition` also defaults its `RangeConnectHandler` argument, causing this compiler error.  
Is this a bug, or is this the actual intended behaviour?  
  
```  
boost::asio::awaitable<void> PostRequestImplementation(std::string const host, std::string const port, std::string const target)  
{  
  using namespace boost;  
  namespace ip   = asio::ip;  
  namespace http = beast::http;  
  
  auto resolver = asio::use_awaitable.as_default_on(ip::tcp::resolver(co_await asio::this_coro::executor));  
  auto stream   = asio::use_awaitable.as_default_on(beast::tcp_stream(co_await asio::this_coro::executor));  
  
  auto [__, results] = co_await resolver.async_resolve(host, port, asio::as_tuple(asio::use_awaitable));  
  
  auto ec = co_await stream.async_connect(results, asio::as_tuple(asio::use_awaitable));  
  // ERROR: Call to member function 'async_connect' is ambiguous  
  // basic_stream.hpp(1028, 5): Candidate function [with EndpointSequence =   
  // boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>, RangeConnectHandler =   
  // boost::asio::as_tuple_t<boost::asio::use_awaitable_t<>>, $2 = void]  
   
  // basic_stream.hpp(1132, 5): Candidate function [with EndpointSequence =   
  // boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>, ConnectCondition =   
  // boost::asio::as_tuple_t<boost::asio::use_awaitable_t<>>, RangeConnectHandler = boost::asio::use_awaitable_t<>, $3 = void]  
  
  // Below code does work, but is a bit annoying  
  // auto [ec, _] = co_await stream.async_connect(  
  // results,  
  // [](boost::system::error_code const &, ip::tcp::endpoint const &) { return true; },  
  // asio::as_tuple(asio::use_awaitable));  
  
  co_return;  
}  
```  
  
### All relevant compiler information  
  
Compiled using gcc (Debian 12.2.0-14) 12.2.0 with C++20 and /W4

---

## Comment 1

> Username: ashtum  
> Created at: 2024-05-15 10:20:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2867#issuecomment-2112128768  

The issue arises from using an executor with a default completion token in this line:  
  
```C++  
auto stream = asio::use_awaitable.as_default_on(beast::tcp_stream(co_await asio::this_coro::executor));  
```  
  
This adds a default value for the last parameter (the completion token), resulting in ambiguous calls because the first parameters like `EndpointSequence` and `ConnectCondition` aren't constrained. If you change `stream` to:  
  
```C++  
auto stream = beast::tcp_stream(co_await asio::this_coro::executor);  
```  
  
the issue goes away. I'll investigate if there's a way to constrain these template parameters and fix the problem.

---

## Comment 2

> Username: Rhidian12  
> Created at: 2024-05-15 11:21:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2867#issuecomment-2112259160  

Aha, that explains a lot, I was following one of the examples, which made them like this.  
Thanks a lot for the quick help! You can close this issue then if you want :)

---

## Comment 3

> Username: ashtum  
> Created at: 2024-05-16 08:51:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2867#issuecomment-2114555433  

Unfortunately, there is no type trait for constraining `ConnectCondition` in Asio. Indeed, the issue also exists in Asio: https://godbolt.org/z/aqxns58n5.  
  
I've raised the issue in the Asio repository: https://github.com/chriskohlhoff/asio/issues/1476.
