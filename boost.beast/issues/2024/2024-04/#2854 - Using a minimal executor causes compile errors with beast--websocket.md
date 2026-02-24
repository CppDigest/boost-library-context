# #2854 - Using a minimal executor causes compile errors with beast::websocket [Closed]

> Username: bgemmill  
> Created at: 2024-04-16 01:26:57 UTC  
> Updated at: 2024-06-27 12:11:34 UTC  
> Closed at: 2024-06-27 12:11:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2854  

Beast 351 from boost 1.84  
  
For some background, I'm trying to use boost::beast with my own executor, based on the minimal example [here](https://www.boost.org/doc/libs/1_85_0/doc/html/boost_asio/std_executors.html).  
  
However, it seems like something inside beast's websocket is doing something funny with the executor's require interface, and leading to compile errors.  
  
For example, imagine a simple executor that's merely a forwarder to `asio::io_context`:  
```  
  struct executor {  
    boost::asio::io_context* context_{nullptr};  
  
    executor& operator=(const executor& other) = default;  
    executor& operator=(executor&& other) = default;  
  
    bool operator==(const executor& other) const noexcept {  
      return context_ == other.context_;  
    }  
  
    bool operator!=(const executor& other) const noexcept {  
      return !(*this == other);  
    }  
  
    boost::asio::execution_context& query(boost::asio::execution::context_t) const noexcept {  
      return *context_;  
    }  
  
    static constexpr boost::asio::execution::blocking_t::never_t query(boost::asio::execution::blocking_t) noexcept {  
      return boost::asio::execution::blocking_t::never;  
    }  
  
    constexpr executor require(boost::asio::execution::blocking_t::never_t) const {  
      return *this;   
    }  
  
    template<class F>  
    void execute(F&& f) const {  
      context_->get_executor().execute(std::forward<F>(f));  
    }  
  };  
```  
  
Creating sockets with this executor works just fine:  
```  
using socket_t = boost::asio::basic_stream_socket<boost::asio::ip::tcp, common::base::executor>;  
...  
auto p = std::make_shared<socket_t>(executor{&ioc_from_somewhere});  
```  
  
But creating websockets leads to compile errors (for example, in handshake):  
```  
using websocket_t = boost::beast::websocket::stream<socket_t, true>;  
...  
auto p = std::make_shared<websocket_t>(executor{&ioc_from_somewhere});  
p->async_handshake("host", "target", [](boost::system::error_code ec){});  
  
...compile....  
  
boost/asio/associated_immediate_executor.hpp:57:12: error: binding reference of type 'executor' to value of type 'const executor' drops 'const' qualifier  
   57 |     return boost::asio::require(e, execution::blocking.never);  
      |            ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
```  
  
The require api is admittedly super clunky, so it's not at all clear what else the executor needs to make beast happy.

---

## Comment 1

> Username: ashtum  
> Created at: 2024-04-16 10:23:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2854#issuecomment-2058751466  

The issue is reproducible with the following too:  
https://godbolt.org/z/5cosahG6M  
```C++  
asio::io_context ioc;  
auto e = asio::get_associated_immediate_executor([] {}, executor{ &ioc });  
```  
It seems we need to check for the existence of the associated immediate executor before attempting to use it in a dispatch operation. There is a possibility that this might be a bug in Asio as well, which I am currently investigating.

---

## Comment 2

> Username: bgemmill  
> Created at: 2024-04-16 13:36:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2854#issuecomment-2059112984  

Thanks for having a look at this!

---

## Comment 3

> Username: ashtum  
> Created at: 2024-04-16 17:58:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2854#issuecomment-2059653756  

This seems to be a bug in Asio. I've made a pull request for it: https://github.com/chriskohlhoff/asio/pull/1465  
As a workaround you might consider commenting out the static `query(asio::execution::blocking_t)` member function:  
```C++  
// static constexpr asio::execution::blocking_t::never_t query(asio::execution::blocking_t) noexcept  
// {  
//   return asio::execution::blocking.never;  
// }  
```

---

## Comment 4

> Username: ashtum  
> Created at: 2024-06-27 12:11:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2854#issuecomment-2194523835  

Addressed in https://github.com/chriskohlhoff/asio/commit/e5cbafb261d73060690c5befa4723db11716a02f,
