# #2727 - asio::deferred doesn't work with BOOST_ASIO_INITFN_RESULT_TYPE [Closed]

> Username: ashtum  
> Created at: 2023-08-21 09:16:13 UTC  
> Updated at: 2023-12-19 15:25:15 UTC  
> Closed at: 2023-09-05 05:24:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2727  

It seems `BOOST_ASIO_INITFN_RESULT_TYPE` needs `asio::async_result<deferred_t, Signature>::return_type` which is not defined for deferred operations.  
  
```BASH  
candidate template ignored: substitution failure [with EndpointSequence = boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>, RangeConnectHandler = const boost::asio::deferred_t &, $2 = void]: no type named 'return_type' in 'boost::asio::async_result<boost::asio::deferred_t, void (boost::system::error_code, boost::asio::ip::basic_endpoint<boost::asio::ip::tcp>)>'  
```  
  
Minimal reproducible example: https://godbolt.org/z/f9h1cn7r6  
```C++  
#include <boost/asio.hpp>  
#include <boost/beast.hpp>  
  
namespace asio  = boost::asio;  
namespace beast = boost::beast;  
  
asio::awaitable<void> async_main()  
{  
  auto stream   = beast::tcp_stream{ co_await asio::this_coro::executor };  
  auto resolver = asio::ip::tcp::resolver{ co_await asio::this_coro::executor };  
  
  auto results = co_await resolver.async_resolve("www.example.com", "80", asio::deferred);  
  
  // error: no matching member function for call to 'async_connect':  
  co_await stream.async_connect(results, asio::deferred);  
  
  // this compiles:  
  // co_await stream.async_connect(results, asio::use_awaitable);  
}  
```

---

## Comment 1

> Username: ashtum  
> Created at: 2023-08-21 09:49:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2727#issuecomment-1686008622  

@klemens-morgenstern Should we replace `BOOST_ASIO_INITFN_RESULT_TYPE` usages with `BOOST_ASIO_INITFN_AUTO_RESULT_TYPE`? this at least fixes the issue in C++14 and upwards.

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2023-08-21 10:06:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2727#issuecomment-1686035338  

I think this is basically the same, except that it's just auto for C++ >= 14. So I think  yes.

---

## Comment 3

> Username: ashtum  
> Created at: 2023-08-21 10:09:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2727#issuecomment-1686040293  

Is there any possibility of fixing it for C++11 as well?

---

## Comment 4

> Username: anarthal  
> Created at: 2023-12-19 15:25:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2727#issuecomment-1862977611  

(FYI) I just found this same issue in MySQL. The way I've solved it (mimicking Asio) is moving away from the macros to explicit `decltypes`. This makes `deferred` work in C++11, too (which was a requirement in my case, since it's useful for `parallel_group`).
